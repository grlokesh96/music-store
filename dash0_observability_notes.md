# Dash0 Observability -- Simple & Clean Notes

These notes cover Dash0 observability setup, common challenges,
architecture, OpenTelemetry integration, cost optimization, and
Kubernetes monitoring.

## 1. What is Dash0?

[Dash0](https://www.dash0.com/) is an observability platform built
around OpenTelemetry (OTel).

It helps monitor applications using three main signals:

-   **Metrics:** CPU, memory, request rate, latency, and errors.
-   **Logs:** Application and system events.
-   **Traces:** End-to-end request flow across microservices.

The goal is to bring all three signals into one platform instead of
using separate tools.

## 2. Challenges with traditional observability

### Challenge 1: Complex setup

-   Every microservice may need instrumentation.
-   Developers must configure metrics, logs, and traces.
-   Collectors and exporters need configuration.
-   Maintaining multiple integrations takes time.

### Challenge 2: Difficult troubleshooting

Example: A user reports that the Catalog service is slow.

  Observability signal   What it shows
  ---------------------- ------------------------------
  Logs                   No obvious application error
  Metrics                CPU usage is very high
  Traces                 A database query is slow

**Problem:** Engineers switch between multiple tools to identify the
root cause.

### Challenge 3: High observability costs

-   Developers generate more and more telemetry.
-   Metrics, logs, and traces continuously increase.
-   Observability platforms may charge based on data volume.
-   Collecting unnecessary telemetry increases costs.

**Solution:** Collect only useful telemetry using filtering, sampling,
and retention policies.

## 3. Dash0 observability architecture

``` text
Kubernetes Cluster
       |
       v
Microservices
(Catalog, Cart, Payment)
       |
       v
OpenTelemetry Instrumentation
       |
       v
OpenTelemetry Collector
       |
       +----------+----------+
       |          |          |
     Metrics     Logs      Traces
       |          |          |
       +----------+----------+
                  |
                  v
             Dash0 Platform
                  |
                  v
       Dashboards and Alerts
```

### How it works

1.  Developers instrument applications using OpenTelemetry.
2.  The OpenTelemetry Collector receives telemetry.
3.  The collector processes and exports telemetry.
4.  Dash0 receives metrics, logs, and traces.
5.  Engineers use Dash0 dashboards to investigate application issues.

### Benefits

-   One observability platform.
-   Centralized telemetry collection.
-   Easier troubleshooting.
-   Better visibility across microservices.
-   Reduced tool switching.
-   Better control over telemetry costs.

## 4. Solution: Simplify observability setup

### A. Instrument once

-   Developers instrument applications using OpenTelemetry.
-   Avoid configuring separate monitoring integrations for every tool.
-   Reuse common instrumentation standards.

### B. Operate centrally

-   Deploy and manage OpenTelemetry Collectors centrally.
-   Use Kubernetes infrastructure monitoring.
-   Configure telemetry collection consistently.

### C. Control telemetry costs

-   Filter unnecessary logs.
-   Apply trace sampling.
-   Remove unwanted telemetry attributes.
-   Configure retention policies.
-   Export only useful telemetry.

## 5. Three layers of telemetry control

  -----------------------------------------------------------------------
  Layer                               Responsibility
  ----------------------------------- -----------------------------------
  In-cluster collection               Collect telemetry from applications
                                      and infrastructure

  Signal control                      Filter, sample, and process
                                      telemetry

  Central observability platform      Store, query, visualize, and alert
                                      on useful telemetry
  -----------------------------------------------------------------------

**Important:** Not every telemetry event needs to be exported. Process
and filter data before sending it to the observability backend where
appropriate.

## 6. Kubernetes monitoring with Dash0

  Category       What to monitor
  -------------- ------------------------------------------
  Cluster        Node health, CPU, and memory
  Pods           Restarts, resource usage, and status
  Deployments    Available and unavailable replicas
  Services       Request rate, errors, and latency
  Applications   Logs, traces, and exceptions
  Database       Query latency, connections, and failures

For a microservices application, distributed tracing is particularly
useful because it helps identify which service or database operation is
responsible for slow requests.

## 7. Practical Dash0 setup -- Kubernetes demo

The following is a sample setup workflow. Adapt namespaces, manifest
names, and service names to your actual project.

### Step 1: Create a Dash0 account

1.  Open the [Dash0 website](https://www.dash0.com/).
2.  Create an account.
3.  Create an organization.
4.  Navigate to organization settings.
5.  Configure the OTLP endpoint and authentication token as described in
    the current Dash0 documentation.

### Step 2: Create a Kubernetes cluster

Create a local Kubernetes cluster using Kind:

``` bash
kind create cluster --name demo-cluster
```

### Step 3: Install the Dash0 Operator

Install the Dash0 Kubernetes Operator using Helm, following the current
official installation instructions.

Verify the installation:

``` bash
kubectl get pods -n dash0-system
kubectl get all -n dash0-system
```

### Step 4: Deploy the sample application

Clone your application repository:

``` bash
git clone <repository-url>
cd <repository-name>
```

Deploy the Kubernetes manifests:

``` bash
kubectl apply -f k8s/
```

Check application pods:

``` bash
kubectl get pods -n multi-store
```

Access the frontend (assuming the service listens on port 8080):

``` bash
kubectl port-forward -n multi-store svc/frontend 8080:8080
```

Open `http://localhost:8080` in your browser.

### Step 5: Enable Dash0 monitoring

Apply your project's monitoring configuration:

``` bash
kubectl apply -f k8s/10-dash0-monitoring.yaml
```

Verify the application and monitoring components:

``` bash
kubectl get pods -n multi-store
kubectl get pods -n dash0-system
```

Then check Dash0 to verify that telemetry is arriving.

> **Note:** The namespace, manifest filename, service name, and ports
> above are illustrative and based on the demo notes. The actual
> Operator installation and monitoring configuration must match your
> cluster and current Dash0 documentation.

## 8. Important concepts to remember

  Concept               Simple explanation
  --------------------- ----------------------------------------------------
  OpenTelemetry         Standard for collecting telemetry
  OTLP                  Protocol used to send telemetry
  Collector             Receives, processes, and exports telemetry
  Instrumentation       Adding telemetry generation to applications
  Sampling              Collecting a selected portion of traces
  Filtering             Removing unnecessary telemetry
  Distributed tracing   Following requests across services
  Cardinality           Number of unique combinations of metric attributes

## 9. Real-world example: Trading application

Suppose you have 20+ microservices:

-   User Service
-   Authentication Service
-   Order Service
-   Trading Service
-   Payment Service
-   Portfolio Service
-   Market Data Service

A user places a trade, but the request takes five seconds.

With Dash0:

1.  **Metrics:** Identify increased request latency.
2.  **Logs:** Find application errors.
3.  **Traces:** Identify which microservice or database query is slow.
4.  **Dashboard:** View the complete request flow.
5.  **Alerting:** Notify the engineering team when thresholds are
    exceeded.

## 10. Final revision

Remember these five points:

1.  **OpenTelemetry** -- Instrumentation and telemetry collection
    standard.
2.  **Dash0** -- Centralized observability platform.
3.  **Three pillars** -- Metrics, Logs, and Traces.
4.  **Kubernetes Operator** -- Helps manage Kubernetes observability
    components.
5.  **Cost optimization** -- Filtering, sampling, and retention.

### Recommended implementation approach

For a 20+ microservices trading application:

1.  Use OpenTelemetry as the standard instrumentation layer.
2.  Use Dash0 as the centralized observability platform.
3.  Start with a few critical services.
4.  Validate metrics, logs, and traces.
5.  Configure useful dashboards and alerts.
6.  Review telemetry volume, sampling, and retention.
7.  Expand monitoring across the remaining services.

**Key takeaway:** Instrument consistently, collect centrally,
troubleshoot using correlated telemetry, and control costs by exporting
only useful data.
