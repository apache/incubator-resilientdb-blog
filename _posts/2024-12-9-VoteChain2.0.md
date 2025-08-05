---
layout: article
title: VoteChain 2.0 - Decentralizing Voting with Blockchain, One Vote at a Time
author: shengzhe
tags: VoteChain ResVault ResilientDB GraphQL
aside:
    toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    src: /assets/images/votechain/votechain_background.jpeg
---
## Overview

VoteChain 2.0 builds on the foundation of the original ResilientApp VoteChain, utilizing **ResilientDB's scalable blockchain infrastructure** to create a customizable and secure voting platform. The enhanced app includes customizable voting, voting history, and a results page and discussion panel for each poll. **ResVault** now provides blockchain-based authentication and enables authorized voting and discussion access. ResilientDB's blockchain guarantees data integrity, anonymity, and privacy, positioning VoteChain as a scalable solution for institutions seeking transparent yet customizable feedback mechanisms.

<p style="text-align:center;">
    <img src="/assets/images/votechain/home_screen.png" alt="VoteChain Home Screen"/>
    <br>
    <em>Figure 1. VoteChain Home Screen
    </em>
</p>

---

## Motivation

While the original ResilientApp VoteChain was designed for government-level elections, we recognized an opportunity to bring the benefits of secure, transparent voting to smaller communities. Research into voting fairness in the gaming community inspired the idea of decentralized, transparent voting to prevent fraud and build trust. To address these issues, we envisioned a solution that seamlessly blends security with user-friendly functionality that encourages collaboration and engagement. Leveraging ResilientDB's robust blockchain capabilities, we developed VoteChain 2.0 — a secure, decentralized platform tailored for community use.

---

## Key Features

1. **Secure and Transparent Elections**
2. **Single Voting Instance**
3. **Immutable Votes**
4. **User-Friendly Interface**
5. **NEW! User Authentication with ResVault**
6. **NEW! Customizable Voting**
7. **NEW! Participate in Discussion Panels**
8. **NEW! View Poll Results & Personal Vote History**

<p style="text-align:center;">
    <img src="/assets/images/votechain/features.png" alt="VoteChain Features"/>
    <br>
    <em>Figure 2. Key Features of VoteChain 2.0
    </em>
</p>

---

## System Architecture

<p style="text-align:center;">
    <img src="/assets/images/votechain/architecture.png" alt="System Architecture"/>
    <br>
    <em>Figure 3. System Architecture of VoteChain 2.0
    </em>
</p>

---

## Tech Stack

1. **ResilientDB** - Provides secure, transparent, and tamper-resistant data storage for voting.
2. **GraphQL** - Efficient data querying with APIs for transaction operations.
3. **React.js** - Builds interactive user interfaces.
4. **Material UI** - Ensures responsive design across devices.
5. **Node.js** - Handles server-side logic and API communication.
6. **ResVault** - Manages user authentication securely.
7. **MongoDB** - Organizes poll-related data (topics, votes, messages).

---

## Screenshots

### Home Screen
<p style="text-align:center;">
    <img src="/assets/images/votechain/home_screen.png" alt="Home Screen"/>
    <br>
    <em>Figure 4. Home Screen
    </em>
</p>

### Login Page
<p style="text-align:center;">
    <img src="/assets/images/votechain/login_page.png" alt="Login Page"/>
    <br>
    <em>Figure 5. Login Page
    </em>
</p>

### Create Polls Screen
<p style="text-align:center;">
    <img src="/assets/images/votechain/create_poll.png" alt="Create Poll Screen"/>
    <br>
    <em>Figure 6. Create Poll Screen
    </em>
</p>

### Results Screen
<p style="text-align:center;">
    <img src="/assets/images/votechain/results_screen.png" alt="Results Screen"/>
    <br>
    <em>Figure 7. Results Screen
    </em>
</p>

---

## Preparation

### Install ResVault Chrome Extension

Google Chrome is available on macOS, Linux, and Windows platforms. Please refer to the official Google installation guide based on your platform.

1. Clone the ResVault repo to get started:

    ```bash
    git clone https://github.com/ResilientApp/ResVault.git
    cd ResVault
    ```

2. Create the build folder in the repository:

    ```bash
    npm install
    npm run build
    ```

3. Load the extension in Chrome:
   - Open `chrome://extensions/` in Google Chrome and toggle **Developer mode** on.
   - Click on **Load unpacked**.
   - Select the build folder you just created.
   - You should see the ResVault extension in Chrome.

---

## Steps to Run the System

### Requirements

1. **Node.js**: Download and install Node.js from [Node.js Downloads](https://nodejs.org/) based on your platform.
2. **npm (Node Package Manager)**: npm is included with Node.js. Verify installation:

    ```bash
    node --version
    npm --version
    ```

### Running VoteChain

1. Clone the VoteChain Git repository to your local machine:

    ```bash
    git clone https://github.com/ItsBaiShiXi/VoteChain.git
    cd VoteChain
    ```

2. Install required dependencies:

    ```bash
    npm install
    ```

3. Once the dependencies are installed successfully:
   - Open a terminal and run the following command to start the backend:

        ```bash
        npm run start-backend
        ```

   - Open another terminal and run the following command to start the frontend:

        ```bash
        npm start
        ```

   - This will launch the application, and you can access it in your web browser.

4. Connect the ResVault Chrome extension. You can now anonymously log in to VoteChain through ResVault.

<p style="text-align:center;">
    <img src="/assets/images/votechain/resvault_connection.png" alt="ResVault Connection"/>
    <br>
    <em>Figure 1. Connecting ResVault to VoteChain
    </em>
</p>

---

## Contributors

- Shengzhe Zhang
- Xin Tang
- Madelyn Nguyen
- Zixuan Weng
- Xiuyuan Qi