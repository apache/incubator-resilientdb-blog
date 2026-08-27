---
layout: article
title: "Implementing Raft Consensus Protocol for ResilientDB"
author: jhutton
tags: ResilientDB Raft Consensus Protocol
aside:
    toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    opacity: 0.2
    src:  /assets/images/resdb-gettingstarted/code_close_up.jpeg

---

Distributed systems are powerful and prevalent tools in today's computing landscape. Properly coordinating these systems requires some mechanism to do so, that being a consensus protocol. Because many consensus protocols exist to target different use cases, it is beneficial for a distributed database to support different protocols. While ResilientDB already contains multiple Byzantine Fault Tolerant algorithms, it has not contained a Crash Fault Tolerant algorithm until now. ResilientDB's Raft implementation fills this gap, allowing for a more favorable asymptotic message complexity compared to Byzantine Fault Tolerant protocols at the cost of no longer tolerating malicious behavior.

# Background 

## Consensus 

Oftentimes, it can be beneficial to leverage the power of multiple computers in multiple locations to solve a computational problem. This may be useful to enable lower latency for users in different locations, handle a higher load, and allow for operations to continue even if individual machines stop working. However, there is one large problem with computation on a distributed system: how do the machines agree on which data values to use during computation? This is the problem of consensus in distributed systems.

Solving this problem is non-trivial. The [CAP theorem](https://dl.acm.org/doi/10.1145/564585.564601) shows that no matter what algorithm or design is used, if messages are allowed to be arbitrarily delayed or dropped, it cannot be guaranteed that all clients will receive the same response to the same request. Tradeoffs have to be made based on the needs of the system and the users. 

## Fault Tolerance

One consideration when designing a consensus protocol is the kinds of faults that are tolerated in the system. Crash Fault Tolerant (CFT) protocols tolerate machines crashing or stopping. Byzantine Fault Tolerant (BFT) protocols allow for faults as well as malicious (Byzantine) behavior. A Byzantine machine may lie or actively act against the goal of the group. If a cluster has `f` faulty nodes, a CFT algorithm must have at least `2f + 1` nodes in total (less than half of the nodes can be faulty). A BFT algorithm must have at least `3f + 1` nodes in total (less than one third of the nodes can be faulty or Byzantine). One well-known BFT algorithm is Practical Byzantine Fault Tolerance (PBFT).


## What is Raft?

Raft is a consensus protocol used in many existing software systems, including etcd (used by Kubernetes), Kafka, and MongoDB. It has been battle-tested in production since it was first published in 2014. The ResilientDB project on GitHub has had an open issue since October 2023 requesting that Raft be added to its portfolio of available consensus protocols. This Raft implementation aims to fulfill this feature request.

Raft is the first CFT protocol available within ResilientDB. This comes with several benefits: a ResilientDB Raft deployment can function with only 3 replicas instead of the current minimum of 4, and transaction throughput should, in theory, degrade less sharply than PBFT as the number of replicas increases, thanks to Raft's more favorable asymptotic message complexity (O(n)) compared to the PBFT family of protocols (O(n<sup>2</sup>)). The performance overhead of cryptography in BFT protocols is also fairly high, so latency improvements can be expected with Raft as well.


<p style="text-align:center;">
    <img src="/assets/images/raft/Raft.gif" alt="Raft Protocol Diagram"/>
    <br>
    <em>Figure 1. Animation of the Flow of the Raft Consensus Protocol
    </em>
    <br>
    <strong> <em>c</em>: client | <em>T</em>: Transaction | <em>L</em>: leader | <em>F</em>: follower </strong>
</p>

In Raft, a leader receives transactions from the client, adds them to its log, and forwards them to followers in an AppendEntries Remote Procedure Call (RPC). The followers execute that procedure to add the entry to their log, and respond to the leader to inform them if the RPC succeeded or failed. The leader waits until it has reached quorum on that transaction, requiring responses from at least `f + 1` nodes, before committing the transaction to be executed. Then the client is informed, and in future AppendEntries RPCs, the commit index is updated so followers know to commit and execute the transaction.

## Raft Leader Elections

In Raft, leaders are supposed to send out heartbeats periodically to maintain their leadership status. All followers maintain a timeout window, and randomly pick a time to wait within that window. Without the follower receiving an AppendEntries RPC from the leader within the time window, it is assumed that the leader is no longer available. The follower will transition to a candidate, increment its term, and send out RequestVote RPCs to all other computers. A computer will respond with a yes vote if:

1. The replica has not already voted for another computer this term
2. The candidate's term and log are at least as up to date as the replica's own.

Replicas decide if a follower's log is more recent based upon the index and the term of the last entry in their logs. If the terms of the last entries are different, then the more up-to-date log is the one with the most recent term. If the terms of the last entries are the same, then the more up-to-date log is the one with the highest last log index.

Because at least `f + 1` nodes are required to win an election, and entries are committed once `f + 1` machines have replicated them, a node can only be elected as leader if its log contains every committed entry.

# Initial Raft Implementation

## Implementation Strategy and Architecture

<p style="text-align:center;">
    <img src="/assets/images/raft/Architecture1.svg" alt="How Raft fits into ResilientDB"/>
    <br>
    <em>Figure 2. How Raft Fits into ResilientDB
    </em>
</p>

Raft needs to fit into ResilientDB's existing codebase. It interacts with the TransactionExecutor to execute transactions and add them to the database, the ReplicaCommunicator for replica messaging, and the Recovery class. Raft also will have access to built-in benchmarking support through PerformanceManager, to easily measure throughput and latency.

<p style="text-align:center;">
    <img src="/assets/images/raft/Architecture2.svg" alt="Raft Architecture"/>
    <br>
    <em>Figure 3. Raft Architecture
    </em>
</p>

The Raft implementation consists of several core components. The consensus object receives messages from the ReplicaCommunicator, and depending on what type of message it is, calls the appropriate Raft function to handle it. Messages can be transactions from a client, or RPCs and RPC responses. These RPCs include AppendEntries, RequestVote, and InstallSnapshot. Raft requires certain data to be durably stored on disk, necessitating the use of RaftRecovery. Specific details on RaftRecovery and the InstallSnapshot RPC are discussed in [Persistence](#persistence) and [Snapshots](#snapshots).

The Raft object has the logic to send heartbeats and start leader elections, but it needs these events to be triggered at appropriate times. This is handled by the LeaderElectionManager.

The LeaderElectionManager operates in two different modes, leader mode and follower mode. The LeaderElectionManager keeps a timer to invoke certain functions when the timer has elapsed. For leaders, the Raft object tells the LeaderElectionManager every time a message is broadcast to all replicas, which refreshes the duration of the timer. If the LeaderElectionManager times out for a leader, then it forces a heartbeat to be sent.

For followers, the Raft object tells the LeaderElectionManager every time an RPC is received, which refreshes the duration of the timer. If the LeaderElectionManager times out for a follower, then it forces the follower to transition to a candidate and start an election, requesting votes from all other replicas.


### Leader In-Flight Message Throttling

The network can become overwhelmed if too many messages are sent unnecessarily. To help mitigate this, there are multiple ways to limit the number of messages sent at once. The first is a cap on message size and the number of entries in a single message. Once that cap would be exceeded, the current message is sent as-is and the remaining entries are sent in one or more additional messages. Additionally, there is a limit on the number of messages allowed to be in flight (from the **leader** to **followers**), meaning sent but not yet acknowledged by a follower. Once that limit is reached, no messages other than heartbeats are sent to that follower until it responds or a timeout is reached.

If a timeout occurs, all in-flight messages are presumed lost, and the follower's next index is reset to its match index (the index of the last entry the follower is known to have).

# Follow-up Raft Improvements

The initial implementation included basic log replication, heartbeats, timeouts, and leader elections. However, it was missing several key features described in [Diego Ongaro and John Ousterhout's Raft Paper](https://raft.github.io/raft.pdf). The main omissions were persisting each server's state to disk, and the ability to create and send snapshots to followers that have fallen behind.

In addition to these required features, the implementation had no tests, and the initial benchmarking was performed on a single local machine. After testing on multiple remote machines, throughput issues surfaced, which are addressed in [Throughput Improvements](#throughput-improvements).

## Persistence

To guarantee the safety of the protocol, Raft requires certain information to be persisted to disk on stable storage before responding to RPCs. This includes the log entries themselves, as well as some extra metadata. ResilientDB's Recovery class was expanded to support Raft's needs while maintaining existing behavior for PBFT.

Since the log information that needs to be stored differs between Raft and PBFT, the Recovery class was split into a RecoveryBase and two derived classes, PBFTRecovery and RaftRecovery. Most of the required work was the same, but since different data needs to be stored, different function arguments need to be used. This led to the use of the Curiously Recurring Template Pattern (CRTP), which is well suited to this case since the object type is known at compile time. The base class takes the derived class, as well as the argument types of some functions, as template parameters. This lets the code that reads and writes the individual elements of the Write-Ahead Log (WAL) live entirely in the base class, while the derived classes define which elements are written and in what order.

The extra metadata is stored separately from RecoveryBase's WAL in RaftRecovery's own `metadata.dat` file. This metadata includes the current term, the replica that was voted for in the current term (if any), and the index and term of the last log entry included in any snapshot. Unlike a log, this data doesn't need to retain history. It only needs to reflect the current state, so it can be overwritten any time it changes. The data is first written to a temporary file, then renamed to `metadata.dat`.

## Snapshots

In a Raft instance, since a follower can be arbitrarily far behind the leader, the entire state of the log must be kept if no precautions have been taken. Without some way to compact this information, the log can grow indefinitely. This problem can be solved by using snapshots to enable log compaction. Snapshots also speed up catching up a follower that has fallen far behind the leader's log.

<p style="text-align:center;">
    <img src="/assets/images/raft/RaftSnapshotFlow.svg" alt="Raft Snapshot Flow"/>
    <br>
    <em>Figure 4. Raft Snapshot Flow
    </em>
</p>

In Raft, snapshots contain the current state of the state machine as is, without retaining all of the instructions to get to that point. For ResilientDB, this state is effectively just the database already being used (such as LevelDB). To decide when a checkpoint is to be taken, ResilientDB uses a timer as well as a "checkpoint sequence." Every 60 seconds (a customizable amount), a thread checks to see if the checkpoint sequence has been updated. This checkpoint sequence is updated after every 1,000 transactions executed, based on a function callback in TransactionExecutor. When the check occurs, if the checkpoint sequence is different from the cached value from the last time it was checked, that triggers the database to be flushed to disk, and the WAL rotates to a new file. Then, the Raft class is notified and truncates the prefix of the log corresponding to that snapshot. Rather than immediately removing every snapshotted entry from the log, a configurable buffer keeps some of them in place.

When a leader knows a follower has fallen behind, it queues a snapshot for that follower. A separate snapshot queue thread reads the queue and sends the InstallSnapshot RPC out. Each snapshot is serialized to a byte string, stored to disk, and then sent to the follower in chunks. The leader waits for the follower to receive and respond to each chunk before sending the next.

During the period between a follower being sent its first chunk of a snapshot and the leader receiving confirmation that the follower has received the last chunk of the snapshot, the leader does not initiate new checkpoints, which pauses prefix truncation until the snapshot completes. This prevents a follower from finishing one snapshot only to immediately need another, because the entry it needs next has already been truncated from the log.

## NO-OP Entries

As part of Raft's safety guarantees, a leader can only directly commit entries from its own term. The specification for Raft addresses this by having the leader submit a blank no-operation (NO-OP) entry at the start of its term. While this was originally described as a step toward supporting read-only transactions, it also solved a specific issue in ResilientDB's testing harness, where the client can only have a finite number of transactions in flight that have not yet been committed. If all of these transactions have been sent out and a leader election then occurs, no new transactions would be added to the log or committed. Without a NO-OP entry at the start of each term, this would cause progress to stall indefinitely.

## Raft Transaction Batching

Another mechanism that was tested to try to address network congestion was to have the leader batch transactions before sending them to followers. When batching is enabled, the leader waits until a set time threshold elapses before sending newly received transactions to followers. In practice, this only helped when transaction bandwidth was not already saturated, so it is currently disabled.

## Tests

Around 100 test cases were added for the Raft implementation. This includes tests for Raft and RaftRecovery, as well as for existing components touched by the new Recovery class, such as MemoryDB and LevelDB. Most of the tests are unit tests written for the sending and receiving of each RPC (AppendEntries, RequestVote, and InstallSnapshot) as well as their responses. Additionally, there are some integration tests to verify how Raft and RaftRecovery work together. These tests write to the WAL and metadata file, let the Raft object go out of scope, and then verify that data is restored properly.

## Throughput Improvements

Benchmarking began after the initial implementation was fully complete. However, throughput was significantly lower than expected. The following changes address this:

### Follower State

Sending a single message at a time and waiting for a response before sending the next is far too slow, so pipelining was added to achieve reasonable throughput. The leader continues sending new transactions as they arrive, without waiting for acknowledgment of previous messages. However, this can overwhelm the network, and the problem compounds whenever a follower fails to add entries to its log for any reason. For example, a single message received out of order causes all subsequent messages to be rejected until the missing entry is resent. The in-flight limits discussed previously mitigate this somewhat, but not completely.

<p style="text-align:center;">
    <img src="/assets/images/raft/FollowerState.svg" alt="Raft Follower State" style="display:block; margin:0 auto; max-width:100%; height:auto;"/>
    <br>
    <em>Figure 5. Follower State
    </em>
</p>

To help solve this, the design borrows an approach inspired by the follower progress tracking in [etcd's Raft implementation](https://github.com/etcd-io/raft). For each follower, the leader tracks which of three states it is in: `REPLICATE`, `PROBE`, or `SNAPSHOT`. While a follower is in the `REPLICATE` state, the leader continues to forward that follower new log entries up to the in-flight limit, assuming everything is being received correctly. If the leader gets a failure response, it sets the follower's state to `PROBE`, assumes all in-flight messages were lost, and waits for a response before trying to catch the follower up again. No messages other than heartbeats are sent to a follower while it is in the `PROBE` state. Once the follower responds, the leader does one of two things: if the missing entry has not been truncated, it sets the follower's state back to `REPLICATE` and sends the missing entry. If the entry has been truncated, it sets the state to `SNAPSHOT` and queues a snapshot for that follower.

This lets the leader use pipelined log replication normally when there are no issues, while avoiding overwhelming the network once a failure occurs. Once the leader has confirmed the follower's log state, it can resume normal operations.

### Asynchronous WAL Writing

Because the log must be persisted to disk before responding to an AppendEntries RPC, the WAL needs to be flushed to disk regularly, which can be expensive. Initially, writes were done every time a leader received a transaction from a client, or every time a follower received an AppendEntries RPC with one or more log entries. This proved prohibitively expensive, especially with multiple threads receiving transactions.

A separate `WalWriterLoop` thread solves this by batching records and performing one `fsync` per batch. This request to add a log entry to the WAL returns a `std::future` that the Raft implementation waits on before responding to an AppendEntries RPC. When a leader receives a transaction from the client, it will add it to its log and then forward the transaction to followers immediately. The `std::future` it receives from RaftRecovery will be waited on after forwarding. Logically, the transaction is only required to be persisted to disk before committing it. Instead, the `std::future` is checked after the messages have been sent to the ReplicaCommunicator, which is sufficient to hide the latency from the required disk I/O.

### Fast Log Backtracking

If a follower cannot accept a leader's AppendEntries RPC, one option is for the leader to decrement its index one entry at a time and try again. Unfortunately, this is impractical with pipelining, since the point where a message was dropped or received out of order may be many thousands of log entries back during periods of high bandwidth. To address this, the Raft paper describes an optional optimization that lets the leader and follower back up one term at a time instead of one entry at a time, allowing for much faster backtracking to the correct log index at which to resume replication. This accelerated log backtracking, while not very well specified in the original Raft paper, can be found in more detail in [Jon Gjengset's Students' Guide to Raft](https://thesquareplanet.com/blog/students-guide-to-raft/#an-aside-on-optimizations).

In Raft, when a leader sends an AppendEntries RPC, it includes the index and term of the entry directly preceding the first log entry sent in the RPC. For example, if the log only contains entries from term 1, when the leader sends entries beginning at index 6, the previous log index is 5, and the previous log term is 1. If the follower contains an entry at that index at that term, it is guaranteed the follower's log is identical to the leader's up until that point.

If the follower's log contains an entry at that index from a different term, the follower must reject that message and reply with the first log index of that term (the one in its log). For example, say that a follower's log only contains entries from term 1, and it receives an AppendEntries RPC with a previous log index of 5 and previous log term of 2. At index 5 in the follower's log, the follower has a conflicting term of 1. So, it will reverse through its log and return the first index from the conflicting term, which is index 1. If the leader's log does not contain any entries from the conflicting term, it will begin sending entries at the conflicting index. If the leader's log does have entries with that conflicting term, the leader will begin sending entries at the index after the last entry in its log that matches the conflicting term.

If the follower's log is simply too short, then it can return the conflicting index as the length of its log, and the leader can try to continue from there. This will either lead to successful replication or one of the previous cases with a conflicting term.

### Performance Manager Changes

Additionally, changes were made to the client's PerformanceManager to allow multiple threads to generate transactions for the consensus cluster. This is not a change to the Raft implementation itself, but part of the benchmarking infrastructure within ResilientDB. While benchmarking the Raft implementation, one client using one thread did not generate enough transactions to saturate bandwidth. Previously, multiple threads could be created to send out transactions, but generating them was still done by only one thread. Additionally, a semaphore class was added to prevent the threads generating transactions from getting too far ahead of the threads sending them out.

# Evaluation

The evaluation was performed remotely on CloudLab using four d430 machines for the Raft nodes. These computers have an Intel Xeon Processor E5-2630v3 2.4 GHz CPU, and 64 GB RAM. Experiments were run for 120 seconds. For all experiments, 3 runs were done, and the average throughput and average latency were recorded for each run. Only the run with the median average throughput was used. In these experiments, the client computer requests transactions to be added to the database, and the leader responds to the client once a transaction has been committed to being executed. For the evaluation, no leader elections occurred. The following config file was used for these runs, with `max_process_txn` being varied across the different runs:
```
{
  "client_batch_num": 100,
  "enable_viewchange": false,
  "recovery_enabled": false,
  "not_need_signature": true,
  "max_client_complaint_num": 10,
  "max_process_txn": 2048,
  "worker_num": 8,
  "input_worker_num": 5,
  "output_worker_num": 5,
  "recovery_ckpt_time_s": 60,
  "min_client_receive_num": 1,
  "raft_follower_batch_timeout_ms": 0
}
```

Note that in the following tables, the in-flight Transaction limit (`max_process_txn` in the config file) is different from the discussion on in-flight messages previously. The previous limit was on the number of unacknowledged messages sent from a **leader** to a **follower**. This in-flight limit is on the number of unacknowledged messages, each message being a batch of 100 transactions, from a **client** to the Raft cluster. This second type of in-flight limit is to prevent the client from overwhelming the Raft cluster.

## Comparison vs HotStuff-1
<table>
  <tr>
    <td align="center">
      <img src="/assets/images/raft/ThroughputByMaxInFlight.png"
           alt="Throughput vs. Maximum In-Flight Transactions"/>
      <br>
      <em>(a) Throughput vs. maximum in-flight transactions</em>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="/assets/images/raft/LatencyByMaxInFlight.png"
           alt="Latency by Maximum In-Flight Transactions"/>
      <br>
      <em>(b) Latency vs. maximum in-flight transactions</em>
    </td>
  </tr>
  <tr>
    <td align="center">
      <img src="/assets/images/raft/ThroughputByLatency.png"
           alt="Throughput by Latency"/>
      <br>
      <em>(c) Throughput vs. latency</em>
    </td>
  </tr>
</table>
<p style="text-align:center;">
    <em>Figure 6. Performance evaluation of the Raft implementation.</em>
</p>

Here, we can see that Raft appears to reach its saturation point around 2,048 maximum in-flight transactions. At this point, the bandwidth of the Raft cluster is fully saturated. As the number of transactions in the system increases, latency increases dramatically, and throughput drops.

Comparisons were run against HotStuff-1, another BFT protocol available in ResilientDB. Setting `recovery_enabled` set to false disables writing the WAL and metadata to stable storage, as well as snapshotting. As a result, the prefix of the log is not truncated. The choices to use 4 nodes and no recovery (no WAL, no storing of metadata) were made to compare to HotStuff-1, which requires a minimum of 4 nodes, and also operates fully in memory. Both protocols have their own saturation point due to their own bottlenecks. [Table 1](#table-raft-hotstuff) shows that, when bandwidth is fully saturated for both protocols, Raft achieves almost 6x higher throughput with just over 9x better latency.

<table id="table-raft-hotstuff" style="margin-left:auto; margin-right:auto; width:fit-content;">
  <tr>
    <th>Metric</th>
    <th>Raft</th>
    <th>HotStuff-1</th>
  </tr>
  <tr>
    <td>Maximum in-flight transactions from client</td>
    <td>2048</td>
    <td>3</td>
  </tr>
  <tr>
    <td>Average throughput (transactions/second)</td>
    <td>588,596</td>
    <td>104,080</td>
  </tr>
  <tr>
    <td>Average latency (ms)</td>
    <td>0.288</td>
    <td>2.611</td>
  </tr>
</table>
<p style="text-align:center;">
    <em>Table 1. Comparison between Raft and HotStuff-1 at their saturation point</em>
</p>


## Effect of Persistence

<table id="figure-7">
  <tr>
    <td align="center">
      <img src="/assets/images/raft/RecoveryThroughputVsLatency.png"
           alt="RecoveryGraph"/>
    </td>
  </tr>
</table>
<p style="text-align:center;">
    <em>Figure 7. Raft evaluation with Snapshotting and Persistence enabled.</em>
</p>

However, even with recovery enabled, Raft achieves nearly identical throughput and latency for values of `max_process_txn` around the saturation point. While this extra bookkeeping does have some overhead, the current settings do not fully utilize the available CPU threads, and the batching of WAL writes has minimized the effect of this overhead.


## Scalability
<table id="figure-8">
  <tr>
    <td align="center">
      <img src="/assets/images/raft/Scalability.png"
           alt="Scalability"/>
    </td>
  </tr>
</table>
<p style="text-align:center;">
    <em>Figure 8. Raft Scalability with different numbers of replicas (n).</em>
</p>

[Figure 8](#figure-8) illustrates the impact that the number of replicas has on the performance of Raft. An increase in the number of replicas leads to an increase in latency and an overall decrease in throughput. In Raft, the latency is generally bottlenecked by the leader. The leader needs to forward all transactions, and keep track of when quorum is reached for each transaction. This work grows linearly with respect to the number of replicas. However, there is also a constant component of the work that runs concurrently with the previous work, like disk I/O and network round-trip time. Since the system is saturated with client transactions, throughput decreases as latency increases. This causes the shape of the curve to be flatter at lower replica counts and steeper as the number of replicas increases.

## Commands to Execute
Here are the commands to download ResilientDB and prepare all requirements:
1. `sudo apt update`
2. `sudo apt install git psmisc`
3. `git clone https://github.com/apache/incubator-resilientdb.git`
4. `cd incubator-resilientdb/`
5. `./INSTALL.sh`
6. `./service/tools/kv/server_tools/start_kv_service.sh`
7. `bazel build service/tools/kv/api_tools/kv_service_tools`
8. `cd scripts/deploy/`
9. `touch config/key.conf`

To run the Raft performance script locally on only a single machine, use:
1. `./performance_local/raft_performance.sh config/kv_performance_server_local.conf` 

To run the evaluation remotely, several changes will need to be made:
1. Make sure the machine you are running the performance evaluation script on is able to connect via ssh key to the remote machines.
2. Change the contents of `scripts/deploy/config/key.conf` to point to the directory of the ssh key on the machine running the performance evaluation script, as shown in `scripts/deploy/config/key_example.conf`
3. (Optional) Change the existing file in `scripts/deploy/config/raft.config` to match the desired config, or pass the separate config file as the second argument to raft_performance.sh
4. Change the IP addresses in `scripts/deploy/config/kv_performance_server.conf` to the IP addresses of the remote machines.
5. Change the user and home directory in `scripts/deploy/script/env.sh` to match the user and home directory on all of the machines you will ssh into.
6. In `scripts/deploy/`, run `./performance/raft_performance.sh config/kv_performance_server.conf <optional_config_file>`

This script will build the needed binaries on the current machine and scp them to the other machines, so no setup should be needed on them.

In order to view more detailed log information, the following steps can be taken:
1. (Optional) If you would like to examine the individual log files, go to `scripts/deploy/performance/run_performance.sh` and uncomment the line `#rm -rf result_*_log`
2. (Optional) If you would like to see the more verbose VLOG lines, on the line where the server binary is actually run underneath the `# Start server` comment in `scripts/deploy/script/deploy.sh`, change `./${server_bin} server.config` to `./${server_bin} --v=3 server.config`

If you want to view detailed log information for the local version, instead modify the files `scripts/deploy/performance_local/run_performance.sh` and `scripts/deploy/script/deploy_local.sh`

The Raft and RaftRecovery tests can be run with:
1. `bazel test //platform/consensus/ordering/raft/algorithm:all --test_timeout=20`
2. `bazel test //platform/consensus/recovery:raft_recovery_test --test_timeout=60`

# Conclusion

Raft is a well-known and well-tested consensus algorithm used in many real-world applications. While the specification for Raft does describe the protocol, many implementation details are left to the reader. This is especially true for an implementation that integrates with a large existing codebase while maintaining compatibility and sharing code with other consensus protocols. On top of needing to ensure correctness, engineering and design effort had to be put in for several different components. Decisions had to be made to decide how to share RecoveryBase code with PBFT, to minimize lock contention in a multi-threaded environment through techniques like batching the WAL writes, and to add extra tracking of individual follower state to allow for more effective usage of pipelining AppendEntries RPCs.

Now, ResilientDB contains a CFT protocol with a different trust model than the BFT protocols. When the situation calls for a protocol where all involved computers can be trusted, this allows for better asymptotic complexity for messages sent as well as no need for cryptographic signatures. However, there is still work to be done for the Raft implementation.

## Future Work

- **Further Throughput Improvements**: When runs are done past Raft's saturation point at 2,048 maximum in-flight transactions, the latency spikes as expected, but throughput goes down. One plausible explanation for this is that once a leader's capacity to receive more transactions from the client is full, the buffer holding these transactions before they get added to the log becomes overwhelmed and degrades performance. It is future work to investigate this in more detail.

  Additionally, others have implemented optimizations to increase Raft throughput in various scenarios, such as [Multi-Raft](https://tikv.org/deep-dive/scalability/multi-raft/) or [Fast Raft](https://arxiv.org/abs/2506.17793v1).

- **Membership Changes**: Enable the safe addition or removal of servers from the cluster dynamically, without shutting down the system or halting operations.

- **Read-Only Operations**: Allow read-only operations without writing anything to the log. Some groundwork has already been laid by having leaders commit a blank NO-OP entry at the start of their term, as described in the Raft paper, which is a step toward this.

### Acknowledgements

The initial development of the Raft implementation was accomplished through the combined effort of five ResilientDB community members: Josh Hutton, Jim Brower, Nachiket Subbaraman, Vinoth Gopikrishnan, and Yuhua Huang. The engineering effort described in [Initial Raft Implementation](#initial-raft-implementation) contains work done by the group. Additionally, initial versions of the written sections in [Background](#background) and [Initial Raft Implementation](#initial-raft-implementation) were created by the group. The second section, [Follow-up Raft Improvements](#follow-up-raft-improvements), and the [Evaluation](#evaluation) document the additional work Josh Hutton completed individually afterward.

# Further Reading

S. Gilbert and N. Lynch, "Brewer's conjecture and the feasibility of consistent, available, partition-tolerant web services," SIGACT News, vol. 33, no. 2, pp. 51–59, Jun. 2002, doi: 10.1145/564585.564601.

etcd-io/raft. Go. etcd-io. [Online]. Available: [https://github.com/etcd-io/raft](https://github.com/etcd-io/raft)

A. Melnychuk and B. SebaRaj, "Implementation and Evaluation of Fast Raft for Hierarchical Consensus," Jun. 21, 2025, arXiv: arXiv:2506.17793. doi: 10.48550/arXiv.2506.17793.

D. Ongaro and J. Ousterhout, "In search of an understandable consensus algorithm," in Proceedings of the 2014 USENIX conference on USENIX Annual Technical Conference. USA: USENIX Association, Jun. 2014, pp. 305–320.

"Multi-raft." [Online]. Available: [https://tikv.org/deep-dive/scalability/multi-raft/](https://tikv.org/deep-dive/scalability/multi-raft/)

J. Gjengset <jon@thesquareplanet.com>, "Students' Guide to Raft." [Online]. Available: [https://thesquareplanet.com/blog/students-guide-to-raft/](https://thesquareplanet.com/blog/students-guide-to-raft/)
