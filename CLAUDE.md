# CLAUDE.md - ECD-API

## Project Overview

ECD-API (Early Childhood Development API) is a backend service for the AMRIT platform that powers the ECD call centre module. It manages outbound calls to mothers/caregivers for early childhood development tracking, quality auditing of calls, call allocation, questionnaire management, and reporting.

## Tech Stack

- Java 17, Spring Boot 3.2.2, Maven
- Spring Data JPA / Hibernate, MySQL 8.0
- Redis for session management
- Lombok, MapStruct for boilerplate reduction
- SpringDoc OpenAPI (Swagger UI at `/swagger-ui.html`)
- ECS logging (logback-ecs-encoder)
- JaCoCo for test coverage, Checkstyle for code style
- Packaged as WAR for Wildfly deployment

## Build & Run

```bash
mvn clean install -DENV_VAR=local          # Build
mvn spring-boot:run -DENV_VAR=local        # Run locally
mvn -B package --file pom.xml -P <profile> # Package WAR (dev, local, test, ci, uat)
mvn test                                    # Run tests
```

Environment config: `src/main/resources/ecd_<ENV_VAR>.properties` is copied to `application.properties` at build time.

## Key Packages (`com.iemr.ecd`)

- **controller/** - REST endpoints organized by domain:
  - `associate/` - Beneficiary registration, call history, call closure, auto preview dialing
  - `callallocation/` - Call configuration and allocation to agents
  - `outboundworklist/` - Outbound worklist and call statistics
  - `quality/` - Quality auditing: sections, questions, grades, sample selection, charts, agent-auditor mapping
  - `questionare/` - ECD questionnaire management
  - `dataupload/` - Data upload and template management (RCH data)
  - `masters/` - Master data endpoints
  - `reports/` - Reporting endpoints
- **service/** - Business logic layer (mirrors controller structure)
- **repository/** & **repo/** - Spring Data JPA repositories
- **dao/** - Data access objects
- **dto/** - Data transfer objects for requests/responses
- **model/** - Domain models including Excel export models
- **config/** - Application configuration
- **utils/** - Utilities:
  - `advice/exception_handler/` - Global exception handling
  - `aop/logging_advice/` - AOP-based request/response logging
  - `http_request_interceptor/` - HTTP interceptor for auth token validation
  - `redis/` - Redis session utilities
  - `mapper/` - Object mapping utilities

## Architecture Notes

- Standard layered architecture: Controller -> Service -> Repository
- Auth tokens validated via HTTP interceptor calling Common-API
- AOP advice handles cross-cutting logging concerns
- Call allocation system manages distribution of outbound ECD calls to agents
- Quality audit module supports configurable sections, questions, grading, and sample selection
- Reports generated from dedicated report repositories
- Artifact ID: `ecd-api`, group: `com.iemr.ecd`, version: 3.6.0
