<div align="center">
  <a href="README-pt.md">Leia este documento em Português 🇧🇷</a>
</div>

# 🏥 Clínica Evolution - System Architecture

<p align="center">
  <img src="https://img.shields.io/badge/Status-Maintained-green?style=for-the-badge" alt="Status"/>
  <img src="https://img.shields.io/badge/Type-Portfolio_Project-blue?style=for-the-badge" alt="Type"/>
  <img src="https://img.shields.io/badge/Architecture-Dockerized-blueviolet?style=for-the-badge" alt="Architecture"/>
</p>

This repository contains the architectural documentation and local deployment configuration for 'Clínica Evolution', a full-stack, containerized web application for clinic management.

**Note:** This repository contains **only the architecture and configuration files**. The application's source code is in a private repository. The purpose of this project is to demonstrate system design and local deployment skills using Docker.

---

## ✨ Key Features of the Application

*   **📅 Smart Scheduling:** Calendar view (month/week/day) and list view, real-time editing, and advanced filters.
*   **👥 Multi-Profile User Management:** Four access levels (Admin, Receptionist, Professional, Patient) with secure authentication.
*   **📄 Electronic Health Record (EHR):** Integrated **ONLYOFFICE Docs** editor for DOCX/XLSX, with version control.
*   **🔔 Real-Time Communication:** Instant notifications via WebSockets (Socket.IO).

---

## 🛠️ Architecture & Tech Stack

This project is designed as a multi-service containerized application, orchestrated with Docker Compose.

#### Container Architecture Diagram
*The following diagram illustrates the services and their interactions within the Docker network.*
<p align="center">
  <img src="https://github.com/antonyandrade01/clinica-evolution-architecture/blob/main/docs/images/arquitetura.png?raw=true" alt="Docker Architecture Diagram" width="80%">
</p>

| Layer          | Technologies                                                                 |
|----------------|------------------------------------------------------------------------------|
| **Backend**    | Python 3, Flask, SQLAlchemy, Flask-SocketIO, Gunicorn                        |
| **Frontend**   | Bootstrap 5, JavaScript, FullCalendar.js, SweetAlert2                        |
| **Database**   | MariaDB                                                                      |
| **Infrastructure** | Docker, Docker Compose                                                     |
| **Integrations** | ONLYOFFICE Docs, Redis, RabbitMQ, Adminer                                  |

---

## 🚀 Local Development Setup Guide

The entire environment is 100% containerized, ensuring a quick and consistent setup.

**1. Clone this repository:**
```bash
git clone https://github.com/antonyandrade01/clinica-evolution-architecture.git
cd clinica-evolution-architecture```


**2. Configure the Environment:**
Copy the example environment file. The default values are pre-configured for local development.
```bash
cp .env.example .env
```

**3. Launch the Services:**
This command will build the necessary images and start all containers.
```bash
docker-compose up -d --build
```

---
## 👤 Contact

Created by **Antony Andrade** - Let's connect!

<p>
  <a href="https://github.com/antonyandrade01" target="_blank"><img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/></a>
  <a href="https://www.linkedin.com/in/antony-andrade-a04b92b7/" target="_blank"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/></a>
</p>
