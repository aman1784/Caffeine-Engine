# ☕ Caffeine Engine

A production-ready **RESTful backend** for a Coffee Shop application, built with **Spring Boot 3** and **Java 17**. This service powers ordering, payments, file storage, caching, and security for a modern coffee shop platform.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Project Structure](#project-structure)
- [API Documentation](#api-documentation)
- [Key Dependencies Explained](#key-dependencies-explained)
- [Build & Run](#build--run)
- [Testing](#testing)
- [Contributing](#contributing)
 
---

## Overview

`backend-coffee` is the server-side component of a Coffee Shop application. It exposes a suite of REST APIs for managing products, orders, users, payments, and media uploads. The application is built following a clean **Controller → Service → Repository** layered architecture and is designed for scalability and maintainability.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 17 |
| Framework | Spring Boot 3.5.3 |
| Build Tool | Gradle (Groovy DSL) |
| Database | MySQL |
| ORM | Spring Data JPA / Hibernate |
| Caching | Redis (Spring Cache) |
| Security | Spring Security 6.3.1 |
| Payment Gateway | Razorpay |
| File Storage | Dropbox SDK |
| API Docs | SpringDoc OpenAPI (Swagger UI) |
| Object Mapping | MapStruct 1.6.3 |
| Boilerplate Reduction | Lombok |
| Serialization | Gson 2.9.1 |
| ID Obfuscation | Hashids |
| Duplicate Detection | Guava Bloom Filter |
| Validation | Jakarta Bean Validation + Spring Validation |

---

## Architecture

The project follows a standard **three-tier layered architecture**:

```
┌─────────────────────────────────────────┐
│              Client / Frontend           │
└────────────────────┬────────────────────┘
                     │ HTTP Requests
┌────────────────────▼────────────────────┐
│         Controller Layer (REST API)      │
│  Handles request/response, routing,      │
│  input validation                        │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│           Service Layer                  │
│  Business logic, orchestration,          │
│  caching, external service calls         │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│         Repository Layer (JPA)           │
│  Database access, query execution        │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│              MySQL Database              │
└─────────────────────────────────────────┘
```

External integrations:
- **Redis** — caching layer (sits alongside the Service layer)
- **Razorpay** — payment processing (called from Service layer)
- **Dropbox** — media/file storage (called from Service layer)

---

## Features

- **Menu Management** — CRUD operations for coffee items and categories
- **Order Management** — Place, track, and manage customer orders
- **User Management** — Registration, login, profile management
- **Authentication & Authorization** — Secured endpoints via Spring Security
- **Payment Integration** — Razorpay payment order creation and verification
- **File Uploads** — Upload and retrieve images/assets via Dropbox
- **Caching** — Redis-backed caching for performance-critical reads
- **ID Obfuscation** — Hashids for hiding sequential database IDs in public APIs
- **Bloom Filter** — Efficient duplicate detection using Google Guava
- **API Documentation** — Interactive Swagger UI via SpringDoc OpenAPI
- **Input Validation** — Request body validation with Jakarta Bean Validation
- **Object Mapping** — Clean DTO ↔ Entity mapping with MapStruct

---

## Prerequisites

Make sure you have the following installed:

| Tool | Version |
|---|---|
| Java (JDK) | 17 or higher |
| Gradle | Bundled via `gradlew` wrapper |
| MySQL | 8.0+ |
| Redis | 6.0+ |

You will also need:
- A **Razorpay** account (Key ID + Key Secret)
- A **Dropbox** app (Access Token)

---

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/aman1784/backend-coffee.git
cd backend-coffee
```

### 2. Set Up MySQL Database

```sql
CREATE DATABASE coffee_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

---

## Configuration

### Environment Variables (Recommended for Production)

Instead of hardcoding secrets in `application.properties`, export them as environment variables:

```bash
export MYSQL_URL=jdbc:mysql://localhost:3306/coffee_db
export MYSQL_USER=root
export MYSQL_PASSWORD=secret
export RAZORPAY_KEY_ID=rzp_live_xxxxx
export RAZORPAY_KEY_SECRET=xxxxxxxxxxxx
export DROPBOX_ACCESS_TOKEN=sl.xxxxxxxxxx
export REDIS_HOST=localhost
export REDIS_PORT=6379
```

And reference them in `application.properties`:

```properties
spring.datasource.url=${MYSQL_URL}
spring.datasource.username=${MYSQL_USER}
spring.datasource.password=${MYSQL_PASSWORD}
razorpay.key.id=${RAZORPAY_KEY_ID}
razorpay.key.secret=${RAZORPAY_KEY_SECRET}
dropbox.access.token=${DROPBOX_ACCESS_TOKEN}
spring.data.redis.host=${REDIS_HOST}
spring.data.redis.port=${REDIS_PORT}
```

---

## Project Structure

```
backend-coffee/
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── coffee/
│   │   │           ├── CoffeeApplication.java       # Spring Boot entry point
│   │   │           ├── controller/                  # REST controllers
│   │   │           ├── service/                     # Business logic
│   │   │           ├── repository/                  # JPA repositories
│   │   │           ├── entity/                      # JPA entities (DB models)
│   │   │           ├── dto/                         # Data Transfer Objects
│   │   │           ├── mapper/                      # MapStruct mappers
│   │   │           ├── config/                      # Spring configs (Security, Cache, etc.)
│   │   │           ├── exception/                   # Custom exceptions & handlers
│   │   │           └── util/                        # Utility classes (Hashids, Bloom filter, etc.)
│   │   └── resources/
│   │       ├── application.properties               # App configuration
│   │       └── application-prod.properties          # Production overrides (optional)
│   └── test/
│       └── java/
│           └── com/
│               └── coffee/
│                   └── CoffeeApplicationTests.java  # Test suite
├── .gitattributes
├── .gitignore
├── build.gradle                                     # Gradle build configuration
├── gradlew                                          # Unix Gradle wrapper
├── gradlew.bat                                      # Windows Gradle wrapper
└── settings.gradle                                  # Root project name: 'coffee'
```

---

## API Documentation

Once the application is running, access the interactive Swagger UI at:

```
http://localhost:8080/swagger-ui.html
```

Raw OpenAPI spec (JSON) is available at:

```
http://localhost:8080/v3/api-docs
```
---

## Build & Run

### Run in Development Mode

```bash
./gradlew bootRun
```

### Run on Windows

```cmd
gradlew.bat bootRun
```

### Clean Build

```bash
./gradlew clean build
```

---

## Contributing

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Open a Pull Request

Please make sure all tests pass before submitting a PR.

---

## License

This project is open source.

---

*Built with ☕ and Spring Boot by [aman1784](https://github.com/aman1784)*
