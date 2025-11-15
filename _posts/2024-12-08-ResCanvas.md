---
layout: article
title: The World’s First Blockchain-Backed Drawing App? Introducing ResCanvas.
author: Henry Chou, Chris Ruan
tags: ResCanvas, ResilientDB, Full-Stack, ResVault, MongoDB, DigitalWallet, GraphQL, Redis
aside:
  toc: true
article_header:
  type: overlay
  theme: dark
  background_color: "#000000"
  background_image:
    gradient: "linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))"
    src: /assets/images/resdb-gettingstarted/code_close_up.jpeg
---

<p align="center">
  <img width="389" height="91" alt="image" src="https://github.com/user-attachments/assets/24a5606a-988c-43ea-9eee-ab19ed6628be" />
</p>

_**<p align="center">Real-time creativity, backed by decentralized trust and resiliency.</p>**_
**<p align="center">By Henry Chou and Chris Ruan</p>**

# The Existing Problem
Drawing is an important aspect of art and free expression within a variety of domains to express new ideas and valuable works of creativity. Tools such as MS Paint allow for drawing to be achievable on the computer, with online tools extending that functionality over the cloud where users can share and collaborate on drawings and other digital works of art. Both Google's Drawing and Canva's Draw application have a sharable canvas page where registered users can draw, and Figma has also been quite popular due to its wide array of features and ease of use.

As you can see, drawing tools are everywhere, from quick doodle apps running on your computer to full fledged cloud design suites hosted over the cloud. **But beneath the convenience lies a hidden cost: centralization.** When you draw on a typical web platform, your work and your identity, are stored on a server you don't control. Your art can be censored, your actions monitored, your data analyzed or sold.

At the same time, creativity has never been more collaborative. Artists sketch together, communities brainstorm visually, and teams rely on whiteboards to communicate ideas.

But existing online platforms store the drawing and user data centrally, making personal data easily trackable by their respective companies, and easily sharable to other third parties such as advertisers. The drawings can be censored by both private and public entities, including government agencies. Privacy is important, yet online collaboration is an essential part of many user's daily workflow.

Even the famous Reddit _r/place_ board, while being massively collaborative as a pixel based canvas, relies on fully centralized state and exposes all user activity to the platform.

Creativity thrives only when ideas can be freely expressed. But freedom requires autonomy, and autonomy requires decentralization.

So we asked a simple question:

**Why can’t collaborative creativity be both expressive and decentralized?**

# Our Solution: ResCanvas
That question led to ResCanvas - a fully decentralized, real-time drawing platform powered by ResilientDB, a high performance blockchain database engineered for trust on a global scale. The canvas drawing board is the core feature of ResCanvas, designed to allow users to perform drawings all while being censorship resistant. It is simple to use, yet it allows for infinite possibilities. **To our knowledge, ResCanvas is the world's first drawing and creativity platform that combines the key breakthroughs in database decentralization brought forth by a blockchain based database, ResilientDB, with the power of expression that comes with art, bridging the gap between the arts and the sciences.**

ResCanvas is designed to seamlessly integrate drawings with the familiarity of online contribution between users using effective synchronization of each user's canvas drawing page. This allows for error-free consistency even when multiple users are drawing all at the same time. 

Multiple users are able to collaborate on a single canvas, and just as many things in life have strength in numbers, so too are the users on a canvas. One user could be working on one key part of a drawing while other users can finish the remaining parts in a collaborative manner.

The key feature of ResCanvas is defined by having all drawings stored persistently within ResilientDB in a stroke by stroke manner. Each stroke is individually cached via in-memory data store using Redis serving as the frontend cache, hosted on a trusted node of your choice. This ensures that the end user is able to receive all the strokes from the other users regardless of the response latency of ResilientDB, which greatly enhances performance for the end user. 

In essence, all users will see each other's strokes under a decentralized context and without any reliance on a centralized server for processing requests and storing data. 

ResCanvas works seamlessly, with the familiarity of the drawing tools you already use, without the issues of surveillance, control, or central ownership.

## Detailed Architecture and Concepts
To turn decentralized creativity into a reality, our application consists of several major components. 

The first one is the **frontend (React)**, which handles drawing input, local smoothing/coalescing of strokes, UI state (tools, color, thickness), optimistic local rendering, and Socket.IO for real-time updates. Thus the frontend handles the user facing side of ResCanvas and ensures a smooth UX while ensuring communication between this frontend layer and the backend. This layer also handles the storage of auth tokens in `localStorage` and its API wrappers (like `frontend/src/api/`) automatically attach JWT access tokens to all protected requests as well. The most important aspect of this layer in terms of security is that the frontend does not perform authentication or authorization logic, since it simply presents credentials and tokens to the backend.

This brings us to the **backend (Flask + Flask-SocketIO)**, which serves as the authoritative security boundary and data handler for the application. The backend validates all JWT tokens server-side using middleware, enforces room membership and permissions, verifies client-side signatures for secure rooms, encrypts/decrypts strokes for private/secure rooms, commits transactions to ResilientDB via GraphQL, and also retrieves data as needed according to the frontend's request. All protected API routes and Socket.IO connections require valid JWT access tokens sent via `Authorization: Bearer <token>` header. Since the backend performs all security checks, clients cannot bypass authentication or authorization and must go through the backend for all sensitive requests. Furthermore, when working with private or secure rooms, the backend handles encryption/decryption and room key management as well.

Going into deeper detail with regards to the backend, it interacts with several other key components. 

The first one is **ResilientDB**, the persistent, decentralized, immutable transaction log where strokes are ultimately stored, and the core essence of this application. Strokes are written as transactions so the full history is auditable and censorship-resistant. Through the `resilient-python-cache` library, ResilientDB synchronizes data blocks with **MongoDB (canvasCache)**. MongoDB is a warm persistent cache and queryable replica of strokes so the backend can serve reads without contacting ResilientDB directly for every request, and can be hosted on any node that users trust. This essentially serves as a secure sync bridge that mirrors ResilientDB into MongoDB. 

From MongoDB, our backend handles the syncronization of data with **Redis**, which is a short-lived, in-memory store keyed by room for fast read/write and undo/redo operations. Redis is intentionally ephemeral as it allows quick synchronization of live sessions while ResilientDB acts as the long-term durable store. Just like MongoDB, this Redis cache can be hosted by any trusted node, and thus ensures that security and privacy guarantees of ResilientDB are still preserved while ensuring fast performance. It can be said that our backend is also another sync bridge layer, that of between MongoDB and Redis.

### Data Model and Stroke Format
ResCanvas uses a simple, compact stroke model that is end-to-end friendly for network transport and decentralized commits. A typical base stroke data payload (JSON) contains the following data:

  - drawingId: unique per-user or per-drawing session
  - userId: author id (when available/allowed)
  - color: hex or named value
  - lineWidth: numeric stroke thickness
  - pathData: an array of (x,y) points, optionally compressed (delta-encoded)
  - timestamp: client-side timestamp for ordering and replay
  - metadata: optional fields for signing, encryption info, transform/offsets, custom brush styles, .etc

### Undo/Redo and Edit History
Despite the decentralized, blockchain backed nature of ResCanvas, users can still leverage the functionality they know and love, including undoing and redoing operations. Undo/redo is implemented through per-room, per-user stacks stored in Redis. Each user action that mutates the canvas pushes an entry to the user's undo stack and updates the live room state in Redis. Redo pops from a redo stack and applies the strokes again via the same commit flow (including related signing and encryption rules). 

Because ResilientDB is immutable, undo/redo on the client is implemented as additional strokes that semantically represent an "undo" (for example, through a delta or a tombstone stroke) by using a separate metadata layer that signals removal in replay. The visible client behavior is immediate, while the authoritative history in ResilientDB preserves the full append only log.

### Consistency, Concurrency and Ordering
Multiple users can draw simultaneously since the system is designed for eventual consistency with low-latency broadcast, where each stroke is broadcast immediately via Socket.IO to all connected room participants. This allows the application to provide near real-time feedback. The backend attempts to persist strokes to ResilientDB and caches (Redis/MongoDB). If ResilientDB write is delayed, clients still see strokes from the Socket.IO broadcast and from Redis while waiting for the backend to finishing writing the stroke data. 

Ordering is primarily guided by timestamps and the sequence of commits in ResilientDB. When replaying history, the authoritative order comes from the ResilientDB transaction log so all data and their ordering is preserved even if the canvas itself is cleared, strokes are undone, or even if the entire canvas room is deleted by the user.

### ResilientDB Theory
ResilientDB is used as an immutable, decentralized transaction log. There are several key properties that we rely on. 

The first one is that of **immutability**, where once a stroke is committed, it cannot be altered silently. This increases trust and accountability. The second one is **decentralization**, since no single host controls the persistent copy of strokes, reducing censorship and central data harvesting. The third key property that we rely on is **auditability** as the entire canvas history can be inspected and verified against the ResilientDB ledger. This ensures transparency as anyone can view and verify the user's drawing history and actions taken on the application, and serves as a key deterrent against malicious activity on the canvas.

By treating each stroke as a transaction, we now achieve a chronological, tamper-evident history of canvas changes. Anyone can verify and review the changes to the canvas as the ground truth source of information. The sync bridge mirrors transactions into MongoDB so read queries don't need to hit ResilientDB for every request, while Redis caching further enhances the performance from the UX standpoint by caching the data from MongoDB on an in-memory basis within a trusted node. Removal operations such as undo and redo, as well as clear canvas/room deletions are simulated using time stamp markers to achieve the same effects without altering the historical backend data as well. 

This hierarchical relationship between ResilientDB, MongoDB, and Redis essentially serves as an unique balance between user experience, security, and privacy.

### Public Rooms vs Private Rooms vs Secure Rooms
In ResCanvas, options are provided to meet the diverse needs of users. One way we achieve this is through the three different types of canvas rooms that users can create. 

The first kind is **public rooms**, which allow anyone to access and draw in them, and so all the data is publically accessible by all registered users without needing to perform decryption and obtaining an access key to the room. 

In **private rooms**, access is restricted as only invited users or those with the room key can join such rooms. Strokes are encrypted so only members with the room key can decrypt and access them. The backend participates in wrapping/unwrapping room keys using `ROOM_MASTER_KEY_B64`. This allows users who want additional privacy while drawing contents containing sensitive or personal information to be able to do so without the risk of exposing all their raw drawings to the general public.

**Secure rooms** go further and expand upon the protections of private rooms by requiring client-side signing of strokes with a cryptographic wallet (such as [ResVault](https://chromewebstore.google.com/detail/resvault/ejlihnefafcgfajaomeeogdhdhhajamf?pli=1)). Each stroke is signed by the user's wallet private key and the signature is stored with the stroke. This enables verifiable authorship since anyone can confirm a stroke was created by the owner of a given wallet address.

#### Putting cryptographic signatures into use, secure rooms work as follows (assuming ResVault is used):
1. User connects cryptographic wallet via the frontend UI and grants signing permissions.
2. When drawing in a secure room, the frontend prepares the stroke payload and asks ResVault to sign the serialized stroke or a deterministic hash of it.
3. The signed payload (signature + public key or address) is sent to the backend along with the stroke.
4. The backend verifies the signature before accepting and committing the stroke to persistent storage.

## Security, Privacy and Threat Model
ResCanvas aims to improve user privacy and resist centralized censorship, and so we mitigated several threats in our application. 

One of the most significant threats that many web based applications face today is the danger of **central server compromise**, since many existing applications are based on a centralized sever and store important data there. However, in ResCanvas, persistent data is stored on ResilientDB and mirrored to MongoDB, which reduces a single point of failure due to the decentralized nature of ResilientDB.

We also prevent **data harvesting by a platform operator** from occurring in the first place as decentralized storage and client-side signing for secure rooms reduce linkability and provide verifiability. This ensures that user's data is not being collected for third party usage, for instance, which could result in data being leveraged for commercial purposes and malicious intent. This assurance in user's data security is also guaranteed through our prevention of **client-side authentication bypasses**. All authentication, authorization, and access control logic runs server-side, rather on the client's front end. The backend middleware validates tokens, checks permissions, and enforces room access rules. Even the most malicious of clients cannot manipulate or circumvent security checks because of this middleware.

Other security protections that ResCanvas offers includes handling the situation where there could be **token theft via XSS**. We manage this risk by having refresh tokens be stored in HttpOnly cookies that cannot be accessed by JavaScript, protecting long-lived sessions from cross-site scripting attacks. Additionally, we prevent **CSRF attacks** by having refresh cookies use `SameSite` attribute. Using this kind of attribute prevents cross-site request forgery from occurring. We also prevent **signature forgery** for secure rooms since the backend verifies cryptographic signatures server-side. This protection ensures that strokes cannot be attributed to users who didn't create them in the first place.

Despite these significant protections that ResCanvas offers, there are several key trade-offs and assumptions that are worth mentioning. 

For instance, there is still a risk that the application can suffer from **frontend device compromise**. While the backend enforces all security decisions, if a user's device or browser is compromised, attackers could steal access tokens from `localStorage` or wallet keys before signing. So access tokens are short-lived (15 minutes by default) to minimize exposure and refresh tokens in HttpOnly cookies are protected from JavaScript access.

The core functionality of ResCanvas also depends on the **availability of ResilientDB**. ResilientDB endpoints and GraphQL commit endpoints used by the backend must remain available and trusted by backend operators. If those services are compromised, ledger inclusion or availability may be affected. However, the probability of this occurring is extremely low due to the decentralized, blockchain nature of ResilientDB.

Furthermore, certain backend layers and services, such as Redis and MongoDB, rely on the user's level of **backend trust**. Users must trust the backend operators to correctly implement and enforce security policies, and also ensure that those backend layers and services are running on trusted nodes. Having nodes that are trustworthy to the user is essential as the backend has access to certain data such as unencrypted strokes for public rooms.

---

# ResCanvas Setup Guide
Want to deploy and run ResCanvas locally right on your own machine? This guide provides complete instructions to deploy ResCanvas locally, including setup for the cache layer, backend, and frontend.

## Prerequisites

Before starting, ensure the following dependencies are installed:

- **Python** ≥ 3.10 and ≤ 3.12  
- **Node.js** (LTS version via `nvm install --lts`)  
- **npm** (latest version)
- **Redis**
- **MongoDB Atlas** account with a working connection URI

## Step 0: MongoDB Account Setup

1. Go to [https://account.mongodb.com/account/login](https://account.mongodb.com/account/login) and **log in or register**.  
2. Create a **project** and **cluster** (if not already existing).  
3. Within your cluster, click **Connect → Drivers** and copy the connection string from **Step 3**
4. Keep this MongoDB connection URI handy. You will use it in later `.env` files.

## Step 1: Clone the Repository

```bash
git clone https://github.com/ResilientApp/ResCanvas.git
cd ResCanvas
```

Check your Python installation:

```bash
python3 --version
```

## Step 2: Resilient Python Cache (First Terminal Window)

This cache layer synchronizes strokes between MongoDB and ResilientDB.

### Setup

```bash
cd backend/incubator-resilientdb-resilient-python-cache/
pip install resilient-python-cache
```

Create a `.env` file in this directory with the following content
(replace everything between brackets):

```
MONGO_URL = "[URI_COPIED_FROM_MONGODB_CONNECTION]"
MONGO_DB = "canvasCache"
MONGO_COLLECTION = "strokes"
```

### Start the Cache Service

```bash
python3 example.py
```

This starts a MongoDB caching service that syncs data with ResilientDB via the `resilientdb://crow.resilientdb.com` endpoint defined in `cache.py`.

## Step 3: Backend Setup (Second Terminal Window)

The backend handles authentication, REST APIs, and interfaces with ResilientDB.

### Create Virtual Environment & Install Dependencies

```bash
cd backend/
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Generate Keys

```bash
python gen_keys.py
```

Copy the printed public and private keys.

### Create .env File

Create a new `.env` under the backend/ folder with the following contents
(replace values between brackets):

```
MONGO_ATLAS_URI=[URI_COPIED_FROM_MONGODB_CONNECTION]
SIGNER_PUBLIC_KEY=[PUBLIC_KEY_COPIED_FROM_GEN_KEYS_PY]
SIGNER_PRIVATE_KEY=[PRIVATE_KEY_COPIED_FROM_GEN_KEYS_PY]
RESILIENTDB_BASE_URI=https://crow.resilientdb.com
RESILIENTDB_GRAPHQL_URI=https://cloud.resilientdb.com/graphql
```

## Step 4: Redis Setup

Redis is required for caching and backend operations.

### macOS (Homebrew)

```bash
brew install redis
brew services start redis
```

### Ubuntu (APT)

```bash
sudo apt-get update
sudo apt-get install -y redis-server
sudo systemctl restart redis.service
```

### Verify Redis

```bash
redis-cli ping
```
Expected output: PONG

### Optional Fix (bcrypt Error)

If you encounter bcrypt issues, run:

```bash
pip install 'passlib>=1.7.4' 'bcrypt>=4.1.2,<5'
```

### Start the Backend

```bash
python app.py
```

## Step 5: Frontend Setup (Third Terminal Window)

The frontend provides the ResCanvas web UI.

```bash
cd frontend/
nvm install --lts
nvm use --lts
npm i -g npm
npm install
npm start
```

The app should now be running at `http://localhost:[...]`

---
