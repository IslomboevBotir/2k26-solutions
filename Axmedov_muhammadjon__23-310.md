# Second-hand goods marketplace - Axmedov Muhammadjon 23-310

## 1. REQUIREMENTS

### 1.1 Functional Requirements

#### User Authentication:

Users must be able to register in the system with email and password
Users must be able to login with valid credentials
Users must have the ability to logout from the system
Password recovery option must be provided through email
User profiles must be manageable and editable

#### Product Management:

Sellers must be able to add new products with details
Product information including title, description, price must be editable
Products must be deletable by sellers
Product images must be uploadable and manageable
Product inventory and stock levels must be tracked

#### Product Catalog:

Customers must be able to view all available products
Product search functionality must be available and responsive
Filtering by product categories must be supported
Filtering by price range must be supported
Detailed product information must be viewable with descriptions and reviews
Product ratings and reviews must be displayable

#### Purchase Process:

Customers must be able to add products to shopping cart
Cart management features including add, remove, and update quantity
Registered users must be able to checkout and place orders
Multiple payment methods must be available including Click, PayMe, and Payme
Order status tracking must be available to customers
Order history must be accessible

#### Admin Panel:

Ability to view and manage all registered users
Product approval and rejection functionality
Order management and fulfillment
Report generation and analytics
User role and permission management
System configuration options

### 1.2 Non-Functional Requirements

#### Performance:

Website load time must be less than 2 seconds
API response time must be under 500 milliseconds
Database query execution must complete within 1 second
Cache optimization must be implemented for faster responses

#### Security:

Passwords must be hashed using secure algorithms
HTTPS and SSL certificates must be implemented
Protection against SQL Injection attacks
Cross-Site Scripting (XSS) attacks must be prevented
JWT or session-based authentication must be used
Admin panel access must be restricted to authorized users
Data encryption for sensitive information
Regular security audits and vulnerability assessments

#### Scalability:

System must handle minimum 10,000 concurrent users
Ability to add additional servers as needed
Database replication and failover support
Caching systems like Redis must be implemented
Load balancing must be configured

#### Responsive Design:

System must work properly on mobile devices
Optimized layout for tablet devices
Fully functional on desktop computers
Media query implementation for responsive behavior
Cross-browser compatibility

#### Availability and Reliability:

99.5 percent uptime guarantee required
24 hour per day, 7 day per week monitoring
Automatic backup system required
Disaster recovery plan must be documented and tested
Zero data loss tolerance

## 2. MONOLITHIC ARCHITECTURE

### 2.1 System Concept:

Monolithic architecture is a system design where all components including frontend, backend, and database are combined into a single application. The entire application runs on one server and shares a single codebase. This approach integrates all functionalities into one interdependent program.

### Tizim Sxemasi

```
┌─────────────────────────────────┐
│                                 │
│     MONOLIT APLIKATSIYA         │
│                                 │
│  ┌────────────────────────────┐ │
│  │  Frontend (HTML/CSS/JS)    │ │
│  └──────────┬─────────────────┘ │
│             │                   │
│  ┌──────────▼─────────────────┐ │
│  │  Backend (Laravel/Express) │ │
│  │  - Routes                  │ │
│  │  - Controllers             │ │
│  │  - Business Logic          │ │
│  └──────────┬─────────────────┘ │
│             │                   │
│  ┌──────────▼─────────────────┐ │
│  │   Database (MySQL)         │ │
│  │   - Users                  │ │
│  │   - Products               │ │
│  │   - Orders                 │ │
│  └────────────────────────────┘ │
│                                 │
└─────────────────────────────────┘
```

### 2.2 System Structure:

The monolithic system consists of three main layers:

Frontend Layer - HTML, CSS, and JavaScript components that users interact with through a web browser

Backend Layer - Application logic written in Laravel, Express, or similar frameworks that processes business rules and handles requests

Database Layer - SQL database like MySQL or PostgreSQL that stores all application data

### 2.3 How It Works

User sends request through browser to the frontend application
Frontend receives the request and processes it using HTML, CSS, and JavaScript
Frontend sends the request to the backend server
Backend server receives the request through defined routes
Business logic is executed to validate and process the request
Backend queries the database to retrieve or store data
Database returns results to the backend
Backend formats response in JSON or HTML format
Response is sent back to frontend
Frontend displays the response to the user

### 2.4 Advantages of Monolithic Architecture

Fast Development - Minimum Viable Product can be built quickly
Low Cost - Single server and database are sufficient
Simple Management - All code is located in one place making it easy to understand
Easy Debugging - The entire system can be run and tested on local machines
Ideal for Small Teams - Works perfectly for teams of 1 to 3 developers
Single Deployment - All changes are deployed together at once
Easy Testing - Integration testing is straightforward

### 2.5 Disadvantages of Monolithic Architecture

Becomes Complex When Scaled - Codebase becomes difficult to manage as the system grows
Low Scalability - Performance degrades under high traffic
Single Point of Failure - One system crash affects the entire application
Security Vulnerabilities - One vulnerability can compromise the whole system
Difficult Deployment - Even small changes require deploying the entire application
Team Coordination Issues - Merge conflicts increase as team size grows
Limited Scaling Options - Mostly restricted to vertical scaling only

### 2.6 When to Use Monolithic Architecture

Monolithic architecture is suitable for:

Startup projects with MVP phase
Small and medium-sized systems
Teams with 1 to 5 developers
Projects with limited budgets
Situations where fast development is critical
Applications with 10,000 to 100,000 users

## 3. MICROSERVICES ARCHITECTURE

### Tizim Sxemasi

```
┌─────────────────────────────────────────────┐
│                                             │
│            Frontend (React/Vue)             │
│                                             │
└─────────────────────────────────────────────┘
                     │
         ┌───────────▼───────────┐
         │   API Gateway         │
         │   (Kong/Nginx)        │
         └───────────┬───────────┘
                     │
        ┌────────────┼────────────┬──────────────┐
        │            │            │              │
        │            │            │              │
    ┌───▼──┐    ┌───▼──┐    ┌───▼──┐      ┌────▼─┐
    │ Auth │    │Product│    │ Order│      │Notify│
    │Service   │Service    │Service     │Service
    └───┬──┘    └───┬──┘    └───┬──┘      └────┬─┘
        │            │            │              │
        │            │            │              │
        └────────────┼────────────┴──────────────┘
                     │
        ┌────────────▼──────────────┐
        │   Message Queue           │
        │   (RabbitMQ/Kafka)        │
        └────────────┬──────────────┘
                     │
        ┌────────────┴──────────────┐
        │                           │
    ┌───▼──┐              ┌────────▼──┐
    │MySQL │              │  Redis    │
    │      │              │  (Cache)  │
    └──────┘              └───────────┘
```

### 3.1 System Concept

Microservices architecture is an approach where the system is divided into small independent services. Each service handles a specific business function and operates independently. Services communicate with each other through APIs and message queues rather than sharing a single database.

### 3.2 System Structure

The microservices system includes multiple components:

Frontend Layer - React, Vue, or Angular application for user interface

API Gateway - Kong, Nginx, or similar that routes requests to appropriate services

Auth Service - Handles user authentication and authorization
Product Service - Manages product catalog and product operations
Order Service - Manages customer orders and order processing
Payment Service - Handles payment processing and transactions
Notification Service - Sends email and SMS notifications

Message Queue - RabbitMQ or Kafka for asynchronous communication between services

Database - Each service may have its own database or share a common database

Cache Layer - Redis for caching frequently accessed data

### 3.3 How It Works

User sends API request through frontend application
API Gateway receives the request
Gateway examines the request and routes it to the appropriate service
The service receives and processes the request
If needed, the service queries its database
Services communicate with each other asynchronously using message queues
Response is sent back to the gateway
Gateway returns response to frontend application
Frontend displays the response to the user

### 3.4 Advantages of Microservices Architecture

High Scalability - Each service can be scaled independently based on demand
Independent Development - Different teams can develop different services simultaneously
Handles High Load - Can support 100,000 or more concurrent users
Fault Tolerance - Failure in one service does not crash the entire system
Faster Deployment - Individual services can be deployed without affecting others
Flexible Technology Stack - Each service can use different programming languages and frameworks
Better Monitoring - Each service can be monitored and optimized independently
Database Per Service - Each service can have its own database if needed

### 3.5 Disadvantages of Microservices Architecture

Complex Architecture - Designing and managing microservices is complicated
Higher Cost - Multiple servers and infrastructure required
Network Latency - Communication between services adds delay
Difficult Debugging - Debugging distributed systems is challenging
Complex Monitoring - Advanced monitoring and logging tools are required
Difficult Transactions - Managing transactions across multiple services is complex
Requires Large Team - Minimum 5 to 10 developers recommended
Longer Development Time - Takes longer to develop compared to monolithic approach

### 3.6 When to Use Microservices Architecture

Microservices architecture is suitable for:

Large-scale enterprise systems
Teams with 10 or more developers
Applications requiring different technology stacks
High traffic platforms with 100,000 to 1,000,000 users
Systems with frequent feature updates
Applications with independent scaling requirements

## 4. COMPARISON: MONOLITH VS MICROSERVICES

### 4.1 Development Time

Monolith - 1 to 3 months for MVP development
Microservices - 3 to 6 months for MVP development

### 4.2 Initial Cost

Monolith - Low cost with 1 to 2 servers required
Microservices - High cost with 5 to 10 servers required

### 4.3 Scalability Options

Monolith - Limited to vertical scaling only
Microservices - Supports both horizontal and vertical scaling

### 4.4 User Capacity

Monolith - Handles 10,000 to 100,000 users
Microservices - Handles 100,000 to 1,000,000 users

### 4.5 Team Size

Monolith - Suitable for 1 to 5 developers
Microservices - Requires 10 or more developers

### 4.6 Deployment Process

Monolith - Slower deployment of full application
Microservices - Faster deployment of individual services

### 4.7 Failure Impact

Monolith - Entire system affected by single failure
Microservices - Only affected service has issues

### 4.8 Monitoring Complexity

Monolith - Simple monitoring of single application
Microservices - Complex monitoring of multiple services

### 4.9 Testing Approach

Monolith - Straightforward integration testing
Microservices - Complex distributed testing

### 4.10 Team Coordination

Monolith - Easier coordination for small teams
Microservices - Requires formal coordination between teams

## 5. RECOMMENDED STRATEGY: MONOLITH FIRST

The recommended approach is to start with monolithic architecture for the following reasons:

Faster time to market for initial product release
Lower initial investment and infrastructure costs
Simpler team coordination and management
Easier to understand and maintain code
Faster feedback from users
Smaller initial team can handle development
