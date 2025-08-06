<div align="center">
  <a href="README-pt.md">Leia este documento em Português 🇧🇷</a>
</div>

---

# 🏥 Clínica Evolution - Clinic Management System

<p align="center">
  <img src="https://img.shields.io/badge/Status-Maintained-green?style=for-the-badge" alt="Status"/>
  <img src="https://img.shields.io/badge/Type-Portfolio_Project-blue?style=for-the-badge" alt="Type"/>
  <img src="https://img.shields.io/badge/Architecture-Dockerized-blueviolet?style=for-the-badge" alt="Architecture"/>
  <img src="https://img.shields.io/badge/CI/CD-Implemented-green?style=for-the-badge" alt="CI/CD Implemented"/>
</p>

A complete solution for clinic management, featuring smart scheduling, an integrated electronic health record, and real-time communication. This repository contains the architectural documentation and the configuration for the local development environment.

**Note:** This repository contains **only the architecture and configuration files**. The application's source code is in a private repository. The purpose of this project is to demonstrate system design and local deployment skills using Docker.

---

## ✨ Key Features

*   **📅 Smart Scheduling:** Calendar view (month/week/day) and list view, with status-based colors, real-time editing, and advanced filters.
*   **👥 Multi-Profile User Management:** Four access levels (Admin, Receptionist, Professional, Patient) with secure authentication (CPF/Email) and CAPTCHA protection.
*   **📄 Electronic Health Record (EHR):** Integrated **ONLYOFFICE Docs** editor for DOCX/XLSX files, with version control and folder organization.
*   **🔔 Real-Time Communication:** Instant notifications via WebSockets (Socket.IO) for important platform events.

---

## 🚀 Quick Start Guide (Development Environment)

The environment is 100% containerized with Docker, ensuring a quick and consistent setup.

**1. Clone the Repository:**
```
bash
git clone https://github.com/antonyandrade01/clinica-evolution-architecture.git
cd clinica-evolution-architecture
```

**2. Configure the Environment:**
Copy the example file. The default passwords are pre-configured for convenience.
```
bash
cp .env.example .env
```

**3. Launch the Services:**
This command will build, download, and start all necessary containers.
```
bash
docker-compose up -d --build
```

**4. Load Demo Data (Optional):**
To populate the system with test data, run the command below to restore the default backup.
```
bash
docker exec -i clinica_mariadb_db mariadb -u evolution -p'#!Ev0_Lm90=4M4' clinica_evolution < backup.sql
```

**5. Access the Interfaces:**
*   **Main Application:** 👉 **[http://localhost:5000](http://localhost:5000)**
*   **Database GUI (Adminer):** 👉 **[http://localhost:8082](http://localhost:8082)**
*   **ONLYOFFICE Server:** 👉 **[http://localhost:8081](http://localhost:8081)**

---

## 🔑 Access Credentials (Test Data)

| Role          | Email                   | Password   |
|---------------|-------------------------|------------|
| Administrator | `admin@clinica.com`     | `senha123` |
| Receptionist  | `recepcao@clinica.com`  | `recep123` |
| Professional  | `maria@clinica.com`     | `maria123` |
| Patient       | `joao@exemplo.com`      | `joao123`  |

---

## 🔧 Maintenance & Operations

### Database Backup and Restore

- **Create a Backup:**
  ```
  bash
  docker exec clinica_mariadb_db mariadb-dump -u evolution -p'#!Ev0_Lm90=4M4' clinica_evolution > backup_$(date +%d-%m-%Y).sql
  ```
- **Restore from a Backup:**
  ```
  bash
  docker exec -i clinica_mariadb_db mariadb -u evolution -p'#!Ev0_Lm90=4M4' clinica_evolution < backup_filename.sql
  ```

### Useful Docker Commands
| Action                     | Command                                      |
|----------------------------|----------------------------------------------|
| **Stop all services**      | `docker-compose down`                        |
| **View application logs**  | `docker-compose logs -f flask_app`           |
| **Restart the application**| `docker-compose restart flask_app`           |

---

## 🛠️ Architecture & Technologies

#### Container Architecture Diagram
<p align="center">
  <img src="https://github.com/antonyandrade01/clinica-evolution-architecture/blob/main/docs/images/arquitetura.png?raw=true" alt="Container Architecture Diagram" width="80%">
</p>

| Layer          | Technologies                                                                 |
|----------------|------------------------------------------------------------------------------|
| **Backend**    | Python 3, Flask, SQLAlchemy, Flask-SocketIO, Gunicorn                        |
| **Frontend**   | Bootstrap 5, JavaScript, FullCalendar.js, SweetAlert2                        |
| **Database**   | MariaDB                                                                      |
| **Infrastructure** | Docker, Docker Compose                                                     |
| **Integrations** | ONLYOFFICE Docs, Redis, RabbitMQ, Adminer                                  |

---
### DevOps Optimizations and Patterns Implemented

Beyond the basic architecture, this project incorporates professional DevOps patterns to ensure security, efficiency, and automation:

*   **🚀 Optimized Images with Multi-Stage Builds:** The Flask application's Docker image is built using a multi-stage process. This separates build-time dependencies from runtime, resulting in a significantly smaller final image.

*   **🛡️ Enhanced Security with Non-Root User:** The containerized application runs under a dedicated user (`appuser`) instead of as `root`. This is a security best practice to mitigate risks in case of a vulnerability.

*   **⚙️ Automated Backups with Ofelia:** A dedicated service (`Ofelia`) has been added to orchestrate automated, scheduled backups of the database and data volumes, using `rclone` to push archives to cloud storage (Google Drive), including a smart retention policy.

*   **▶️ Robust Initialization with Entrypoint:** An `entrypoint.sh` script ensures that essential tasks, such as applying database migrations (`flask db upgrade`) and fixing volume permissions, are executed before the main application starts, preventing errors and ensuring environment consistency.

*   **🔄 Continuous Integration with GitHub Actions:** An automated CI pipeline builds and pushes the Docker images for both the backend (Flask) and frontend (Next.js) in parallel upon every push to the `main` branch. It utilizes GitHub Secrets for secure authentication with Docker Hub and tags images with both `latest` and the commit SHA for versioning and traceability.

        
<details>
  <summary>Click to view the <code>ci.yml</code> workflow</summary>

  ```yaml
  # .github/workflows/ci.yml
  name: CI - Build e Push das Imagens Docker

  on:
    push:
      branches: [ "main" ]
    workflow_dispatch:

  jobs:
    build-and-push-backend:
      runs-on: ubuntu-latest
      steps:
        - name: Checkout do repositório
          uses: actions/checkout@v4

        - name: Configurar Docker Buildx
          uses: docker/setup-buildx-action@v3

        - name: Login no Docker Hub
          uses: docker/login-action@v3
          with:
            username: ${{ secrets.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

        - name: Build e Push da imagem do Backend
          uses: docker/build-push-action@v5
          with:
            context: .
            file: ./docker/Dockerfile.flask
            push: true
            tags: antonyandrade/clinica-evolution-backend:latest, antonyandrade/clinica-evolution-backend:${{ github.sha }}

    build-and-push-frontend:
      runs-on: ubuntu-latest
      steps:
        - name: Checkout do repositório
          uses: actions/checkout@v4

        - name: Configurar Docker Buildx
          uses: docker/setup-buildx-action@v3

        - name: Login no Docker Hub
          uses: docker/login-action@v3
          with:
            username: ${{ secrets.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

        - name: Build e Push da imagem do Frontend
          uses: docker/build-push-action@v5
          with:
            context: ./frontend
            file: ./frontend/Dockerfile.frontend
            push: true
            tags: antonyandrade/clinica-evolution-frontend:latest, antonyandrade/clinica-evolution-frontend:${{ github.sha }}

```

</details>


<details>
  <summary>Click to view the optimized Dockerfile.flask</summary>

  ```dockerfile
# --- Build Stage ---
FROM python:3.13.5-alpine3.22 AS builder

# Set the working directory
WORKDIR /app

# Install system dependencies
RUN apk add --no-cache gcc musl-dev linux-headers

# Virtual environment to isolate application dependencies
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Install dependencies in the virtual environment
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code
COPY . .

# --- Runtime Stage ---
FROM python:3.13.5-alpine3.22 AS runtime

# Create a non-root user and group
RUN apk add --no-cache su-exec curl && \
  addgroup -S appgroup && \
  adduser -S appuser -G appgroup

# Set the working directory
WORKDIR /app
COPY --from=builder /opt/venv /opt/venv

# Copy the application code from the build stage
COPY --from=builder --chown=appuser:appgroup /app /app

# Copy the entrypoint script into the image
COPY ./docker/entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]

# Set environment variables to use the virtual environment
ENV PATH="/opt/venv/bin:$PATH"
ENV PYTHONUNBUFFERED=1

EXPOSE 5000

CMD ["gunicorn", "--workers", "2", "--worker-class", "eventlet", "--bind", "0.0.0.0:5000", "app:app"]
```
</details>

---
## 👤 Contact

Created by **Antony Andrade** - Let's connect!

<p>
  <a href="https://github.com/antonyandrade01" target="_blank"><img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/></a>
  <a href="https://www.linkedin.com/in/antony-andrade-a04b92b7/" target="_blank"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/></a>
</p>

