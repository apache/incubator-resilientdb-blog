---
layout: article
title: "Charitap: Transforming Spare Change into Social Impact"
author: Aman_Dhairye_Himanshu
tags: ResilientDB Blockchain Donation Micro-donation SmartContract
aside:
  toc: true
article_header:
  type: overlay
  theme: dark
  background_color: "#203028"
  background_image:
    gradient: "linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))"
---

# Charitap: Transforming Spare Change into Social Impact

Giving back shouldn't be difficult, yet many potential donors are held
back by a lack of transparency, and the friction of complicated donation
processes. Traditional models often pressure individuals to make large,
one-time contributions, which can feel overwhelming and lead to a total
deterrent from regular giving.

**Charitap** changes this narrative by making philanthropy effortless.

**Useful Links**

Website is live at:
[https://charitap-frontend.vercel.app](https://charitap-frontend.vercel.app)

Code Repository:
[https://github.com/dhairye/Charitap](https://github.com/dhairye/Charitap)

Presentation Slides:
[https://github.com/dhairye/Charitap/blob/chrome-extension/Presentation%20-%20Charitap.pdf](https://github.com/dhairye/Charitap/blob/chrome-extension/Presentation%20-%20Charitap.pdf)

Demo:
[https://drive.google.com/file/d/1hs5s6Y1NnB4djBBSIoU4bPaFRvC6t5Mp/view?usp=sharing](https://drive.google.com/file/d/1hs5s6Y1NnB4djBBSIoU4bPaFRvC6t5Mp/view?usp=sharing)

### **The Motivation: Why We Built This**

Many people genuinely want to help but struggle to choose charities
wisely or track exactly where their funds go.

**The \"Milk\" Use Case: Low-Pressure Philanthropy** Imagine the
simplicity of a routine trip to the grocery store. You buy a gallon of
milk for **\$3.60**. In a traditional setting, you might be asked to
donate \$5 or \$10 at the register, which feels like a \"pressure
point.\"

With Charitap, you don't even have to think about it. The system
automatically detects the purchase and \"rounds up\" the transaction to
**\$4.00**. That **\$0.40** difference, essentially just \"spare
change\" is quietly set aside. You walk away with your milk, knowing
you've contributed to a cause you love without it ever impacting your
daily budget or requiring a conscious decision at the checkout line. It
turns the act of living into an act of giving.

### **The Charitap Journey: From Checkout to Change**

Step 1: Users sign up or log in to their personal impact portal. This is
where your journey of giving begins.

![](ssets/images/2026-25-2-Charitap/image7.png)

**Seamless signup experience with Google OAuth**

Step 2: Charitap works silently in the background. When you visit any of
the millions of supported e-commerce sites and reach the checkout, the
extension springs to life.

![](ssets/images/2026-25-2-Charitap/image4.png)

**Charitap automatically detects your cart total on sites like Amazon or
Shopify and offers to round up your spare change.**

Step 3: One-Click Impact & Celebration With a single click on the \"✓\"
button, your donation is processed. To celebrate your kindness, the
screen bursts with confetti, providing instant confirmation that you've
made a difference.

![](ssets/images/2026-25-2-Charitap/image3.png)

**Making the world better, one cent at a time**

Step 4: Head back to the Charitap dashboard to see the big picture.
Here, you can track your total donations, see how many \"round-ups\"
you've completed, and view your impact trends over time.

![](ssets/images/2026-25-2-Charitap/image2.png)

**Your personal command center for tracking the power of your collective
kindness.**

Step 5: The Activity page provides a detailed Ledger of every
transaction. Each entry is linked to a ResilientDB blockchain ID,
providing 100% proof of your donation.

![](ssets/images/2026-25-2-Charitap/image5.png)

**Every cent is logged on an immutable blockchain for you to verify.**

Step 6: Nominate your favorite charities, update your profile, and
manage your account to ensure your philanthropy is as personal as it is
impactful.

![](ssets/images/2026-25-2-Charitap/image1.png)

**Customizing your experience to support the causes that matter most to
you.**

### **Core Features at a Glance**

-   **Universal Compatibility:** Charitap is built with a sophisticated
  detection engine that automatically recognizes checkout and
  payment pages across millions of websites. Whether you are
  shopping on Amazon, Shopify-powered boutiques, or eBay, Charitap
  intelligently identifies the moment you're about to pay and offers
  a way to give back.

-   **Blockchain-Backed Trust:** In an era where trust is everything,
  Charitap leads with transparency. Every single donation is
  recorded on ResilientDB, a high-performance blockchain. This
  ensures that every cent you donate is immutable and traceable,
  providing total peace of mind that your contribution is reaching
  its intended destination.

-   **Premium "Glassmorphic" UI/UX:** The extension features a premium
  floating widget designed with modern glassmorphism and vibrant
  gradients. It stays out of your way until it's needed, appearing
  only during the final steps of your purchase with smooth, subtle
  animations.

-   **Privacy-First Filtering & Smart Cooldowns:** Charitap respects
  your digital space. It is programmed to exclude non-shopping sites
  like Instagram, Facebook, and Gmail to prevent intrusive popups.
  Furthermore, it features an intelligent cooldown system; if you
  choose to skip a donation, the widget politely stays hidden for an
  hour to avoid \"notification fatigue.\"

-   **Real-Time Impact Dashboard:** Connected to a robust backend,
  Charitap tracks your lifetime impact. Users can see their total
  contributions, the number of \"round-ups\" completed, and their
  verified blockchain transaction history, allowing you to see the
  tangible weight of your collective kindness over time.

### **Technical Deep Dive: The Hybrid Architecture**

![](ssets/images/2026-25-2-Charitap/image6.jpg)

Charitap is a multi-layered system designed to bridge the gap between
traditional finance (fiat) and decentralized ledgers.

#### **1. The Frontend Stack**

-   **ReactJS & Tailwind CSS:** Powers the responsive dashboard for a
  modern, high-performance user experience.

-   **Google OAuth & JWT:** Ensures secure, one-tap login for users.

-   **Chrome Extension (Manifest v3):** Built to be lightweight and
  secure, monitoring DOM elements on specific retail sites to
  capture transaction totals.

#### **2. The Server Engine**

-   **Node.js & Express:** The central nervous system that coordinates
  between the extension, the database, and the blockchain.

-   **Node-Cron:** A scheduled processor that \"collects\" the small
  round-up entries. To minimize transaction fees on the payment
  side, this job batches your change until it reaches a threshold
  before triggering a transfer.

-   **MongoDB:** Stores off-chain metadata like user preferences and
  charity profiles for lightning-fast retrieval on the dashboard.

#### **3. The Financial & Blockchain Layer**

-   **Stripe Connect:** We use Stripe's enterprise-grade infrastructure
  to handle the actual movement of money from your bank to the
  charity.

-   **ResilientDB (The Truth Layer):** While Stripe handles the USD,
  ResilientDB provides the **Proof**. Every donation is logged via a
  **GraphQL** endpoint to an immutable KV store, creating a
  permanent audit trail.

### **Beyond Logging: Smart Contracts & Verification**

While ResilientDB provides an immutable log of \"what happened,\"
Charitap utilizes **Smart Contracts** to add a layer of functional logic
and automated receipts.

#### **ResContract: The Digital Receipt System**

In the Charitap ecosystem, every donation isn't just a number; it's a
verifiable event. We use **ResContract** to mint **Digital Donation
Receipts**:

-   **Automated Minting:** The moment Stripe confirms a transfer, the
  server triggers a smart contract on the ResilientDB chain.

-   **Mathematical Integrity:** The contract ensures that the total
  \"Impact\" displayed on your dashboard matches the actual state
  recorded on the blockchain. This prevents any central authority
  (including us) from altering your donation history or misreporting
  figures.

### **Steps to Run the System**

#### **Prerequisites**

-   **Node.js** (v16+)

-   **MongoDB** (Local or Atlas)

-   **ResilientDB instance** (Local or Remote)

-   **Stripe Account** (Test mode keys)

#### **Installation**

1.  **Clone the repository** git clone
  https://github.com/dhairye/Charitap.git

2.  **Install Frontend Dependencies** cd src\
  npm install

3.  **Install Backend Dependencies** cd backend\
  npm install

#### **Environment Setup**

Create .env files and add the necessary keys:

-   MONGODB_URI

-   STRIPE_SECRET_KEY

-   RESILIENTDB_URL

-   (Other necessary environment variables, refer to the example env)

#### **Run the Application**

-   **Backend:** cd backend && npm run dev

-   **Frontend:** npm start (from root)

### **Contributions**

-   **Aman Dwivedi:** Built the Chrome Extension and the roundup logic
  for logging user transactions into the database. Integrated Smart
  Contracts to mint donation receipts for user-charity transactions.

-   **Dhairye Gala:** Constructed the NodeJS server, providing API
  endpoints for the frontend and Chrome extension. Setup the payment
  flow from the user bank account to the charity's account using
  Stripe Connect.

-   **Himanshu Nimonkar:** Engineered the React/Tailwind dashboard with
  real-time Chart.js visualizations for tracking donation impact.
  Integrated Google OAuth, Stripe, and a GraphQL-powered ResilientDB
  public ledger for immutable transaction transparency.

### **Our Mission**

Developed at the **Expolab (UC Davis)** by Aman Dwivedi, Dhairye Gala,
and Himanshu Nimonkar, Charitap is a proof-of-concept for the future of
\"Resilient\" finance. Under the guidance of **Professor Mohammad
Sadoghi**, we are proving that even the smallest change can change the
world when it is backed by a foundation of transparency and
high-performance distributed systems.

**Ready to turn your shopping into a force for good?** Start small, give
big with Charitap.
