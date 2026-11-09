# Aston Microservices Ecosystem

A containerized microservices architecture built with **Java 21**, **Spring Boot 3**, and **Spring Cloud**, orchestrated via **Docker Compose**. The system demonstrates dynamic service discovery, centralized configuration, reactive API gateway routing, fault tolerance, and asynchronous event-driven communication.

## System Architecture

The ecosystem consists of five independent microservices (built with Gradle) and infrastructure components running in an isolated Docker network:

* **[API Gateway](https://github.com/2021Stalker2021/api-gateway)** (`Port 8080`) — Single entry point built with reactive Spring Cloud Gateway (Netty), handling dynamic load-balanced routing (`lb://`) via Eureka.
* **[Discovery Server](https://github.com/2021Stalker2021/discovery-server)** (`Port 8761`) — Netflix Eureka server (`@EnableEurekaServer`) for dynamic service registration, heartbeat monitoring, and discovery.
* **[Config Server](https://github.com/2021Stalker2021/config-server)** (`Port 8888`) — Spring Cloud Config server (`@EnableConfigServer`) providing centralized external configuration (`native` profile).
* **[User Service](https://github.com/2021Stalker2021/user-service)** — Core business service managing user CRUD operations with Spring HATEOAS, MapStruct, and Swagger/OpenAPI, backed by **PostgreSQL 15** (`Port 5433`) and publishing domain events to Kafka.
* **[Notification Service](https://github.com/2021Stalker2021/notification-service)** — Asynchronous Kafka consumer and REST service sending SMTP emails, protected by **Resilience4j** (`@CircuitBreaker` with fallback methods).

## Tech Stack

* **Core & Frameworks:** Java 21 (LTS), Spring Boot (3.2.2 / 3.4.1), Spring Cloud (Gateway, Netflix Eureka, Config), Spring Web, Spring Data JPA, Spring HATEOAS
* **Messaging & Fault Tolerance:** Apache Kafka, Zookeeper, Resilience4j (Circuit Breaker)
* **Database & Mapping:** PostgreSQL 15, Hibernate, MapStruct, Lombok
* **Testing & Documentation:** JUnit 5, Testcontainers (PostgreSQL & Kafka), Springdoc OpenAPI (Swagger UI)
* **Build & Infrastructure:** Gradle, Docker, Docker Compose (with custom healthchecks and startup dependency management)

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
   git clone https://github.com
   git clone https://github.com
   git clone https://github.com
   git clone https://github.com
   git clone https://github.com
   ```

3. **Prepare configuration directory:**
   Ensure your service configuration files (`api-gateway.yml`, `user-service.yml`, `notification-service.yml`) are placed inside the `./config-repo` folder (mounted to `/config-repo` inside the Config Server container).

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
