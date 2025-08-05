---
layout: article
title: PetChain 
author: Ansha Prashanth, Neha Pradeep, Sakshi Singh, Sarika Dinesh
tags: PetChain ResilientDb Full-Stack SmartContracts
aside:
    toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    src: /assets/images/petchain/blog-bg.jpg

---

Built on the ResilientDB fabric, PetChain provides secure lost-and-found tracking, seamless health record management, streamlined ownership transfer, and automated pet insurance processing, ensuring reliability in pet care.

## Project Overview
### Motivation

The pet industry, valued at around $150 billion in the U.S. alone, faces critical challenges in pet identification, health record management, and delays in insurance processing. As a team of pet owners and technology enthusiasts, we’ve experienced various challenges in pet ownership—ranging from the frustration of delayed insurance disbursements during emergencies to difficulties accessing reliable information on pet ownership transfers. These shared experiences underscored the need for a unified, secure system that offers pet owners faster and better transparent solutions. This need drove us to develop PetChain, a blockchain-based platform designed to holistically address these issues and enhance the overall pet ownership experience.

### Why PetChain?
**Scalable and Reliable**: PetChain, powered by ResilientDB, delivers a robust platform designed to scale effortlessly with increasing data and users, maintaining high performance and fault tolerance.<br>
**Streamlined Lost-and-Found and Ownership Transfers**: Leveraging blockchain technology, PetChain enables swift and secure reunification of lost pets with their owners while simplifying the transfer of pet ownership.<br>
**Automated Insurance Processing**: PetChain uses smart contracts to automate insurance claims, ensuring faster, more efficient, and error-free processes.<br>
**Secure and Transparent**: Blockchain-based storage ensures pet identification and ownership data remain tamper-proof, fostering trust and transparency in pet care.

## Feature Set
<div style="display: flex; justify-content: center; gap: 10px;">
    <div>
        <img src="/assets/images/petchain/Landing-page.png" alt="LandingPage" width="500" />
        <br>
        <em>Landing Page</em>
    </div>
    <div>
        <img src="/assets/images/petchain/Signup.png" alt="SignUp" width="700" />
        <br>
        <em>SignUp Page</em>
    </div>
</div>
### User and Pet Registration
The registration and login workflow in PetChain uses JWT tokens for secure authentication and role-based access control. Passwords are hashed using bcrypt and stored securely in the database. A tamper-proof ownership hash, created using the owner's custom ID and unique petId with SHA-256, is stored in ResDB for blockchain traceability, while pet details are saved in MongoDB for quick retrieval. Owners can retrieve pet details via the getPetDetails API, ensuring a secure and efficient process for pet registration and ownership validation.

Pet registration event logged in ResDB:
```
(2024-12-09 11:05:19) [INFO    ] Request: 127.0.0.1:45068 0x7fccdc002870 HTTP/1.1 POST /v1/transactions/commit
I20241209 03:05:19.347833 652360 crow_service.cpp:158] body: {"id":"OWNER_1733742092244","value":{"pet_id":"PET_1733742319324","ownershipHash":"a44f3f6cb0c0efc4671f254ffe1eeb28a09ae7dfc820f5be21e85c38c2eef74c","timeStamp":"2024-12-09T11:05:19.326Z","status":"active","event":"register pet"}}
I20241209 03:05:19.349356 652360 crow_service.cpp:180] Set OWNER_1733742092244 to {"id":"OWNER_1733742092244","value":{"pet_id":"PET_1733742319324","ownershipHash":"a44f3f6cb0c0efc4671f254ffe1eeb28a09ae7dfc820f5be21e85c38c2eef74c","timeStamp":"2024-12-09T11:05:19.326Z","status":"active","event":"register pet"}}
```
<div style="display: flex; justify-content: center; gap: 10px;">
    <div>
        <img src="/assets/images/petchain/OwnerDashboard.png" alt="User Landing Page" />
        <br>
        <em>User DashBoard</em>
    </div>
    <div>
        <img src="/assets/images/petchain/RegisterPet.png" alt="Register Pet"/>
        <br>
        <em>Register Pet</em>
    </div>
</div>
<div>
        <img src="/assets/images/petchain/ManagePet.png" alt="Manage Pet" />
        <br>
</div>
### Lost and Found
<p align="center">
    <img src="/assets/images/petchain/LostFoundSequence.png" alt="Logo" width="800"/>
    <br>
    <em> Figure 1. Sequence Diagram for Lost and Found Service
    </em>
</p>
When a pet goes missing, the owner can report it by clicking the "Lost" button in the user interface, triggering a POST request to the petLostandFound() API. This API updates the pet’s status in MongoDB and generates a unique LostHash using the pet ID, owner ID, and event details, which is then securely stored in ResDB.
```
(2024-12-09 11:05:19) [INFO    ] Response: 0x7fccdc002870 /v1/transactions/commit 201 0
(2024-12-09 11:06:38) [INFO    ] Request: 127.0.0.1:39276 0x7fccdc000c20 HTTP/1.1 POST /v1/transactions/commit
I20241209 03:06:38.358037 652359 crow_service.cpp:158] body: {"id":"OWNER_1733742092244","value":{"pet_id":"PET_1733742319324","lostHash":"0d753f137b9725f0bc7b8e29d376f4ff9ae67ff946a8fea7a301695dae293de9","timeStamp":"2024-12-09T11:06:38.354Z","status":"active","event":"lost pet"}}
I20241209 03:06:38.358719 652359 crow_service.cpp:180] Set OWNER_1733742092244 to {"id":"OWNER_1733742092244","value":{"pet_id":"PET_1733742319324","lostHash":"0d753f137b9725f0bc7b8e29d376f4ff9ae67ff946a8fea7a301695dae293de9","timeStamp":"2024-12-09T11:06:38.354Z","status":"active","event":"lost pet"}}
```
<div>
        <img src="/assets/images/petchain/MarkLost.png" alt="MarkLost" width="600" />
        <br>
        <em>Mark Pet as Lost</em>
</div>

When the pet is found by someone, they can submit the pet's details via the "Found" feature in the UI, prompting the searchLostPet() API to generate a FoundHash.  The system then retrieves the LostHash from ResDB, performs validation and updates the pet’s status to "found," completing the process of reuniting the pets with its owner.
```
(2024-12-09 11:08:41) [INFO    ] Request: 127.0.0.1:35466 0x7fccdc002870 HTTP/1.1 POST /v1/transactions/commit
I20241209 03:08:41.755368 652360 crow_service.cpp:158] body: {"id":"OWNER_1733742092244","value":{"pet_id":"PET_1733742319324","foundHash":"8c85e9e98417656cdbc5172bfac80aa7bde53deda0fd44f8d8d519646cfeb240","timeStamp":"2024-12-09T11:08:41.752Z","status":"active","event":"found pet"}}
I20241209 03:08:41.755961 652360 crow_service.cpp:180] Set OWNER_1733742092244 to {"id":"OWNER_1733742092244","value":{"pet_id":"PET_1733742319324","foundHash":"8c85e9e98417656cdbc5172bfac80aa7bde53deda0fd44f8d8d519646cfeb240","timeStamp":"2024-12-09T11:08:41.752Z","status":"active","event":"found pet"}}
```
<div style="display: flex; justify-content: center; gap: 10px;">
    <div>
        <img src="/assets/images/petchain/Found.png" alt="Found Pet"/>
        <br>
        <em>Found Pet</em>
    </div>
    <div>
        <img src="/assets/images/petchain/NotifyOwner.png" alt="Notify Owner"/>
        <br>
        <em>Notify Owner</em>
    </div>
</div>

### Pet-Health Management
**Health Record Management**: Owners can record vaccination history, allergies, minor illnesses, and past treatments for their pets.
<div>
        <img src="/assets/images/petchain/PetHealth.png" alt="Pet Health"/>
        <br>
</div>
<br>
**Veterinarian Access**: A dedicated Veterinarian Profile interface allows vets to view pet health records by entering the pet ID.
<div>
        <img src="/assets/images/petchain/VetUpdate.png" alt="Vet Portal"/>
        <br>
        <em>Vet Portal</em>
</div>
### Ownership Transfer

<p align="center">
    <img src="/assets/images/petchain/OwnerTransferSequence.png" alt="Logo" width="800"/>
    <br>
    <em> Figure 2. Sequence Diagram for Ownership Transfer
    </em>
</p>
The ownership transfer process in PetChain ensures a secure and seamless experience. When the "Transfer Ownership" button is clicked, a POST request initiates the transfer by generating an approval token, saving the transfer request in MongoDB, and sending an email notification to the new owner's email using Nodemailer. The email contains an approval link that, when clicked, triggers a POST request to validate the transfer. This involves fetching pet details and ownership hashes from MongoDB and ResDB, validating the data, and generating a transfer hash upon success. The event is logged in ResDB, MongoDB is updated with the new owner's details, and the new owner is redirected to the website with a success message. In case of validation failure, the transfer is rejected, and the owner is notified via a dialog box.

```
(2024-12-09 11:25:14) [INFO    ] Request: 127.0.0.1:38158 0x7fccdc002870 HTTP/1.1 POST /v1/transactions/commit
I20241209 03:25:14.258247 652360 crow_service.cpp:158] body: {"id":"OWNER_1733743246179","value":{"pet_id":"PET_1733742319324","ownershipTransfer":{"oldOwnerId":"OWNER_1733742092244","newOwnerId":"OWNER_1733743246179"},"transferHash":"0e3da49f70790cc3d2a761319c8e2432507d36c312f390ef0850f8803701ff99","timeStamp":"2024-12-09T11:25:14.255Z","status":"completed","event":"ownership transfer"}}
I20241209 03:25:14.258463 652360 crow_service.cpp:180] Set OWNER_1733743246179 to {"id":"OWNER_1733743246179","value":{"pet_id":"PET_1733742319324","ownershipTransfer":{"oldOwnerId":"OWNER_1733742092244","newOwnerId":"OWNER_1733743246179"},"transferHash":"0e3da49f70790cc3d2a761319c8e2432507d36c312f390ef0850f8803701ff99","timeStamp":"2024-12-09T11:25:14.255Z","status":"completed","event":"ownership transfer"}}
```
<div style="display: flex; justify-content: center; gap: 10px;">
    <div>
        <img src="/assets/images/petchain/InitTransfer.png" alt="Initiate Request"/>
        <br>
        <em>Initiate Request</em>
    </div>
    <div>
        <img src="/assets/images/petchain/Notify.png" alt="NotifyOldOwner"/>
        <br>
        <em>Dialog pop up for owner</em>
    </div>
</div>
<br>
<br>
<div style="display: flex; justify-content: center; gap: 10px;">
<div>
        <img src="/assets/images/petchain/ApproveRequest.png" alt="EmailRequest" width="800"/>
        <br>
        <em>Approval Request to NewOwner</em>
</div>
<div>
        <img src="/assets/images/petchain/TransferSuccess.png" alt="OldOwnerView" width="800"/>
        <br>
        <em>Old Owner View</em>
    </div>
</div>


### Smart Contract Based Insurance Processing
The insurance service in PetChain provides two main functionalities: Add Insurance and Claim Insurance. In the Add Insurance process, a POST API maps a pet's details (pet ID) to a policy's information, including the policy number and coverage type. This information is securely stored in MongoDB, finalizing the policy addition. In the Claim Insurance process, a POST API integrates with a smart contract to handle claims in two stages: Pre-Approval, which verifies eligibility based on policy details, and Approval, which calculates the reimbursement amount by considering the coverage type and deductibles. This approach ensures a secure, efficient, and transparent insurance workflow.

<p align="center">
    <img src="/assets/images/petchain/InsuranceSequence.png" alt="Logo" width="800"/>
    <br>
    <em> Figure 3. Sequence Diagram for Insurance Processing
    </em>
</p>
<div>
        <img src="/assets/images/petchain/AddInsurance.png" alt="AddInsurance"/>
        <br>
        <em>Add Insurance</em>
</div>
<div>
        <img src="/assets/images/petchain/ClaimInsurance.png" alt="ClaimInsurance"/>
        <br>
        <em>Claim Insurance</em>
</div>


## Technical Specifications
### System Architecture
This section outlines how the core components of the PetChain platform are integrated to deliver seamless functionality. The Node.js back-end is used to manage and mediate between the databases and contract services. MongoDB is used for quick retrieval of user, pet, health, and insurance data, while ResDB stores important hashes, IDs, and events for secure blockchain operations. Smart contracts ensure automated and transparent processes like insurance management, and the front-end provides an intuitive interface for interaction with all system features.
<p align="center">
    <img src="/assets/images/petchain/System-Architecture.jpg" alt="Logo"/>
    <br>
    <em> Figure 4. Architecture Diagram
    </em>
</p>

### Technology Stack
- **Frontend**: React, MaterialUI
- **Backend**: Node.js, Express.js, HardHat, ethers.js, Solidity, Nodemailer
- **Databases**: ResilientDB, MongoDB

### Steps to Run the System
#### Prerequisites
- Download NodeJS from [here](https://nodejs.org/en/download) and ensure that it’s added to PATH.
- ##### Setup ResilientDB and start KV Service
```sh
git clone https://github.com/apache/incubator-resilientdb.git resilientdb
cd resilientdb
./INSTALL.sh
./service/tools/kv/server_tools/start_kv_service.sh
```
- ##### Setup Crow HTTP Service
```sh
git clone https://github.com/ResilientApp/ResilientDB-GraphQL.git
cd ResilientDB-GraphQL
bazel build service/http_server/crow_service_main
bazel-bin/service/http_server/crow_service_main service/tools/config/interface/client.config service/http_server/server_config.config
```

#### Running the application
- Git clone the repositories linked below into individual folders. Navigate to project directories and run the following command in each.
```sh
npm install
```
- Create a .env file in the backend folder and enter the following:
```sh
EMAIL_USER=<email-address>
EMAIL_PASS=<app-password-for-email-account>
JWT_SECRET=<token>
CONTRACT_ADDRESS=<contract-address>
ACCOUNT_ADDRESS=<contract-account-address>
DB_URL=<mongo-db-url>
PRIVATE_KEY=<private-key-for-contract-deployment>
```
- Run the following command in each folder (frontend and backend) to get the application running.
```
npm start
```
- Navigate to http://localhost:3001/ to launch application in web browser.


### Links
- Code repository:
[Frontend](https://github.com/nehapradeep/PetChainPlus), 
[Backend](https://github.com/sakshisingh301/PetChainBackend/tree/master)
<br>
- [Demo Video](https://drive.google.com/drive/folders/1bGqkGbOBiAmOWikIJbD70ByvCTGR6PsK?usp=sharing)
<br>
- [Presentation Slides](https://docs.google.com/presentation/d/190cyr4yLdWzSKJSrkdzT-m8rJX4C3bKX/edit?usp=sharing&ouid=116836227048374057417&rtpof=true&sd=true)


## About Team

- Sakshi Singh
- Ansha Prashanth
- Neha Pradeep
- Sarika Dinesh