# Appointment Planner

A containerized web application for **appointment scheduling and availability management**, designed for small businesses and service providers.

The application allows customers to view available appointment slots and book appointments online. Business operators can manage services, availability and appointments through a dedicated web interface.

The project demonstrates a complete application workflow from frontend and backend development through database persistence, containerization, cloud infrastructure and automated deployment.

---

## Screenshots

### Customer Booking

The customer-facing interface provides a simple booking workflow for selecting a service, date and available appointment time.

![Customer Booking](./screenshots/customer-booking.png)

### Business Management

The business interface provides tools for managing services, availability and appointments.

![Business Dashboard](./screenshots/business-dashboard.png)

### Appointment Management

Booked appointments can be reviewed and managed through the business interface.

![Appointment Management](./screenshots/appointment-management.png)

---

# Features

## Customer Booking

* View available services
* Select an appointment date
* View available time slots
* Book an appointment online
* Receive email notifications

Only currently available appointment slots are presented for selection. The backend also validates appointment availability to prevent overlapping bookings.

## Business Management

* Manage services
* Configure business availability
* View appointments
* Manage scheduled appointments
* Customer-specific configuration

## Scheduling

The scheduling system takes several factors into account:

* Service duration
* Business opening hours
* Existing appointments
* Appointment overlaps
* Business timezone
* Day-of-week availability

Date and time calculations are handled with timezone awareness to ensure that appointment times remain consistent between the application, server and customer.

---

# Architecture

The application uses a **containerized service architecture** deployed on an AWS EC2 instance.

![Application Architecture](./screenshots/architecture.png)

```text
                         Internet
                            │
              ┌─────────────┴─────────────┐
              │                           │
        Customer Interface         Business Interface
             Port 80                    Port 8080
              │                           │
              └─────────────┬─────────────┘
                            │
                         REST API
                        Port 3001
                            │
                 ┌──────────┴──────────┐
                 │                     │
            PostgreSQL              AWS SES
```

The current production deployment consists of **three Docker containers running on one EC2 instance**:

```text
AWS EC2
│
└── Docker Compose
    │
    ├── Frontend Container
    │      └── React / Vite / Nginx
    │
    ├── API Container
    │      └── Node.js / Fastify / Prisma
    │
    └── PostgreSQL Container
           └── Application Database
```

AWS SES is used as an external cloud service by the API container and is not part of the Docker stack.

---

# Components

## Frontend

React-based web application providing:

* Customer booking
* Service selection
* Appointment slot selection
* Business management

The frontend is built using Vite and served through Nginx inside the frontend container.

## Backend

Fastify-based REST API responsible for:

* Appointment management
* Availability calculation
* Service management
* Validation
* Database access
* Email integration

## PostgreSQL

PostgreSQL provides persistent storage for:

* Companies
* Services
* Availability schedules
* Appointments
* Customer information

## AWS SES

Amazon SES is used by the backend for transactional email delivery.

---

# Technology Stack

### Frontend

* React
* TypeScript
* Vite
* Nginx

### Backend

* Node.js
* Fastify
* TypeScript
* Prisma

### Database

* PostgreSQL

### Infrastructure

* Docker
* Docker Compose
* Linux
* AWS EC2
* AWS IAM
* AWS SES

---

# AWS Deployment

The application is currently deployed to an **AWS EC2 instance using Docker Compose**.

![AWS Deployment](./screenshots/aws-deployment.png)

The deployment intentionally uses a simple infrastructure setup suitable for the current MVP while keeping the individual application components separated into independent containers.

```text
                         AWS Cloud
                            │
                         EC2 Instance
                            │
                      Docker Compose
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
         Frontend         Backend      PostgreSQL
         Container        Container      Container
             │              │
             │              └──────────────► AWS SES
             │
        Port 80 / 8080
                            │
                        Port 3001
```

The current architecture keeps infrastructure requirements relatively small while providing clear separation between the presentation layer, API and database.

---

# Scalability

The current deployment runs all three containers on a **single EC2 instance**.

Scalability is therefore not implemented as horizontal scaling yet. However, the separation of the application into independent containers provides a foundation for future infrastructure changes.

For example, as demand increases, the architecture could evolve toward:

```text
                         Load Balancer
                              │
                 ┌────────────┴────────────┐
                 │                         │
             API Instance              API Instance
                 │                         │
                 └────────────┬────────────┘
                              │
                         PostgreSQL
                              │
                         AWS Services
```

Potential future infrastructure improvements include:

* AWS Application Load Balancer
* Multiple API containers
* Auto Scaling
* Amazon ECS / Fargate
* Amazon RDS for PostgreSQL
* CloudWatch monitoring
* Centralized logging
* HTTPS/TLS termination

The important architectural principle is that the application is **not dependent on a single monolithic process**. Frontend, API and database are already separated at container level, making future migration to managed cloud infrastructure possible without fundamentally changing the application structure.

---

# Containerization

The application runs as a Docker Compose stack:

```text
appointment-planner
├── frontend
├── api
└── postgres
```

Each service runs in its own Docker container.

This provides:

* Service isolation
* Reproducible deployments
* Consistent runtime environments
* Simplified local development
* Easy service management
* Clear separation of application responsibilities

The same containerized architecture is used during development and deployment.

---

# Deployment Automation

Deployment is handled through a dedicated deployment workflow.

```text
Customer Configuration
          │
          ▼
    Deployment Script
          │
          ├── Validate configuration
          ├── Configure EC2
          ├── Transfer application
          ├── Generate environment configuration
          ├── Build Docker images
          └── Start Docker Compose
                    │
                    ▼
              Running Application
```

Customer-specific configuration is kept separate from the application source.

This allows the same application to be deployed for different businesses with different:

* Company identities
* Timezones
* Email configuration
* Database configuration
* Application settings

The deployment process therefore provides a foundation for a **multi-customer deployment model** while keeping customer-specific configuration outside the public repository.

---

# API

The backend exposes a REST API for the main application functions.

```text
GET  /services
GET  /slots
GET  /appointments
POST /appointments
```

The `/slots` endpoint calculates available appointment slots based on configured availability and existing appointments.

Example:

```text
GET /slots?companyId=...&serviceId=...&date=...
```

The API returns only slots that can currently be booked.

---

# Availability Logic

Appointment availability is calculated dynamically.

```text
Business availability
        │
        ▼
Service duration
        │
        ▼
Generate possible slots
        │
        ▼
Check existing appointments
        │
        ▼
Detect overlapping appointments
        │
        ▼
Remove unavailable slots
        │
        ▼
Available appointment slots
```

Existing appointments are checked against the requested time range.

This prevents already-booked periods from being offered to customers.

The backend also validates the requested appointment when a booking is submitted, rather than relying solely on frontend validation.

---

# Database

PostgreSQL is used as the primary persistent data store.

The core data model contains entities for:

```text
Company
   │
   ├── Services
   │
   ├── Availability
   │
   └── Appointments
            │
            └── Customer information
```

Prisma provides the database access layer and type-safe interaction with PostgreSQL.

---

# AWS Services

The current deployment uses:

### Amazon EC2

Hosts the Docker Compose application stack.

### AWS IAM

Provides controlled authentication and authorization for AWS services.

### Amazon SES

Provides transactional email delivery for appointment notifications.

AWS services are integrated at application level without coupling the complete application architecture to a single AWS-specific runtime.

---

# Security & Privacy

This public project documentation intentionally excludes sensitive production information.

The public repository does not contain:

* AWS credentials
* Passwords
* API keys
* Private keys
* Production environment files
* Database dumps
* Real customer data
* Production secrets

Customer-specific configuration is kept outside the public repository.

AWS credentials are provided to the application through the AWS credential provider chain rather than being embedded in application source code.

---

# Project Status

**Status: Working MVP deployed on AWS**

The current implementation provides:

* Customer appointment booking
* Business management
* Dynamic availability calculation
* Timezone-aware scheduling
* Appointment overlap detection
* PostgreSQL persistence
* Docker-based deployment
* AWS infrastructure
* Transactional email notifications
* Customer-specific deployment configuration

---

# Future Development

Potential future improvements include:

* Outlook / Microsoft 365 calendar integration
* Calendar synchronization through ICS/HTTP feeds
* HTTPS with automated certificate management
* AWS Application Load Balancer
* Amazon RDS
* Horizontal API scaling
* Auto Scaling
* Centralized monitoring and logging
* CI/CD pipeline
* Improved authentication and authorization
* Multi-tenant infrastructure
* Infrastructure as Code

These improvements would allow the current MVP to evolve from a **single-EC2 Docker Compose deployment toward a more scalable cloud architecture** as business and traffic requirements grow.

---

# What This Project Demonstrates

This project combines several areas of practical software engineering and cloud infrastructure:

* Full-stack web development
* REST API design
* Database modeling
* Appointment scheduling logic
* Timezone-aware application design
* Docker & containerization
* Linux server administration
* AWS infrastructure
* IAM and cloud service integration
* Transactional email
* Deployment automation
* Customer-specific configuration
* Scalable application architecture
* Security-conscious handling of production credentials

