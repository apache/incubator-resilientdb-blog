---
layout: article
title: ResIdentity Document Signer
excerpt_separator: "<!-- excerpt end -->"
Author: Sachin Shankar Balasubramanyam, Ananya Pandey, Anish Kataria, Shyam Varahagiri, Kiransingh Pal
tags: ResilientDB ResIdentity
aside:
   toc: true
article_header:
   type: overlay
   theme: dark
   ackground_color: '#000000'
   background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    src: /assets/images/residentity/banner.gif
---

**Powered by the robust ResilientDB fabric, ResIdentity secures crucial documents with a tamper-proof signing process that ensures verifiable integrity and heightened protection.**<!-- excerpt end -->

# Project Overview
## Background
**What ResIdentity Does** \
ResIdentity is a document signing application that allows users to securely authenticate and upload PDFs for signing. The digests of these signed PDFs are committed to the ResilientDB blockchain, which ensures that each document’s signature remains unaltered. Users can then download their signed PDFs as needed.

Additionally, ResIdentity enables users to upload signed PDFs for verification. During this process, the application checks the ResilientDB blockchain for a matching digest of the signed document to verify its authenticity.

## Motivation
**Why We Built ResIdentity** \
The need for secure and reliable document signing cannot be overstated. Traditional platforms, while prevalent, often fail to meet the necessary standards for handling sensitive documents like insurance contracts, loan paperwork, employment offers, etc. These platforms typically rely on centralized servers, which, if compromised, can expose personal information at the risk of theft or alteration. Such centralized systems are vulnerable to service disruptions owing to a single point of failure. This can impact the availability of critical services when they are most needed, causing operational delays, user frustration and reduced trust. Furthermore, privacy is another concern with conventional document signing solutions. Users must entrust their sensitive information to third-party providers, who manage and control all aspects of data security, leaving them withwith little control over their own data.

ResIdentity was built to address these vulnerabilities by leveraging the power of blockchain technology.

## What Sets Us Apart
By using the ResilientDB blockchain to store document digests, ResIdentity ensures that each document's signature is immutable and verifiable. This decentralized approach eliminates single points of failure, enhances data security, and gives users full control over their documents. The blockchain's inherent transparency also means that every transaction is auditable, providing a layer of accountability and trust that traditional platforms cannot match.

Through ResIdentity, we aim to restore confidence in digital transactions and empower users with a tool that upholds the integrity and privacy of their documents, redefining the standards for document security in the digital world.

## Links
- [ResIdentity Client Application](https://residentity.resilientdb.com/)
- [Demo (accessible through UC Davis e-mail)](https://drive.google.com/drive/folders/1eYQ-X_1hTyO_Ni1gRFiTIvdXjPHuqLI7?usp=sharing)
- [Source](https://github.com/orgs/ecs265-group/repositories)

# Feature Set
## Registration
A user's interaction with ResIdentity begins at Registration, which features a straightforward interface that requires information such as an email address, a secure password, etc. for registration. A public-private keypair is generated after successful registration and is stored at the client's machine; the public key is stored in MongoDB.

## Login
Returning users can quickly enter their email address and password to continue their secure journey on our platform. Upon login, users are redirected to a dashboard which presents the options to upload a PDF, verify an already signed PDF and logout.

## Upload PDFs
Our minimalist Upload PDF interface ensures a clear and direct user experience, allowing you to quickly upload your PDF for signing or verification with just a click.

## Sign PDFs
ResIdentity's PDF Signing UI displays the document uploaded in the previous step on the right for confirmation. On the left, it prompts the user to re-enter their password for additional security before affixing their signature to the document.

## Download PDFs
When the signed document is retrieved from ResIdentity's backend service, this interface faciliates its saving on the user's machine.

## Verify Signed PDFs
Users can quickly verify the authenticity of signed PDFs with by simply uploading their signed documents to ensure that they are genuine and unaltered.

# User Guide
## Setup
**Dependencies:**
[Docker Compose](https://docs.docker.com/compose/install/), [ResilientDB](https://resilientdb.incubator.apache.org/)

- To build and launch ResIdentity Server, run:
```
docker-compose up -d --build
```
- If ResilientDB's KVService doesn't start by default, run:
```
docker exec resid-core-backend bash -c "cd /resdb && chmod +x INSTALL.sh && chmod +x service/tools/kv/server_tools/start_kv_service.sh && ./INSTALL.sh && service/tools/kv/server_tools/start_kv_service.sh"
```
To verify the status of KV Service, run:
```
curl -X POST -d '{"id":"key1","value":"value1"}' 127.0.0.1:18000/v1/transactions/commit
curl 127.0.0.1:18000/v1/transactions/key1
```
- The server should be running on ```http://localhost:8000/```.
Visit ```http://localhost:8000/api``` to verify its status.

## Usage (Screenshots)
### User Registration
![defaultview](/assets/images/residentity/ss_registration.png)
### Upload, Sign & Download
![defaultview](/assets/images/residentity/ss_sign.png)
### Viewing the Signature
![defaultview](/assets/images/residentity/ss_viewsign.png)
### Verifying Signature Authenticity
![defaultview](/assets/images/residentity/ss_verify.png)

# Tech Specs
## Technology Stack
**Frontend Frameworks:**\
Next.js, Tailwind CSS\
**Backend Frameworks:**\
ResilientDB, Docker, Python FastAPI, MongoDB, AWS S3

## Architecture
![defaultview](/assets/images/residentity/architecture.png)

## Sequence Diagrams
### User Registration
![defaultview](/assets/images/residentity/Registration.png)
### PDF Upload, Signing and Download
![defaultview](/assets/images/residentity/Upload-Sign-Download.png)
### Signature Verification
![defaultview](/assets/images/residentity/Signature Verification.png)

# About The Developers
- **Sachin Shankar Balasubramanyam:** A first-year MSCS student at UC Davis, Sachin earned his Computer Science degree from PES University, Bengaluru, in 2023. He was the lead engineer at a VC-funded AI startup, with expertise in software architecture, Python, and web development, essential for ResIdentity's client application.

- **Ananya Pandey:** Ananya is an MSCS student at UC Davis. She earned her Bachelor's in Computer Science from PES University, Bengaluru, in 2018. With over five years of software development experience at Zebra Technologies and Expedia Group, she specializes in Android/AOSP, 802.11 protocols, Node.js, Spring Boot, and AWS, focusing on backend services for ResIdentity.

- **Anish Kataria:** Anish is pursuing an MS in Computer Science at UC Davis, with a Bachelor's in Computer Science from VIIT, Pune (2023). Formerly an Associate Software Engineer at LogicMonitor, he focused on backend development with Golang, Docker, Kubernetes, and scalable microservices. Anish is dedicated to the client application for ResIdentity.

- **Shyam Varahagiri:** Shyam is a first-year MSCS student at UC Davis and graduated from Manipal University Jaipur in 2024 with a BTech in Information Technology. He worked 11 months at Tata Elxsi as an MLOps Developer, using Docker, Kubernetes, and Python, and has frontend experience with React and Typescript. Shyam works on ResIdentity’s backend.

- **Kiransingh Pal:** Kiran, an MSCS student at UC Davis with a Bachelor's in Electronics and Telecommunications from Pune Institute of Computer Technology, has two years at miniOrange, leading Cloud Access Security projects, building cross-browser extensions, and working with MERN, Golang, Docker, and Kubernetes. Skilled in blockchain and multi-wallet solutions, Kiran focuses on client app development for ResIdentity.