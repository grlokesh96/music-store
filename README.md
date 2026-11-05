# Music Store

A demo microservices application that simulates an online musical instrument store. It showcases a polyglot architecture instrumented for observability (e.g., with the [Dash0](https://www.dash0.com/) Kubernetes operator).

## Architecture

| Service             | Language / Stack       | Responsibility                          | Port  |
|---------------------|------------------------|-----------------------------------------|-------|
| `frontend`          | Node.js                | Store website (UI)                      | 8080  |
| `api-gateway`       | Node.js                | Routes browser calls to backend services| 3000  |
| `catalog-service`   | Java (Spring Boot)     | Products & categories                   | 8081  |
| `inventory-service` | .NET                   | Stock levels & reservations             | 8082  |
| `cart-service`      | Node.js + Redis        | Shopping cart                           | 8083  |
| `order-service`     | Python (FastAPI)       | Checkout & orders                       | 8084  |
| `payment-service`   | Ruby (Sinatra)         | Mock payments                           | 8085  |
| `shipping-service`  | Python (FastAPI)       | Shipments & tracking                    | 8086  |
| `load-generator`    | Node.js                | Simulated shopper traffic               | —     |

Supporting infrastructure: **PostgreSQL** (catalog, orders) and **Redis** (cart).

### Request flow

```
                                              ┌─> catalog-service ────> PostgreSQL
                                              │
 User ──> frontend ──> api-gateway ──────────┼─> inventory-service
(browser)                                     │
                                              ├─> cart-service ───────> Redis
                                              │
                                              └─> order-service ──────> PostgreSQL
                                                     │
                                                     ├─> payment-service
                                                     ├─> inventory-service
                                                     └─> shipping-service
```

## Repository structure

```
.
├── services/                 # Application microservices
│   ├── api-gateway/
│   ├── cart-service/
│   ├── catalog-service/
│   ├── frontend/
│   ├── inventory-service/
│   ├── load-generator/
│   ├── order-service/
│   ├── payment-service/
│   └── shipping-service/
├── k8s/                      # Kubernetes manifests
├── scripts/                  # Helper scripts
│   └── build-images.sh
├── docs/                     # Additional documentation
│   └── dash0-setup.md
├── docker-compose.yml        # Local development stack
├── .gitignore
├── LICENSE
└── README.md
```

## Getting started

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and Docker Compose
- (Optional, for Kubernetes) `kubectl` and a cluster (kind, minikube, etc.)

### Run locally with Docker Compose

```bash
docker compose up --build
```

Open the store at [http://localhost:8080](http://localhost:8080).

### Run on Kubernetes

Build images and load them into your local cluster:

```bash
# kind
./scripts/build-images.sh --kind

# minikube
./scripts/build-images.sh --minikube
```

Deploy manifests:

```bash
kubectl apply -f k8s/
```

Access the frontend:

```bash
kubectl -n music-shop port-forward svc/frontend 8080:8080
```

Then visit [http://localhost:8080](http://localhost:8080).

## Observability

This project is designed to demonstrate automatic traces, logs, and metrics collection across services (e.g., via the Dash0 Kubernetes operator) with minimal code changes. See [docs/dash0-setup.md](docs/dash0-setup.md) for setup instructions.

## Contributing

Contributions are welcome. Please open an issue to discuss significant changes, or submit a pull request.

## License

This project is licensed under the [MIT License](LICENSE).
