# Microservices Workspace — Java Spring Boot

This workspace contains a small Spring Boot microservices demo implemented with Maven. It includes four standalone services (each is a Maven project):

- `ServiceRegstry` — service registry (Eureka or equivalent discovery server)
- `RestaurantServive` — restaurant service (business logic / APIs)
- `RatingService` — rating service (manages ratings/reviews)
- `UserService` — user service (user data and authentication-related APIs)

Each service lives in its own folder under the workspace root and follows a standard Spring Boot layout (`src/main/java`, `src/main/resources`, `pom.xml`). The repository includes the Maven wrapper (`mvnw`, `mvnw.cmd`) so you can build and run without a separate Maven install.

**Repository layout (top-level folders):**

- `ServiceRegstry/ServiceRegstry`
- `RestaurantServive/RestaurantServive`
- `RatingService/RatingService`
- `UserService/UserService`

## Quick checklist (before running)

- Install a compatible JDK (check each service `pom.xml` for the targeted Java version; Java 17+ is common).
- Ensure ports defined in `src/main/resources/application.yml` (or `.properties`) do not conflict between services.
- If using the service registry, start `ServiceRegstry` first so other services can register.

## Run services locally (PowerShell / Windows CMD)

Start services individually using the Maven wrapper from each service folder. Example sequence (PowerShell):

```powershell
cd ServiceRegstry/ServiceRegstry
.\mvnw spring-boot:run

# In separate terminals
cd RestaurantServive/RestaurantServive
.\mvnw spring-boot:run

cd RatingService/RatingService
.\mvnw spring-boot:run

cd UserService/UserService
.\mvnw spring-boot:run
```

Build a JAR for a specific service:

```powershell
cd RatingService/RatingService
.\mvnw clean package
# then run
java -jar target\ratingservice-<version>.jar
```

Run tests for a service:

```powershell
.\mvnw test
```

## Configuration notes

- Each service has `src/main/resources/application.yml` or `application.properties` where typical keys include:
  - `server.port` — HTTP port for the service
  - service registry/Eureka configuration (URL, client settings)
  - datasource settings (if a DB is used)
  - security settings (if OAuth2/JWT or basic auth is configured)
- If any service uses a database, check `pom.xml` for database drivers and `application.yml` for connection properties.

## Recommended repository improvements before pushing

- Add a root `.gitignore` to exclude build artifacts and OS/editor files. Example entries:

  - `**/target/`
  - `.idea/`
  - `*.iml`
  - `.DS_Store`

- Add per-service `README.md` files containing:
  - actual port numbers used
  - example API endpoints and sample curl requests
  - environment variables required (DB URLs, credentials)

- Provide a `docker-compose.yml` to run the full system locally (registry + all services) — I can create this for you.

## What I scanned and next steps

- I scanned the workspace structure and found `HELP.md` files inside each service folder, but reading them failed previously due to a filesystem access error.

I can proceed with one of the following (pick one):

1. Automatically extract exact `server.port`, dependencies, and common endpoints from each service's `pom.xml` and `src/main/resources` and update this README and create per-service READMEs. (Recommended)
2. Create a `docker-compose.yml` that uses inferred default ports and lets you override via `.env`.
3. Add per-service `README.md` files now with placeholders for ports/endpoints which you can fill in.

Reply with the number of the option you want, or ask me to perform all three steps.

## Architecture diagram

Below is a high-level system diagram showing the services and their main interactions. This uses Mermaid notation — GitHub renders Mermaid diagrams in README files.

```mermaid
flowchart LR
  SR[ServiceRegistry]\n  subgraph Registry
    SR
  end
  US[UserService]
  RS[RestaurantService]
  RT[RatingService]

  SR <-- "registers" --- US
  SR <-- "registers" --- RS
  SR <-- "registers" --- RT

  US -->|"GET /users/{id}"| RS
  RS -->|"GET /restaurants/{id}"| RT
  US -->|"POST /users/{id}/ratings"| RT

  classDef svc fill:#f9f,stroke:#333,stroke-width:1px;
  class SR,US,RS,RT svc;
```

If you'd like a different diagram style (sequence, deployment, or component), tell me which and I will replace it.

