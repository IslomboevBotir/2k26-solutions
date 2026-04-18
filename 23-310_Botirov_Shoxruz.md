Project: Language Testing Platform (LTP)

Stage 1. Requirements

Functional Requirements
1. User Authentication & Profiles: The system must allow students and examiners to create accounts and manage language proficiency levels.
2. Test Management: Examiners must be able to create and categorize tests (A1-C2) and modules (Reading, Listening, Writing, Speaking).
3. Real-time Examination: The system must provide a timed interface for students to take tests with audio recording support.
4. Automated & Manual Scoring: The system must automatically grade multiple-choice questions and allow manual grading for speaking/writing.
5. Results & Analytics: Students must be able to view score reports and progress charts.

Non-Functional Requirements
1. Availability: The platform should have 99.9% uptime during scheduled examination windows.
2. Scalability: The system must handle up to 5,000 concurrent students during peak hours.
3. Security (Data in Transit): All client-to-service and service-to-service traffic must be encrypted using TLS/SSL.
4. Security (Data at Rest): Stored student data, exam answers, and audio recordings must be encrypted at rest, with encryption keys managed securely.
5. Low Latency: Audio streaming and recording latency must be under 200ms.
6. Reliability: The system must autosave student progress every 30 seconds to prevent data loss.


Stage 2. Architecture

Option 1: Monolithic Architecture
In this approach, all components (Auth, Testing, Scoring) are part of a single codebase.

Advantages: Simple to develop and lower latency for internal communication.
Disadvantages: Hard to scale individual modules and a single bug can crash the entire system.

Option 2: Microservices Architecture
The system is split into independent services (User Service, Test Service, Media Service) communicating via APIs.

Advantages: Independent scaling for heavy modules (like Audio) and better fault isolation.
Disadvantages: Higher operational complexity and infrastructure costs.

Recommendation
Option 2 (Microservices) is better because testing platforms have "spiky" traffic. It allows us to scale the "Test Delivery" service independently during exam sessions without wasting resources on other parts of the system.
