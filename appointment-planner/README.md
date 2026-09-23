# Appointment Planner

A containerized full-stack appointment booking application designed for small businesses and service providers.

The application allows customers to book available appointments online while providing operators with a dedicated dashboard for managing services, availability, and appointments.

This directory contains the **public project documentation and showcase material**. The application source code is maintained separately.

---

## Overview

Appointment Planner is a full-stack web application built around a simple booking workflow:

1. Select a service
2. Select a date
3. Select an available time slot
4. Enter customer details
5. Confirm the appointment

Operators can manage services, availability, and appointments through a dedicated dashboard.

The project focuses on practical full-stack development, containerization, cloud deployment, transactional email, and timezone-aware business logic.

---

## Screenshots

<table>
  <tr>
    <td align="center">
      <img src="./screenshots/login.png"
           alt="Login"
           style="width: 400px; height: 130px;">
      <br>
      <b>Login</b>
    </td>
    <td align="center">
      <img src="./screenshots/booking.png"
           alt="Booking"
           style="width: 400px; height: 130px;">
      <br>
      <b>Booking</b>
    </td>
  </tr>

  <tr>
    <td align="center">
      <img src="./screenshots/dashboard.png"
           alt="Dashboard"
           style="width: 400px; height: 130px;">
      <br>
      <b>Dashboard</b>
    </td>
    <td align="center">
      <img src="./screenshots/dashboard_2.png"
           alt="Dashboard 2"
           style="width: 400px; height: 130px;">
      <br>
      <b>Dashboard 2</b>
    </td>
  </tr>

  <tr>
    <td align="center">
      <img src="./screenshots/appointments.png"
           alt="Appointments"
           style="width: 400px; height: 130px;">
      <br>
      <b>Appointments</b>
    </td>
  </tr>
</table>

---

## Features

### Customer Booking

* Service selection
* Date selection
* Available appointment slots
* Customer name and email
* Booking confirmation
* Email confirmation after successful booking

### Operator Dashboard

* View appointments
* Manage services
* Configure availability
* Cancel appointments
* Receive booking notifications

### Backend

* REST API
* PostgreSQL persistence
* Prisma ORM
* Timezone-aware appointment handling
* AWS SES email integration

---

## Technology Stack

| Area             | Technology                   |
| ---------------- | ---------------------------- |
| Frontend         | React, TypeScript, Vite      |
| Backend          | Node.js, TypeScript, Fastify |
| Database         | PostgreSQL                   |
| ORM              | Prisma                       |
| Email            | AWS SES                      |
| Containerization | Docker, Docker Compose       |
| Cloud            | AWS EC2, IAM                 |
| AWS Region       | `eu-central-1`               |

---

## Architecture

The application follows a containerized architecture with separate frontend, backend, and database components.

```text
                    ┌──────────────────┐
                    │     Customer     │
                    │     Browser      │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │     Frontend     │
                    │ React
```

