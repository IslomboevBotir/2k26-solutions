# Internship Application Portal

The **Internship Application Portal** is a digital platform designed to bridge the gap between students seeking professional experience and companies looking for fresh talent.
It allows students to apply for internships, while companies can manage candidates through an organized dashboard.

### Functional Requirements

- **Profile Management**  
  Students can enter their skills, education, and portfolio.  
  Companies can create and manage their brand pages.

- **Internship Search & Filtering**  
  Students can search for internships using filters such as:
  - Field of study  
  - Technologies  
  - Location  
  - Work type (remote/onsite)

- **Resume Upload & Application**  
  Users can upload resumes (PDF) and apply with a single click.

- **Application Tracking**  
  Students can track application status:
  - Pending  
  - Under Review  
  - Accepted  
  - Rejected  

- **Notification System**  
  System sends email or push notifications when:
  - Application status changes  
  - New relevant internships are posted  

---

### Non-Functional Requirements

- **Security**  
  - AES-256 encryption for sensitive data  
  - Secure authentication (JWT / OAuth2)

- **Performance**  
  - Response time < 1 second  

- **Availability**  
  - High Availability

- **Usability**  
  - Works smoothly on web and mobile devices  

- **Data Integrity**  
  - Daily automated backups  
  - Protection against data loss  

---

## 2. Microservices Architecture

The system is built using a **microservices architecture**, divided into independent services:

- **Auth Service**
- **Job Service**
- **Application Service**
- **Notification Service**

These services communicate via:
- **API Gateway**
- Message Broker (e.g., RabbitMQ)

---

### Pros

- **High Scalability**  
  Each service can scale independently (e.g., search service during peak usage)

- **Fault Tolerance**  
  Failure in one service (e.g., Notification) does not break the entire system  

- **Technology Flexibility**  
  Different tech stack can be used:
  - Flutter (Frontend)  
  - .NET (Auth Service)  
  - Node.js (Realtime features)  

---

### Cons

- **Complexity**  
  Requires service discovery and inter-service communication setup  

- **Data Consistency Issues**  
  Hard to manage transactions across multiple databases  

- **Higher Cost**  
  Needs infrastructure like:
  - Docker  
  - Kubernetes  
  - Monitoring tools  

---

## Architecture Diagram

[![Architecture](https://drive.google.com/file/d/12Ow820s39YvUe_QZMz4H8czhtH6shqfO/view?usp=sharing)
