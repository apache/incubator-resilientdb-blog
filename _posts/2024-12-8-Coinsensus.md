
## **Coincensus: Split Bills and Track Balances**, Powered by ResilientDB

![Photo by [**GoMoney](https://gomoney.global/blog/worry-less-about-the-bill-when-you-split-it-with-gomoney/)**](https://cdn-images-1.medium.com/max/14396/1*j7LPyKLoHwY-u9iHAuyhdA.png)

Managing group expenses and financial transactions is often tedious, error-prone, and vulnerable to privacy breaches with traditional methods. Centralized platforms lack transparency, risk data breaches, and fail to provide secure, real-time updates. This creates a need for a decentralized, transparent, and user-friendly solution to ensure trust, automate calculations, and securely manage group expenses. Coinsensus addresses these challenges with blockchain technology and a seamless interface, redefining expense management.

### What is Coincensus?

Coinsensus, a blockchain-based bill management platform powered by ResilientDB, is designed to create a trustless and transparent system for expense tracking among friends and groups. By leveraging blockchain technology's decentralized and tamper-resistant nature, the project ensures secure and reliable monitoring of debts and lending without relying on a central authority.

Through integrating **ResilientDB¹**, a high-performance blockchain framework that uses Practical Byzantine Fault Tolerant Consensus Protocol (PBFT)² internally, the system achieves fault tolerance, low latency, and scalability, guaranteeing data integrity even in environments prone to network failures or malicious attacks.

![Photo by [**Apache ResilientDB ****(Incubating)](https://github.com/apache/incubator-resilientdb-site)**](https://cdn-images-1.medium.com/max/2000/1*1yJUI8me1QqUwrQAIlpYrQ.png)

Additionally, Coinsensus also automates balance calculations and updates while maintaining an immutable transaction history, addressing ineﬃciencies in manual record-keeping and vulnerabilities in centralized systems. This product aims to redefine financial technology by oﬀering a resilient, decentralized solution³ that prioritizes data privacy and transparency for the users.

### Why Coinsensus?

* **Strengthens Transaction Management with ResilientDB:** Our integration with ResilientDB significantly enhances Coinsensus’ ability to manage financial transactions with unparalleled trust and transparency. Leveraging ResilientDB’s decentralized framework, the platform ensures fault tolerance and robust data integrity, even during network disruptions and malicious activities. This integration provides users with a secure, resilient, and high-performance system, redefining the reliability of payment management.

* **Streamlined Group Expense Tracking:** The core of our application is a robust system for automatic and transparent expense sharing among group members or individuals. Each transaction is securely recorded on the blockchain, creating an immutable and tamper-proof ledger while enabling real-time balance updates. This eliminates the need for manual record-keeping, enhancing eﬃciency, accuracy, and trust within the group and individuals.

* **User-Friendly Interface:** Our frontend application, developed using ReactJS, delivers a seamless and engaging user experience. With an intuitive and user-friendly design, it empowers users to eﬃciently manage their transactions, whether it’s adding expenses, tracking real-time balances, or accessing a detailed transaction history. The application ensures smooth navigation and functionality, making group expense management eﬀortless and accessible for all users.

### Architecture Overview

![**Coincensus: Architecture Overview**](https://cdn-images-1.medium.com/max/2128/1*y_39ECQ_ZT0zxwlhXEPz9Q.png)

### Tools and Development

Coinsensus combines modern front-end frameworks, efficient backend solutions, and powerful blockchain technology to deliver a seamless and secure expense management platform. Here’s an overview of the tools that brought this product to life.

* **React with TypeScript**: Used to develop a highly interactive and scalable user interface, combining React’s flexibility with TypeScript’s type safety for robust front-end development.

* **Tailwind CSS**: Simplified the styling process with its utility-first approach, allowing for the creation of a sleek, responsive, and consistent UI design.

* **FastAPI**: Powered the backend with a high-performance API framework, ensuring efficient data handling and secure communication between the client and server.

* **ResilientDB**: Implemented as the blockchain framework, providing fault tolerance, scalability, and low-latency transaction management for secure and reliable expense tracking¹.

* **SQLite**: A second database used to store the public keys of all the users, which will be eventually used to update the transaction (money sent and received) from both ends.

* **GIT & GitHub**: Enabled version control and collaboration, ensuring smooth teamwork and reliable code management throughout the development process.

* **JIRA**: Facilitated task tracking and project management, helping the team stay organized, manage sprints, and deliver features efficiently over six sprints of one week each.

### Design Flow of Coincensus

![**Coincensus: Design Flow**](https://cdn-images-1.medium.com/max/2560/1*pxPYzDZbXl0Fwc3wjW1mRQ.jpeg)

### Coincensus Workflow

### 1. User Registration and Login

* **New Users**: Register an account by providing personal details, then proceed to the landing page after successful registration.
![Signup](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Yk5TACIHGSSonrrnzOmK2w.png)

* **Existing Users**: Log in with credentials to directly access the home page.

![Login](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*PbktXcCIfWSMoqFfnjfOBA.png)

### 2. Landing/ Home Page and Navigation

After the authentication procedure, the application redirects to the landing page where we can see recent transactions, add expenses, check groups, and settle payments. Access the **side menu**, which includes the following sections:

* **Activity**: View recent transactions.

* **Friends**: Add new friends and view balances related to friends.

* **Account**: Customize user settings like theme preferences and currency.
![**Landing page**](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*c4QZ2J2kWervSXkrrOr45w.png)

### 3. Activity Section

View a summary of recent financial transactions for quick updates.

![**Activities**](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*YzZ5ettDPiFnafQ55XbxpQ.png)

### 4. Friend Management

* **Add Friends**: Add friends to enable expense tracking and settlement.

* **View Balance for Friends**: Monitor individual balances with friends

* **Settle Up**: If a user decides to settle balances, process the payment directly. After payment, update the ledger.
![**Friend Management**](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*TwKPh3eb2tKfd3dT9pUmoQ.png)

![**Settle Balances**](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*AB7iUp6fnjdXC1BNucynBQ.png)
### 5. Adding and Splitting Expenses

* **Select Expense Type**: Choose a group for shared costs or choose specific friends for one-on-one expenses.

* **Enter Details**: Add expense specifics like amount and description.

* **Choose Split Type**: Distribute the amount evenly or allocate costs as per user input.

Save the transaction, which updates the blockchain ledger in real time.


![**Add Expense**](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*-yi7cm55G-qu0xn8oJW5ww.png)

### 6. Account Settings

* **Edit Profile**: Update user information like name and avatar.

* **Light/Dark Theme**: Toggle between themes for a personalized interface.

* **Currency Settings**: Customize currency preferences based on user location.
![**Account Settings**](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*AQO7pNzZvOOGMP5YuVwZGQ.png)
### 7. Transaction Processing

* Every action, such as adding an expense or settling balances, updates the tamper-proof blockchain ledger using ResilientDB. This ensures real-time synchronization and transparency. Each transaction is saved securely on the blockchain, ensuring an immutable and auditable history.

### 8. Caching

* Using lightweight fast access SQLite DB to cache frequently queried data to decrease latency.

### 9. Error Handling and Validation

* Include mechanisms to validate user input and resolve errors (e.g., insufficient funds or incorrect splits).

### Impact

* **Decentralized Trust and Data Security: **By using blockchain with ResilientDB, Coinsensus ensures secure, immutable transactions, reducing reliance on centralized platforms and minimizing the risk of data breaches, giving users more control over their financial data.

* **Efficient Financial Reconciliation: **Coinsensus automates balance and transaction calculations, eliminating manual reconciliation, reducing human error, and saving time for individuals and groups, leading to smoother financial settlements.

* **Encourages Financial Literacy: **By automating calculations and providing transparent transaction histories, Coinsensus promotes financial awareness and better decision-making, helping users improve their financial literacy.

### Project Demo

A short project video demo showcasing the implementation of **Coinsensus**:

Demo link: https://youtu.be/O51VAFmH8Vw

### Conclusion

<iframe src="[https://medium.com/media/6bdcdfdd687b804bc403f3b52a12294f](https://drive.google.com/file/d/1TXsNhkY_3_9bn-Zddv1HF1uEecSqdYJP/view?usp=sharing)" frameborder=0></iframe>

Coinsensus demonstrates the power of blockchain technology in finance and expense management. By combining decentralization, automation, and an intuitive interface, the platform resolves inefficiencies and builds trust among users. This project sets the stage for more resilient, secure, and transparent financial management solutions, offering a significant step forward in financial technology.

### **Future Work**

* **User Features: **Automate feature for real-time notifications for payment reminders and updates and facilitate connecting with friends in the application.

* **Integration and Interoperability:** Integrate Coincensus with third-party payment gateways for transaction settlements.

* **User Experience Enhancements:** Integrate a chatbot feature powered by NLP (Natural Language Processing), to assist users with bill management queries.

* **Advanced Security:** Develop fraud detection and reporting algorithms to flag malicious or suspicious activities while recording payment transactions.

* **Community Engagement:** Build and integrate features for group budgeting, enabling users to set financial goals and track progress collaboratively.

### References

[1] **Apache** **ResilientDB**: Global-Scale Sustainable Blockchain Fabric [https://resilientdb.apache.org/](https://resilientdb.apache.org/)
[2] Gupta S, Hellings J , Sadoghi M. (2021). Fault-Tolerant Distributed Transactions on Blockchain, Synthesis Lectures on Data Management, February 2021, Vol. 16, No. 1 , Pages 1–268
[[https://doi.org/10.2200/S01068ED1V01Y202012DTM065](https://doi.org/10.2200/S01068ED1V01Y202012DTM065)]
[3] Nakamoto, S. (2008). “Bitcoin: A Peer-to-Peer Electronic Cash System”
[[https://bitcoin.org/bitcoin.pdf](https://bitcoin.org/bitcoin.pdf)]
>  Thank you for exploring **Coincensus** with us! We encourage you to experiment with ResilientDB’s features in your projects too!

### Credits

Designed and Developed by:
Sankalp Kashyap [sankashyap@ucdavis.edu](mailto:sankashyap@ucdavis.edu) |
Rajaram Manohar Joshi  [rmjoshi@ucdavis.edu](mailto:rmjoshi@ucdavis.edu) |
Vijeth Kumbarahally Lakshminarayana  [kumbara@ucdavis.edu](mailto:kumbara@ucdavis.edu) |
Sanchit Kaul  [skkaul@ucdavis.edu](mailto:skkaul@ucdavis.edu) |
Shaik Haseeb Ur Rahman  [hrahman@ucdavis.edu](mailto:hrahman@ucdavis.edu) |
***University of California, Davis | Fall ’24***
>  **Go through the detailed code, [@coinsensus-frontend](https://github.com/joshirajaram/coinsensus-frontend) & [@coinsensus-backend](https://github.com/joshirajaram/coinsensus-backend)**

***Happy Blockchaining!***

Click here to check out more blogs on **ResilientDB**.
[**@blog.resilientdb](https://blog.resilientdb.com/)**
