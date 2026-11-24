# event-driven-microservices-qa

QA learning project. The system under test (SUT) is [event-driven-microservices](https://github.com/ikraskovskyi/event-driven-microservices):

5 Spring Boot microservices communicating asynchronously over Kafka (choreography saga, transactional outbox, database per service).

So far the repo runs the SUT locally with Docker Compose, monitored with Prometheus and Grafana. Tests will be added step by step.

The SUT runs from prebuilt images (`ghcr.io/ikraskovskyi/event-driven-microservices/<service>`). No source code needed.

# How to run

Requires only Docker with Compose v2.

```bash
docker compose up -d --wait   # returns once all services are healthy
docker compose down           # stop and remove everything
```

| What | Where |
|---|---|
| Kafka UI: topics and messages | http://localhost:8080 (change with `KAFKA_UI_PORT=8090 docker compose up ...`) |
| Kafka, from the host (outside Docker) | `localhost:29092` |
| pgAdmin: browse all 4 databases, no login | http://localhost:5050 (change with `PGADMIN_PORT`) |
| Postgres, with any SQL client | `localhost:5432` order_db, `5433` payment_db, `5434` inventory_db, `5435` analytics_db (user/password `postgres`) |
| Order API (Swagger): create an order, then read its status | http://localhost:8081/swagger-ui.html |
| Analytics API (Swagger) | http://localhost:8085/swagger-ui.html |
| Health / Prometheus metrics | `http://localhost:808x/actuator/health`, `/actuator/prometheus` (ports 8081–8085) |
| Grafana: "SUT overview" dashboard, no login | http://localhost:3000 (change with `GRAFANA_PORT`) |
| Prometheus: scrapes `/actuator/prometheus` of all 5 services every 5 s | http://localhost:9090 (change with `PROMETHEUS_PORT`) |

Notes:
- **Data is ephemeral.** Every start gets fresh databases with stock reset to its initial values, and freshly created topics.
- **Restart the whole stack**, not single containers. A recreated Kafka container starts without topics.
- **For local use only.** pgAdmin and Grafana have no login and all passwords are hardcoded.
- **Don't run it together with the SUT's own stack** (same ports).
- **SUT version:** `latest` by default. Pin one with `SUT_TAG=sha-<short commit> docker compose up ...`.
- **Optionally override ports or `SUT_TAG`:** `cp .env.example .env` and edit it (Compose reads `.env` automatically).
