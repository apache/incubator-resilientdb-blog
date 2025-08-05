---
title: "Resilient Vote"
date: 2024-12-8
tags:
  - Computer Science
  - Distributed Systems
  - Database
  - Security
  - ResilientDB
category: Science
author: Boqi Zhao
---

# ResVote: A Scalable and Secure Online Voting System Built on ResilientDB

<iframe width="560" height="315" src="https://www.youtube.com/embed/aHThfkBDVPg?si=L9O4tg5dqE4qONS_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Our Motivation

* Traditional voting systems, such as in-person voting and mail-in ballots, are often slow, prone to errors, and vulnerable to fraud. These methods are labor-intensive and struggle to handle large-scale elections efficiently.

* Resilient Vote leverages ResilientDB to create a secure, scalable online voting platform. The system guarantees confidentiality, integrity, and transparency, ensuring that elections are both reliable and tamper-proof.

## Our Goals 

## Project Goals and Objectives

The primary goal of **Resilient Vote** is to develop a secure and scalable voting system that addresses the limitations of traditional voting methods. By using **ResilientDB** as the underlying database, we aim to create a system that is not only reliable but also capable of handling large-scale elections efficiently.

Key objectives of our project include:

- **Scalability**: Build a platform that can handle a high volume of voters and election data without compromising performance.
- **Security**: Ensure the confidentiality and integrity of votes through cryptographic techniques and tamper-proof storage.
- **Transparency**: Provide clear and accessible visualizations of election data to ensure transparency and accountability in the voting process.
- **Automation**: Eliminate the need for manual vote counting by automating the vote tallying process, reducing errors and human intervention.
- **Ease of Use**: Develop a user-friendly web interface that makes the voting process seamless for voters, administrators, and election officials.


## Technical Approach

To build **Resilient Vote**, we designed a secure, scalable, and user-friendly system by leveraging modern technologies and distributed database solutions. Our approach integrates a web-based front end, a robust back-end API, and a fault-tolerant database system powered by **ResilientDB**.

![Resilient Vote](https://yfhe.net/images/ResVote/ResVote.png)

### System Architecture

The system is divided into three main components:

1. **Front-End (React & XML-RPC)**:
   - The user interface is built using **React**, ensuring a responsive and interactive experience for voters, administrators, and general users.
   - For communication between the front end and the back end, we use **XML-RPC** (Extensible Markup Language Remote Procedure Call). XML-RPC allows the front-end React application to interact with the back-end services securely and efficiently. By using this protocol, the system can send method calls to the server and receive responses, ensuring smooth data exchange and integration between the user interface and the database.
   - This approach provides a lightweight solution for data transmission, making it easy to implement in a distributed environment while maintaining high security and reliability.
   ![ResVote React Frontend](https://yfhe.net/images/ResVote/ResVote_React.png)


2. **Back-End (ResilientDB Crow Service & ORM)**:
   - The back-end uses **ResilientDB Crow Service** and **ResilientDB ORM** to interact with the database. The **Crow Service** ensures that the system is fault-tolerant and highly available, with data securely stored and accessible across distributed nodes.
   - The **ResilientDB ORM** simplifies the database interactions by abstracting CRUD operations into Python-friendly methods, reducing boilerplate code and making it easier to manage votes and election data efficiently.

3. **Database (ResilientDB & MongoDB)**:
   - **ResilientDB** serves as the primary database, providing a distributed, fault-tolerant system to store and manage votes securely. Key features of ResilientDB, such as **parallel processing**, **system crash resilience**, and robust **security**, ensure data integrity and high availability, making it the perfect solution for a scalable voting system.
   - **MongoDB** is used as a caching layer for the transactions. It stores temporary data related to votes and election activities, helping to reduce latency and improve system performance by offloading frequent read and write operations from ResilientDB. MongoDB serves as a fast, scalable cache to ensure that the system can handle high volumes of requests during peak voting periods.


4. **Randomized Vote Generator based-on Property-based Testing**:
   - To ensure the reliability and robustness of the voting system, we use the **Hypothesis** library, a property-based testing tool for simulating election events. With **Hypothesis**, we generate random election scenarios, including creating voters, elections, and votes. This enables us to test the system under various conditions and verify that it behaves as expected.
   - By stress-testing the system with large-scale, simulated data, **Hypothesis** helps identify edge cases and potential issues, ensuring that **Resilient Vote** performs accurately and securely under diverse scenarios.


## Deployment Tutorial From Scratch

In order to provide more complete examples for beginners who are interested in participating in distributed database projects, we have improved the project documentation to deliver a project guide from scratch.

### Python

We recommend using `conda` to manage python environment.

```sh
conda create -n "resdb" python=3.10.0 ipython
conda activate resdb
pip install -r requirements.txt
```

### Get ResilientDB Dependencies

To save some time, we also provide a docker image that has all the dependencies installed.

```sh
docker pull yfhecs/rsdb
docker run -d -p 18000:18000 --name resdb yfhecs/rsdb
```
If you choose to use docker, you can skip the following steps.
Please wait a few minutes for the docker container to start.
You can check if it is ready by the follow command:

```sh
❯ curl -X POST -d '{"id":"key1","value":"value1"}' localhost:18000/v1/transactions/commit
id: key1

❯ curl 127.0.0.1:18000/v1/transactions/key1
{"id":"key1","value":"value1"}
```

You can also find the related dockerfile in the `./docker` directory.


#### ResilientDB

The following commands will clone and build ResilientDB,
and then starts 4 replicas and 1 client. Each replica instantiates a key-value store.

```sh
git clone https://github.com/apache/incubator-resilientdb.git resilientdb
cd resilientdb
./INSTALL.sh
./service/tools/kv/server_tools/start_kv_service.sh
```

#### Graph QL

```sh
git clone https://github.com/apache/incubator-resilientdb-graphql.git resilientdb-graphql
cd resilientdb-graphql
sh ./INSTALL.sh
pip install -r requirements.txt
```

##### Running Crow service (HTTP endpoints)

```sh
bazel build service/http_server/crow_service_main
bazel-bin/service/http_server/crow_service_main service/tools/config/interface/client.config service/http_server/server_config.config
```


### ResVote Server

```sh
source ./env.sh
python app/serve.py
```

### ResVote TUI Client

```sh
source ./env.sh
python app/tui.py
```

## Results & Achievements 

The development of **Resilient Vote** has yielded significant progress in both the **Virtual Vote Generator** and **Data Visualizations**, two core features of the system.

### Virtual Vote Generator

One of the key innovations of our project is the **Virtual Vote Generator**, developed using the **Hypothesis** library. This tool simulates large-scale election events by automatically generating random voting data. It creates virtual voters, assigns them different identity attributes (such as age, gender, region, race, education level, timestamp etc.), and simulates voting in various election scenarios.

The **Virtual Vote Generator** serves three main purposes:
1. **Stress Testing**: It helps simulate high volumes of votes to ensure the system can handle large-scale elections without performance degradation.
2. **System Validation**: The generator validates that the system handles diverse voting scenarios correctly, ensuring data consistency across distributed nodes.

3. **Demographic Analysis**: By generating voters with varied attributes, the system can examine how different demographics might influence election outcomes, thus guiding more informed decision-making.

This feature is essential for testing the resilience and scalability of the system before it's deployed in real-world elections.

### Data Visualizations

The system includes a powerful **data visualization** component that provides a clear and interactive view of the election results. The visualizations include a variety of charts and graphs that are automatically updated as votes are cast. Key features of the data visualization component include:

- **Real-Time Election Results**: As votes are cast, the system displays real-time updates in the form of bar charts, pie charts, and line graphs.
- **Voter Demographics**: The system visualizes the distribution of votes across different voter demographics, such as age groups, gender, regions, race, education level and other attributes. For each attribute, we make overall visualization based on total voters.

https://yfhe.net/images/ResVote/ResVote_React.png

| <img src="https://yfhe.net/images/ResVote/candidate_distribution.png" alt="Candidate Distribution" style="width:150px; height:150px;"> | <img src="https://yfhe.net/images/ResVote/age_attribute_distribution.png" alt="Age Distribution" style="width:150px; height:auto;"> | <img src="https://yfhe.net/images/ResVote/gender_attribute_distribution.png" alt="Gender Distribution" style="width:150px; height:auto;"> |
|:---------------------------------------------------------------------------------------------------------------:|:-------------------------------------------------------------------------------------------------------------:|:-------------------------------------------------------------------------------------------------------------:|
| Candidate Distribution                                                                                          | Age Distribution                                                                                               | Gender Distribution                                                                                           |

| <img src="https://yfhe.net/images/ResVote/region_attribute_distribution.png" alt="Region Distribution" style="width:150px; height:auto;"> | <img src="https://yfhe.net/images/ResVote/race_attribute_distribution.png" alt="Race Distribution" style="width:150px; height:auto;"> | <img src="https://yfhe.net/images/ResVote/education_attribute_distribution.png" alt="Education Distribution" style="width:150px; height:auto;"> |
|:-------------------------------------------------------------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------------:|
| Region Distribution                                                                                          | Race Distribution                                                                                               | Education Distribution                                                                                           |

   And we also make grouped analysis of different candidates for better comparison.

| <img src="https://yfhe.net/images/ResVote/age_grouped_bar_chart.png" alt="Age Grouped Bar Chart" style="width:150px; height:auto;"> | <img src="https://yfhe.net/images/ResVote/gender_grouped_bar_chart.png" alt="Gender Grouped Bar Chart" style="width:150px; height:auto;"> | <img src="https://yfhe.net/images/ResVote/region_grouped_bar_chart.png" alt="Region Grouped Bar Chart" style="width:150px; height:auto;"> |
|:-------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------:|
| Age Grouped Bar Chart                                                                                         | Gender Grouped Bar Chart                                                                                         | Region Grouped Bar Chart                                                                                         |

<div style="display: flex; justify-content: center;">

| <img src="https://yfhe.net/images/ResVote/race_grouped_bar_chart.png" alt="Race Grouped Bar Chart" style="width:150px; height:auto;"> | <img src="https://yfhe.net/images/ResVote/education_grouped_bar_chart.png" alt="Education Grouped Bar Chart" style="width:150px; height:auto;"> |
|:-------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------:|
| Race Grouped Bar Chart                                                                                         | Education Grouped Bar Chart                                                                                       |

</div>



- **Trend Analysis**: The visualizations show trends in voting over time, helping administrators understand voter participation patterns.
![Time Series Analysis](https://yfhe.net/images/ResVote/time_series.png)

These visualizations not only enhance the transparency of the voting process but also make it easier to interpret the results and track the progress of elections.

By combining these two powerful features—automated voting simulations and real-time, interactive data visualizations—**Resilient Vote** provides a robust platform for conducting scalable, transparent, and efficient elections.

### Feasibility Analysis

While the Virtual Vote Generator was initially designed to produce synthetic data for stress-testing and validation, its underlying structure closely mirrors that of real-world voting data. By leveraging randomized yet controlled attribute assignments—gender, region, age, race, education level—the generator ensures broad coverage of realistic scenarios. This design principle allows the transition from testing scenarios to actual election data to be seamless.

In practice, once real votes start coming in, the same code paths and visualization routines used for generated data can be directly applied to genuine voting records. Since the generator’s outputs and real votes share the same data schema, all existing analytic and graphical methods remain valid. The rich set of demographic and temporal charts that clarify patterns in synthetic elections will likewise provide meaningful insights when applied to real voter populations. In other words, the visualizations that help us pinpoint unexpected trends or correlations in randomized testing scenarios are just as effective in highlighting authentic voter behaviors, preferences, and participation patterns.

By maintaining this consistent data format and analysis approach, **Resilient Vote** ensures that lessons learned from simulation inform actual voting events. As a result, insights gained during development translate into actionable understanding and enhanced transparency once real ballots are being cast.

## Future Plans

As we continue to develop **Resilient Vote**, there are several key areas we plan to improve and enhance:

### 1. UI Enhancement and Front-End & Back-End Integration Upgrade

Our primary focus for future development is to **enhance the user interface** with more user-friendly operating logic and **strengthen the integration between the front-end and back-end**. While the current system is functional, we aim to improve the overall user experience by ensuring a seamless flow of data between the front-end and back-end, making the platform more responsive and efficient.

### 2. Real-Time Visualization of Vote Trends

We will take the **data visualization** features a step further by incorporating **real-time vote trends** during elections. This will allow voters and administrators to track the election results dynamically, providing insights into voter participation and trends as the voting progresses.

### 3. Enhanced User Registration Security

We will implement more robust security measures like Multi-factor authentication (MFA) to protect user identities and prevent unauthorized access. In addition, we plan to achieve advanced encryption methods to protect personal information and prevent data breaches.

## About Team

**ECS 265 Distributed Database Systems Fall 2024**  
Professor [Mohammad Sadoghi](https://expolab.org/)
TA [Daikai Kang](https://dakaikang.github.io/)

### Team Members:
- Tyler Culp
- Jason Eissayou
- Simon Draeger
- [Yifeng He](https://yfhe.net/about/)
- [Jicheng Wang](https://www.linkedin.com/in/jicheng-jeremy-wang-0a07b833b/)
- Boqi Zhao

University of California, Davis  
Davis, CA
