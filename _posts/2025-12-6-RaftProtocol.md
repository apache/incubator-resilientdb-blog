---
layout: article
title: "Implementing RAFT Consensus Protocol for ResilientDB"
author: Vinoth Gopikrishnan, Josh, Jim, Nachiket, Eva
tags: ResielientDB Consensus Security
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

## What is Raft?

Raft is a consensus protocol used in many existing software systems including Kubernetes, Kafka, and MongoDB. It has been battle-tested in many circumstances since it was first described in 2013. The ResilientDB project on GitHub has had an open issue since October 2023 for Raft to be added to the portfolio of available consensus protocols. We aim to fulfill this feature request.

Raft is a crash fault tolerant (CFT) protocol, and would be the first non-Byzantine fault tolerant (BFT) consensus protocol available within ResilientDB. There are convenience and performance justifications for why it would be a worthwhile addition. A ResilientDB deployment would be able to function with only 3 replicas instead of the current minimum of 4 replicas. ResilientDB transaction throughput would scale far better with the number of replicas increases due to the preferable asymptotic message complexity of Raft (O(n)) compared to the PBFT family of protocols (O(n2)). The performance overhead of cryptography in the BFT protocols is also quite high, so latency improvements can also be expected while using Raft.


<p style="text-align:center;">
    <img src="/assets/images/raft/protocolOverview.png" alt="Raft Protocol Diagram"/>
    <br>
    <em>Figure 1. Picture describing Arrayán
    </em>
</p>


