# Gateway Service PFE

This repository contains the API Gateway for the final-year project microservices architecture. It acts as the single entry point for the frontend, centralizes request routing, applies CORS policies, and forwards requests to the correct backend service using Spring Cloud Gateway.

The gateway is designed to work alongside the other project services and is registered with Eureka for service discovery.

## Project purpose

The gateway service provides:

- a unified API entry point for the frontend
- request routing to multiple backend microservices
- service discovery via Eureka
- CORS management for browser requests
- WebSocket forwarding for notifications
- centralized response-header deduplication

## Architecture

```text
Frontend (Vue/React app on localhost:5173)
                 |
                 v
       Gateway Service (localhost:9999)
                 |
                 +--> Identity Service (/api/v1/auth/**)
                 +--> Provider Content Service (/api/v1/service/**, /api/v1/expertise/**)
                 +--> Booking & Review Service (/api/v1/reservations/**, /api/v1/reviews/**)
                 +--> Notifications Service (/api/v1/notifications/**, /ws-notifications/**)
                 |
                 v
             Eureka Server (service discovery)
```

## Technologies used

- Java 21
- Spring Boot 3.4.3
- Spring Cloud Gateway
- Spring Cloud Netflix Eureka Client
- Spring Boot Actuator
- Spring WebFlux / Project Reactor
- Maven Wrapper

## Key files

- `pom.xml` — project dependencies and build configuration
- `src/main/resources/application.yml` — gateway rules, routes, and filters
- `src/main/java/com/dalal/gatewayservicepfe/GatewayServicePfeApplication.java` — application bootstrap
- `src/main/java/com/dalal/gatewayservicepfe/config/CorsConfig.java` — CORS configuration

## Gateway routes

The service forwards requests based on URL patterns:

| Route | Path pattern | Target service |
| --- | --- | --- |
| `identity-service-route` | `/api/v1/auth/**` | `IDENTITY-SERVICE-PFE` |
| `provider-content-service-route` | `/api/v1/service/**`, `/api/v1/expertise/**` | `PROVIDER-CONTENT-SERVICE-PFE` |
| `booking-and-review-service-route` | `/api/v1/reservations/**`, `/api/v1/reviews/**` | `BOOKING-AND-REVIEW-SERVICE-PFE` |
| `notifications-service-route` | `/api/v1/notifications/**` | `NOTIFICATIONS-SERVICE-PFE` |
| `notification-websocket-route` | `/ws-notifications/**` | `NOTIFICATIONS-SERVICE-PFE` via WebSocket |

## Default filters

A default filter is configured to prevent duplicate CORS headers:

```yaml
- DedupeResponseHeader=Access-Control-Allow-Origin Access-Control-Allow-Credentials
```

This helps avoid browser issues when both the gateway and downstream service send the same CORS response headers.

## CORS configuration

The gateway allows requests from:

```text
http://localhost:5173
```

Allowed methods include:

- GET
- POST
- PUT
- DELETE
- OPTIONS
- PATCH

The `Authorization` header is exposed and credentials are enabled.

## Requirements

Before running this service, make sure the following are available:

- JDK 21
- Maven Wrapper (included in the repo)
- Eureka Server running
- The downstream microservices are up and registered with the names used in the routes

## Run locally

From the project root:

```bash
./mvnw spring-boot:run
```

On Windows:

```bat
mvnw.cmd spring-boot:run
```

## Build

```bash
./mvnw clean package
```

## Run tests

```bash
./mvnw test
```

## Application ports

- Gateway port: `9999`
- Frontend origin allowed by CORS: `http://localhost:5173`

## Notes

This project is part of a microservices-based final-year student project. The gateway is the central integration layer that allows multiple independent services to be exposed behind one API endpoint.

This repository currently contains the gateway service only; it assumes the discovery server and other backend services are running in the same system architecture.

## License

This project does not currently declare a custom license in the repository.
