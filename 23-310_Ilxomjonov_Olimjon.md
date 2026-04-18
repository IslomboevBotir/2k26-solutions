# System Requirements and Architecture
Student: Ilxomjonov Olimjon
Group: 23-310
Topic: Weather Alert Notification Service


# Stage 1. Requirements

 <!-- Project Overview -->
A Weather Alert Notification Service that monitors weather conditions and sends real-time alerts to users when dangerous or significant weather events are detected in their selected locations (e.g., storms, heavy rain, extreme heat, frost).


<!-- 5 functional requirements -->

1. User Registration & Location Setup — Users can create an account and set one or more locations they want to receive weather alerts for.

2. Weather Monitoring — The system continuously fetches weather data from an external weather API and checks for predefined alert conditions (e.g., temperature thresholds, wind speed, precipitation).

3. Alert Notification — When a condition is triggered, the system sends a notification to the user via their preferred channel (email, SMS, or push notification).

4. Alert Preferences — Users can configure which types of alerts they want to receive and set their own thresholds (e.g., "notify me if temperature drops below 0°C").

5. Alert History — Users can view a log of all past alerts they have received, including the time, location, and type of alert.


<!-- 5 non-functional requirements -->

1. Performance — Weather data should be checked and alerts dispatched within 2 minutes of a condition being detected.

2. Reliability — The system should have at least 99% uptime to ensure critical weather warnings are never missed.

3. Scalability — The system must be able to handle a growing number of users and locations without degrading performance.

4. Security — User data (accounts, location preferences) must be stored securely; passwords must be hashed and API communication encrypted via HTTPS.

5. Usability — The user interface for managing locations and preferences should be simple and require no more than 3 steps to set up an alert.


# Stage 2. Architecture

<!-- Option 1 — Monolithic Architecture -->


*System Diagram*
https://drive.google.com/file/d/1w-5osTsUJnLndEu_u55Mg51wz8IPggve/view?usp=sharing


All modules (user management, weather checking, alert sending) live inside a single application and share one database

*Advantages*
- Simple to develop, test, and deploy — everything is in one place
- Easier to manage for a small team or early-stage project
- No network overhead between components

*Disadvantages*
- Hard to scale individual parts — if the weather monitor is under load, the whole app is affected
- A bug in one module can crash the entire application
- Becomes harder to maintain as the codebase grows


<!-- Option 1 — Monolithic Architecture -->

*System Diagram*
https://drive.google.com/file/d/1MK_Ay6GWhuXvHPdBFHdEuu3CTn1-d-eu/view?usp=sharing

All services communicate via a Message Queue (e.g. RabbitMQ)

Each feature is a separate, independently deployable service that communicates through a message queue or REST API.

*Advantages*
- Each service can be scaled independently based on demand
- A failure in one service does not bring down the whole system
- Easier to update or replace individual services over time

*Disadvantages*
- More complex to set up and deploy (requires orchestration tools like Docker/Kubernetes)
- Communication between services adds latency and requires careful error handling
- Overkill for a small or early-stage project


# Which Option is Better and Why?
For the current stage of this project, *Option 1 (Monolithic Architecture)* is the better choice.

Since this is an early-stage academic project with a small scope and a single developer, the simplicity of a monolith allows faster development, easier debugging, and quicker deployment. There is no immediate need to scale individual components independently.

However, if the service were to grow into a production system with thousands of users across many regions, *Option 2 (Microservices)* would become the right long-term direction, as it provides the scalability and fault isolation that a critical notification system would require.
