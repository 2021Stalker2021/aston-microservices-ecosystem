# Aston Microservices Ecosystem

A containerized microservices architecture built with Java and Spring Cloud, orchestrated via Docker Compose. The system demonstrates service discovery, centralized configuration, API gateway routing, and asynchronous event-driven communication.

## System Architecture

The ecosystem consists of five independent microservices and infrastructure components running in an isolated Docker network:

* **[API Gateway](https://github.com)** (`Port 8080`) — Single entry point for external client requests, handling dynamic routing via Eureka.
* **[Discovery Server](https://github.com)** (`Port 8761`) — Netflix Eureka server for service registration and discovery.
* **[Config Server](https://github.com)** (`Port 8888`) — Spring Cloud Config server providing centralized external configuration (`native` profile).
* **[User Service](https://github.com)** — Core business service managing user CRUD operations, backed by PostgreSQL (`Port 5433`) and publishing domain events to Kafka.
* **[Notification Service](https://github.com)** — Asynchronous consumer listening to Apache Kafka topics (`Port 9092`) to process user event notifications.

## Tech Stack

* **Backend:** Java, Spring Boot, Spring Cloud (Gateway, Netflix Eureka, Config)
* **Messaging:** Apache Kafka, Zookeeper
* **Database:** PostgreSQL 15
* **Infrastructure & DevOps:** Docker, Docker Compose (with custom healthchecks and startup dependency management)

## Getting Started

### Prerequisites
* Docker & Docker Compose installed
* Git

### Installation & Run

1. **Clone this infrastructure repository:**
   ```bash
   git clone https://github.com
   cd aston-microservices-ecosystem
   ```

2. **Clone all microservice repositories into the root directory:**
   ```bash
   git clone https://github.com.git
   git clone https://github.com.git
   git clone https://github.com.git
   git clone https://github.com.git
   git clone https://github.com.git
   ```

3. **Prepare configuration directory:**
   Ensure your service `.yml` / `.properties` files are placed inside the `./config-repo` folder (mounted to `/config-repo` inside the Config Server container).

4. **Build and start the ecosystem:**
   ```bash
   docker compose up -d --build
   ```

### Startup Order & Healthchecks
The `docker-compose.yaml` enforces a strict startup sequence using `healthcheck` probes (`nc -z`) and `depends_on` conditions:
1. `postgres`, `zookeeper`, `kafka`, and `discovery-server` start first.
2. `config-server` waits until `discovery-server` passes its healthcheck on port `8761`.
3. `user-service`, `notification-service`, and `api-gateway` wait until `config-server` is healthy on port `8888` before initializing.

## Useful Endpoints
* **API Gateway:** `http://localhost:8080`
* **Eureka Dashboard:** `http://localhost:8761`
* **Config Server:** `http://localhost:8888`
