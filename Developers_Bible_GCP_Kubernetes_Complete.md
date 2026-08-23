# The Developer's Bible: GCP + Kubernetes + OpenShift
## The Only Guide You Need — Completely Free, Developer-First, Zero External Dependencies
### For Java Backend Developers (3-4 YOE) | 12-Week Mastery Path

---

# TABLE OF CONTENTS

1. [Philosophy: Why This Guide Exists](#philosophy)
2. [Pre-Flight: Zero-Cost Setup](#preflight)
3. [Part A: Docker — The Foundation (Week 1)](#docker)
4. [Part B: Kubernetes Core — The Mental Model (Weeks 2-3)](#k8s-core)
5. [Part C: Kubernetes Networking — How Traffic Flows (Week 4)](#k8s-networking)
6. [Part D: Configuration & Storage (Week 5)](#k8s-config)
7. [Part E: Production Patterns — Probes, Resources, HPA (Week 6)](#k8s-production)
8. [Part F: Helm — Package Management (Week 7)](#helm)
9. [Part G: Security — RBAC, NetworkPolicies, SCCs (Week 8)](#security)
10. [Part H: Observability — Logs, Metrics, Debugging (Week 9)](#observability)
11. [Part I: GCP Integration — GKE, Workload Identity, Cloud SQL (Week 10)](#gcp-integration)
12. [Part J: CI/CD — GitHub Actions + ArgoCD (Week 11)](#cicd)
13. [Part K: Capstone Project + Certification Prep (Week 12)](#capstone)
14. [Appendix A: Complete YAML Reference Library](#yaml-ref)
15. [Appendix B: Every `kubectl`/`oc`/`gcloud` Command You Need](#cli-ref)
16. [Appendix C: Troubleshooting Playbook](#troubleshooting)
17. [Appendix D: Free Resource Index](#free-resources)

---

# 1. PHILOSOPHY: WHY THIS GUIDE EXISTS <a name="philosophy"></a>

## The Problem with Existing Resources

- **Official docs** are reference manuals, not learning paths.
- **YouTube tutorials** skip the "why" and jump to copy-paste.
- **Courses** cost money and often teach outdated versions.
- **Blog posts** cover one topic each — you need 50 tabs open.

## This Guide Solves That

| Feature | What You Get |
|--------|-------------|
| **Self-contained** | Every concept explained from first principles. No "go read this other article." |
| **Developer-first** | Written for Java/Spring Boot developers, not infrastructure engineers. |
| **Completely free** | Every tool, platform, and resource is free. |
| **Hands-on daily** | Every section ends with commands to run. |
| **OCP-aware** | Covers OpenShift-specific resources (Route, BuildConfig, ImageStream). |
| **GCP-integrated** | Learn K8s on real GKE using free credits strategically. |

## The 12-Week Promise

By the end of this guide, you will:
- [ ] Build optimized Docker images for Spring Boot
- [ ] Write any Kubernetes YAML from scratch or from memory
- [ ] Deploy production-grade apps on GKE
- [ ] Package microservices with Helm
- [ ] Debug any pod crash, network issue, or config problem
- [ ] Connect GKE to Cloud SQL and Pub/Sub securely
- [ ] Set up CI/CD pipelines that deploy on every git push
- [ ] Pass mock exams for Google Cloud Associate Cloud Engineer

---

# 2. PRE-FLIGHT: ZERO-COST SETUP <a name="preflight"></a>

## 2.1 What You Need

| Tool | Purpose | Cost | Install Command |
|------|---------|------|-----------------|
| **Docker Desktop** | Build and run containers locally | Free (personal) | [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop) |
| **Minikube** | Local Kubernetes cluster | Free | `brew install minikube` |
| **kubectl** | Kubernetes CLI | Free | `brew install kubectl` |
| **oc** | OpenShift CLI | Free | [mirror.openshift.com](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/stable/) |
| **Helm** | Kubernetes package manager | Free | `brew install helm` |
| **gcloud** | Google Cloud CLI | Free | `brew install --cask google-cloud-sdk` |
| **GitHub account** | Code repository + CI/CD | Free | [github.com](https://github.com) |
| **GCP Free Trial** | $300 credit for 90 days | Free | [cloud.google.com/free](https://cloud.google.com/free) |

## 2.2 GCP Free Trial Setup (Critical)

```bash
# Step 1: Sign up at cloud.google.com/free
# Step 2: Create a billing alert IMMEDIATELY
gcloud billing budgets create   --billing-account=YOUR_BILLING_ACCOUNT   --display-name="My Learning Budget"   --budget-amount=50USD   --threshold-rule=percent=50   --threshold-rule=percent=80   --threshold-rule=percent=100

# Step 3: Verify installation
gcloud version          # Should show version
kubectl version --client # Should show version
helm version            # Should show version
minikube version        # Should show version
```

## 2.3 Your First Kubernetes Cluster (Free, Local)

```bash
# Start Minikube (uses Docker driver, completely free)
minikube start --driver=docker --memory=4096 --cpus=2

# Verify
kubectl get nodes
# NAME       STATUS   ROLES           AGE   VERSION
# minikube   Ready    control-plane   10s   v1.28.3

# Enable useful addons
minikube addons enable ingress
minikube addons enable metrics-server
minikube addons enable dashboard

# Open dashboard
minikube dashboard
```

## 2.4 Your First GKE Cluster (Free, Cloud — Delete After Use)

```bash
# Login
gcloud auth login

# Set project
gcloud config set project YOUR_PROJECT_ID

# Create a small cluster (DELETE AFTER PRACTICE)
gcloud container clusters create practice   --zone=us-central1-a   --num-nodes=1   --machine-type=e2-medium   --disk-size=20GB

# Get credentials
kubectl get nodes

# DELETE WHEN DONE (set a phone alarm!)
gcloud container clusters delete practice --zone=us-central1-a --quiet
```

**Cost of a 1-node e2-medium cluster: ~$0.08/hour. A 2-hour practice session costs $0.16.**

---

# 3. PART A: DOCKER — THE FOUNDATION (WEEK 1) <a name="docker"></a>

## 3.1 Why Docker Matters

Kubernetes runs containers. If you don't understand Docker, you don't understand what Kubernetes is actually doing.

## 3.2 The Dockerfile for Spring Boot (Production-Grade)

```dockerfile
# ═══════════════════════════════════════════════════
# STAGE 1: Build
# ═══════════════════════════════════════════════════
FROM maven:3.9-eclipse-temurin-17-alpine AS builder

WORKDIR /build

# Layer caching: copy pom first, download deps, THEN copy source
COPY pom.xml .
COPY mvnw .
COPY .mvn .mvn
RUN ./mvnw dependency:go-offline -B

# Now copy source (this layer only rebuilds when source changes)
COPY src ./src
RUN ./mvnw clean package -DskipTests -B

# ═══════════════════════════════════════════════════
# STAGE 2: Runtime
# ═══════════════════════════════════════════════════
FROM gcr.io/distroless/java17-debian12:nonroot

WORKDIR /app

# Copy only the JAR from builder
COPY --from=builder /build/target/*.jar app.jar

# Non-root user (security)
USER nonroot:nonroot

# JVM tuned for containers
# MaxRAMPercentage: use X% of container memory limit (not host!)
ENTRYPOINT ["java",     "-XX:+UseContainerSupport",     "-XX:MaxRAMPercentage=75.0",     "-XX:InitialRAMPercentage=50.0",     "-Djava.security.egd=file:/dev/./urandom",     "-jar",     "app.jar"]
```

## 3.3 Docker Commands You Must Know

```bash
# Build
docker build -t my-app:1.0 .

# Run
docker run -d -p 8080:8080 --name my-app my-app:1.0

# Logs
docker logs -f my-app

# Shell (only if image has shell; distroless does not)
docker exec -it my-app /bin/sh

# For debugging distroless, use :debug tag temporarily
# FROM gcr.io/distroless/java17-debian12:debug

# List
docker ps
docker images

# Stop and remove
docker stop my-app && docker rm my-app

# Inspect
docker inspect my-app

# Resource usage
docker stats my-app
```

## 3.4 Docker Concepts for Kubernetes

| Concept | What It Is | K8s Equivalent |
|---------|-----------|----------------|
| Image | Blueprint for container | The `image` field in pod spec |
| Container | Running instance of image | What runs inside a Pod |
| Volume | Shared storage | Pod volumes |
| Network | Container networking | Pod network namespace |
| Port | Exposed port | containerPort |
| ENV | Environment variables | env / envFrom |

## 3.5 Week 1 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | Dockerfile anatomy | Write a Dockerfile for your Spring Boot app |
| 2 | Multi-stage builds | Compare image sizes: basic vs multi-stage |
| 3 | Layer caching | Change one source file, measure rebuild time |
| 4 | Distroless images | Build with distroless, try to `docker exec` (fail), switch to debug tag |
| 5 | JVM container tuning | Test `-XX:MaxRAMPercentage` vs fixed `-Xmx` |
| 6 | Docker Compose local | Run app + PostgreSQL locally with `docker-compose.yml` |
| 7 | Review | Write a cheat sheet: "Dockerfile best practices for Java" |

---

# 4. PART B: KUBERNETES CORE — THE MENTAL MODEL (WEEKS 2-3) <a name="k8s-core"></a>

## 4.1 The Kubernetes Architecture (For Developers)

```
┌─────────────────────────────────────────────────────────────┐
│                    KUBERNETES CLUSTER                        │
│                                                              │
│  ┌───────────────────────────────────────────────────────┐  │
│  │              CONTROL PLANE (Managed)                 │  │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌──────────┐ │  │
│  │  │ API     │ │ etcd    │ │Scheduler│ │Controller│ │  │
│  │  │ Server  │ │ (store) │ │         │ │ Manager  │ │  │
│  │  └─────────┘ └─────────┘ └─────────┘ └──────────┘ │  │
│  └───────────────────────────────────────────────────────┘  │
│                          │                                   │
│  ┌───────────────────────┼───────────────────────────────┐ │
│  │  WORKER NODE 1        │       WORKER NODE 2           │ │
│  │  ┌─────────────────┐  │  ┌─────────────────┐         │ │
│  │  │ kubelet         │  │  │ kubelet         │         │ │
│  │  │ kube-proxy      │  │  │ kube-proxy      │         │ │
│  │  │ container runtime│  │  │ container runtime│        │ │
│  │  └─────────────────┘  │  └─────────────────┘         │ │
│  │         │              │         │                  │ │
│  │  ┌──────┴──────┐      │  ┌──────┴──────┐          │ │
│  │  │   Pod A     │      │  │   Pod C     │          │ │
│  │  │ ┌─────────┐ │      │  │ ┌─────────┐ │          │ │
│  │  │ │Container│ │      │  │ │Container│ │          │ │
│  │  │ └─────────┘ │      │  │ └─────────┘ │          │ │
│  │  └─────────────┘      │  └─────────────┘          │ │
│  │  ┌─────────────┐      │                           │ │
│  │  │   Pod B     │      │                           │ │
│  │  └─────────────┘      │                           │ │
│  └────────────────────────┴───────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

**As a developer, you only interact with the API Server.** Everything else is managed.

## 4.2 The Pod — The Smallest Unit

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    version: v1.0
spec:
  containers:
  - name: my-app
    image: my-app:1.0
    ports:
    - containerPort: 8080
    env:
    - name: SPRING_PROFILES_ACTIVE
      value: "production"
    resources:
      requests:
        memory: "256Mi"
        cpu: "100m"
      limits:
        memory: "512Mi"
        cpu: "500m"
```

**Key insight:** You almost NEVER create Pods directly. You create Deployments, which create Pods.

## 4.3 The Deployment — "Keep My App Running"

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-java-app
  labels:
    app: my-java-app
spec:
  replicas: 3                    # Always keep 3 copies
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1               # Create 1 extra during update
      maxUnavailable: 0          # Never go below 3
  selector:
    matchLabels:
      app: my-java-app
  template:
    metadata:
      labels:
        app: my-java-app
    spec:
      containers:
      - name: app
        image: my-java-app:1.0
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
```

**What happens when you apply this:**
1. Deployment creates a ReplicaSet
2. ReplicaSet creates 3 Pods
3. If a Pod dies, ReplicaSet creates a new one
4. If you update the image, Deployment performs a rolling update

## 4.4 Hands-On: Your First Real Deployment

```bash
# Create a deployment
kubectl create deployment nginx --image=nginx --replicas=3

# See what was created
kubectl get all

# Scale it
kubectl scale deployment nginx --replicas=5

# Update image
kubectl set image deployment/nginx nginx=nginx:1.25

# Watch rollout
kubectl rollout status deployment/nginx

# See history
kubectl rollout history deployment/nginx

# Rollback
kubectl rollout undo deployment/nginx

# Delete
kubectl delete deployment nginx
```

## 4.5 The ReplicaSet (Understand But Don't Touch)

```bash
# See ReplicaSets (created by Deployment)
kubectl get replicasets

# You never edit ReplicaSets directly.
# You edit the Deployment, and it updates the ReplicaSet.
```

## 4.6 DaemonSet — "One Per Node"

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
      - name: node-exporter
        image: prom/node-exporter:latest
        ports:
        - containerPort: 9100
```

**Use cases:** Log collectors, monitoring agents, network proxies.

## 4.7 Job & CronJob — "Run And Exit"

```yaml
# Job: Run once, ensure completion
apiVersion: batch/v1
kind: Job
metadata:
  name: db-migration
spec:
  template:
    spec:
      containers:
      - name: flyway
        image: flyway/flyway:latest
        command: ["flyway", "migrate"]
      restartPolicy: Never
  backoffLimit: 3
```

```yaml
# CronJob: Run on schedule
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nightly-cleanup
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cleanup
            image: my-app:latest
            command: ["java", "-jar", "cleanup.jar"]
          restartPolicy: OnFailure
```

## 4.8 Week 2-3 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | Pods | Create, describe, delete pods. Understand lifecycle. |
| 2 | Deployments | Create, scale, update, rollback. Watch ReplicaSets. |
| 3 | Rolling updates | Update nginx image, watch pods cycle, rollback. |
| 4 | DaemonSets | Deploy node-exporter, verify on every node. |
| 5 | Jobs & CronJobs | Run a one-time job, create a CronJob. |
| 6 | Labels & selectors | Filter pods by label. Understand matchLabels. |
| 7 | Review | Cheat sheet: "When to use Deployment vs DaemonSet vs Job" |
| 8 | Namespaces | Create dev/staging/prod namespaces. Deploy to each. |
| 9 | Resource quotas | Limit CPU/memory per namespace. |
| 10 | Limit ranges | Set default resource requests. |
| 11 | Pod disruption budgets | Ensure minimum availability. |
| 12 | Review | Draw architecture: Namespace → Deployment → ReplicaSet → Pod |

---

# 5. PART C: KUBERNETES NETWORKING — HOW TRAFFIC FLOWS (WEEK 4) <a name="k8s-networking"></a>

## 5.1 The Service — Stable IP for Ephemeral Pods

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-java-app
spec:
  selector:
    app: my-java-app
  ports:
  - port: 80
    targetPort: 8080
  type: ClusterIP
```

**The magic:**
```
Other pods call: http://my-java-app:80
                    │
                    ▼
            Service (10.96.123.45)  ← Stable IP!
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
    Pod:8080    Pod:8080    Pod:8080
```

## 5.2 Service Types

| Type | Behavior | Use Case |
|------|----------|----------|
| ClusterIP | Internal IP only | Microservice-to-microservice |
| NodePort | Exposes on node IP:port | Dev testing |
| LoadBalancer | Cloud provider LB | Production external |
| ExternalName | DNS alias to external | Calling external APIs |

## 5.3 Ingress — "The Smart Router"

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: api.mycompany.com
    http:
      paths:
      - path: /orders
        pathType: Prefix
        backend:
          service:
            name: order-service
            port:
              number: 80
      - path: /users
        pathType: Prefix
        backend:
          service:
            name: user-service
            port:
              number: 80
```

**In OpenShift, this is a Route:**
```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: my-app-route
spec:
  host: my-app.company.com
  to:
    kind: Service
    name: my-java-app
  tls:
    termination: edge
```

## 5.4 DNS Resolution

```bash
# From inside any pod:
nslookup my-java-app                    # Same namespace
nslookup my-java-app.my-namespace       # Different namespace
nslookup my-java-app.my-namespace.svc.cluster.local  # Full name
```

## 5.5 NetworkPolicy — Firewall for Pods

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-policy
spec:
  podSelector:
    matchLabels:
      app: api-service
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

## 5.6 Week 4 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | ClusterIP | Deploy 2 pods, create Service, curl from one to another. |
| 2 | NodePort | Expose via NodePort, access from host. |
| 3 | LoadBalancer | (On GKE) Create LB service, get external IP. |
| 4 | Ingress | Install NGINX ingress, route /api to different services. |
| 5 | DNS | Test DNS resolution from inside pods. |
| 6 | NetworkPolicy | Install Calico, create default-deny + whitelist. |
| 7 | Review | Draw: Internet → Ingress → Service → Pod flow. |

---

# 6. PART D: CONFIGURATION & STORAGE (WEEK 5) <a name="k8s-config"></a>

## 6.1 ConfigMap — 3 Ways to Use

**Way 1: Environment Variables (all keys)**
```yaml
spec:
  containers:
  - name: app
    envFrom:
    - configMapRef:
        name: app-config
```

**Way 2: Environment Variable (single key)**
```yaml
spec:
  containers:
  - name: app
    env:
    - name: LOG_LEVEL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: log.level
```

**Way 3: Volume Mount (files)**
```yaml
spec:
  containers:
  - name: app
    volumeMounts:
    - name: config-vol
      mountPath: /app/config
  volumes:
  - name: config-vol
    configMap:
      name: app-config
```

## 6.2 Secret — Same Patterns, Base64 Encoded

```bash
# Create without manual base64
kubectl create secret generic db-secret   --from-literal=username=admin   --from-literal=password=secret123
```

## 6.3 Downward API — Pod Metadata as Env Vars

```yaml
spec:
  containers:
  - name: app
    env:
    - name: POD_NAME
      valueFrom:
        fieldRef:
          fieldPath: metadata.name
    - name: POD_IP
      valueFrom:
        fieldRef:
          fieldPath: status.podIP
```

## 6.4 Volumes

| Volume | Use Case | Persists? |
|--------|----------|-----------|
| emptyDir | Share files between containers | No |
| hostPath | Node-local storage | Yes (node-local) |
| configMap | Config files | Yes (in CM) |
| secret | Sensitive files | Yes (in Secret) |
| persistentVolumeClaim | Database data | Yes |

## 6.5 PVC — Persistent Storage

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard
```

## 6.6 Week 5 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | ConfigMap env | Create CM, mount all keys as env vars. |
| 2 | ConfigMap files | Mount CM as files in pod. Update CM, check if file updates. |
| 3 | Secrets | Create Secret, mount as env var and file. Decode it. |
| 4 | Downward API | Inject pod name, IP, namespace as env vars. |
| 5 | emptyDir | Share files between 2 containers in one pod. |
| 6 | PVC | Create PVC, mount to pod, write data, delete pod, recreate, verify data. |
| 7 | Review | Document: "ConfigMap vs Secret vs Downward API vs PVC" |

---

# 7. PART E: PRODUCTION PATTERNS (WEEK 6) <a name="k8s-production"></a>

## 7.1 Probes — Health Checks

```yaml
spec:
  containers:
  - name: app
    # Startup: "Wait for app to finish starting"
    startupProbe:
      httpGet:
        path: /actuator/health
        port: 8080
      failureThreshold: 30
      periodSeconds: 10

    # Liveness: "Is app alive? If not, restart it."
    livenessProbe:
      httpGet:
        path: /actuator/health/liveness
        port: 8080
      initialDelaySeconds: 60
      periodSeconds: 10
      failureThreshold: 3

    # Readiness: "Is app ready for traffic? If not, remove from Service."
    readinessProbe:
      httpGet:
        path: /actuator/health/readiness
        port: 8080
      initialDelaySeconds: 30
      periodSeconds: 5
      failureThreshold: 3
```

## 7.2 Spring Boot Actuator Configuration

```properties
# application.properties
management.endpoints.web.exposure.include=health,info,prometheus
management.endpoint.health.probes.enabled=true
management.health.livenessstate.enabled=true
management.health.readinessstate.enabled=true
```

## 7.3 Resource Requests & Limits

```yaml
resources:
  requests:
    memory: "512Mi"    # Scheduling: node must have this available
    cpu: "250m"        # 250 millicores = 0.25 CPU
  limits:
    memory: "1Gi"      # Enforcement: OOMKill if exceeded
    cpu: "1000m"       # Throttle if exceeded
```

## 7.4 Horizontal Pod Autoscaler (HPA)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-java-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 60
```

## 7.5 Pod Disruption Budget (PDB)

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-java-app
```

## 7.6 Week 6 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | Startup probe | Add to app, make it fail, watch behavior. |
| 2 | Liveness probe | Simulate failure, watch pod restart. |
| 3 | Readiness probe | Make it fail, watch traffic stop. |
| 4 | Resource limits | Set limits, cause OOMKill intentionally. |
| 5 | HPA | Install metrics-server, configure HPA, generate load. |
| 6 | PDB | Create PDB, try to drain node, watch it block. |
| 7 | Review | Document: "Production Checklist for Java Apps on K8s" |

---

# 8. PART F: HELM — PACKAGE MANAGEMENT (WEEK 7) <a name="helm"></a>

## 8.1 Why Helm?

Without Helm: 500 YAML files for 50 microservices.
With Helm: 1 chart template + 50 values files.

## 8.2 Chart Structure

```
my-chart/
├── Chart.yaml
├── values.yaml
├── values-prod.yaml
└── templates/
    ├── _helpers.tpl
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    └── configmap.yaml
```

## 8.3 Template Example

```yaml
# templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-chart.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
      - name: app
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        resources:
          {{- toYaml .Values.resources | nindent 10 }}
```

## 8.4 Values Files

```yaml
# values.yaml (dev)
replicaCount: 1
image:
  repository: my-app
  tag: latest
resources:
  requests:
    memory: 256Mi
    cpu: 100m
```

```yaml
# values-prod.yaml
replicaCount: 5
resources:
  requests:
    memory: 2Gi
    cpu: 1000m
```

## 8.5 Commands

```bash
helm create my-chart
helm template my-chart          # See generated YAML
helm install my-app ./my-chart -f values-prod.yaml
helm upgrade my-app ./my-chart -f values-prod.yaml
helm rollback my-app 1
helm history my-app
```

## 8.6 Week 7 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | Helm create | Create chart, explore structure. |
| 2 | Templates | Write deployment template with variables. |
| 3 | Values files | Create dev and prod values, install both. |
| 4 | Helpers | Write _helpers.tpl for reusable labels. |
| 5 | Conditionals | Add `if/else` for optional resources. |
| 6 | Hooks | Add pre-install Job for DB migration. |
| 7 | Review | Package a complete microservice with Helm. |

---

# 9. PART G: SECURITY (WEEK 8) <a name="security"></a>

## 9.1 RBAC

```yaml
# Role (namespace-scoped)
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

```yaml
# RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
subjects:
- kind: ServiceAccount
  name: my-sa
roleRef:
  kind: Role
  name: pod-reader
```

## 9.2 Security Context

```yaml
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
  containers:
  - name: app
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
        - ALL
```

## 9.3 OCP SecurityContextConstraints (SCC)

```bash
# Check which SCC your pod uses
oc get pod <pod> -o yaml | grep openshift.io/scc

# Assign SCC to ServiceAccount
oc adm policy add-scc-to-user nonroot -z my-sa
```

## 9.4 Week 8 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | ServiceAccounts | Create SA, run pod with it. |
| 2 | Roles | Create Role with limited permissions. |
| 3 | RoleBindings | Bind SA to Role, test access. |
| 4 | Security contexts | Run pod as non-root, read-only FS. |
| 5 | NetworkPolicies | Default-deny + whitelist. |
| 6 | OCP SCCs | Test different SCCs. |
| 7 | Review | Security checklist for production. |

---

# 10. PART H: OBSERVABILITY (WEEK 9) <a name="observability"></a>

## 10.1 Logging

```bash
# View logs
kubectl logs <pod>
kubectl logs <pod> -f
kubectl logs <pod> --previous
kubectl logs -l app=my-app
```

## 10.2 Structured Logging (Java)

```xml
<!-- logback-spring.xml -->
<appender name="JSON" class="ch.qos.logback.core.ConsoleAppender">
    <encoder class="net.logstash.logback.encoder.LogstashEncoder"/>
</appender>
```

## 10.3 Prometheus Metrics

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

```properties
management.endpoints.web.exposure.include=health,info,prometheus
```

## 10.4 Installing Prometheus + Grafana (Free)

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/kube-prometheus-stack
kubectl port-forward svc/prometheus-grafana 3000:80
# Login: admin / prom-operator
```

## 10.5 Week 9 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | kubectl logs | Practice all log commands. |
| 2 | JSON logging | Configure Spring Boot for structured logs. |
| 3 | Prometheus | Add Micrometer, expose /actuator/prometheus. |
| 4 | Install stack | Deploy Prometheus + Grafana on Minikube. |
| 5 | Dashboards | Create dashboard for JVM metrics. |
| 6 | Alerts | Configure alert for high CPU. |
| 7 | Review | Observability checklist. |

---

# 11. PART I: GCP INTEGRATION (WEEK 10) <a name="gcp-integration"></a>

## 11.1 GKE Cluster Types

| Type | You Manage | Cost | Use Case |
|------|-----------|------|----------|
| Standard | Nodes, scaling, upgrades | Lower | Need control |
| Autopilot | Just pods | Higher | Hands-off |

## 11.2 Creating a GKE Cluster (Use Free Credits)

```bash
gcloud container clusters create my-cluster   --zone=us-central1-a   --num-nodes=2   --machine-type=e2-medium   --enable-workload-identity
```

## 11.3 Workload Identity — The Most Important Feature

**Without Workload Identity (BAD):**
```yaml
# Mount JSON key as Secret
volumes:
- name: gcp-key
  secret:
    secretName: gcp-sa-key
```

**With Workload Identity (GOOD):**
```yaml
spec:
  serviceAccountName: my-ksa
  containers:
  - name: app
    image: my-app:1.0
    # No keys! App uses ADC automatically
```

**Setup:**
```bash
# 1. Create GCP SA
gcloud iam service-accounts create my-gcp-sa

# 2. Create K8s SA
kubectl create serviceaccount my-ksa

# 3. Bind them
gcloud iam service-accounts add-iam-policy-binding   my-gcp-sa@project.iam.gserviceaccount.com   --role=roles/iam.workloadIdentityUser   --member="serviceAccount:project.svc.id.goog[namespace/my-ksa]"

# 4. Annotate K8s SA
kubectl annotate serviceaccount my-ksa   iam.gke.io/gcp-service-account=my-gcp-sa@project.iam.gserviceaccount.com
```

## 11.4 Cloud SQL Proxy Sidecar

```yaml
spec:
  containers:
  - name: app
    image: my-app:1.0
    env:
    - name: DB_HOST
      value: localhost
  - name: cloud-sql-proxy
    image: gcr.io/cloud-sql-connectors/cloud-sql-proxy:2.0.0
    args:
    - "--structured-logs"
    - "--port=5432"
    - "project:region:instance"
    securityContext:
      runAsNonRoot: true
```

## 11.5 Pub/Sub from Spring Boot

```xml
<dependency>
    <groupId>com.google.cloud</groupId>
    <artifactId>spring-cloud-gcp-starter-pubsub</artifactId>
</dependency>
```

```java
@Service
public class OrderPublisher {
    @Autowired private PubSubTemplate pubSubTemplate;

    public void publishOrder(Order order) {
        pubSubTemplate.publish("order-topic", order);
    }
}
```

## 11.6 Week 10 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | GKE Standard | Create cluster, deploy app. |
| 2 | GKE Autopilot | Create Autopilot, compare. |
| 3 | Workload Identity | Configure WI, list GCS buckets from pod. |
| 4 | Cloud SQL Proxy | Deploy sidecar, connect to Cloud SQL. |
| 5 | Pub/Sub | Publish message from Spring Boot. |
| 6 | Secret Manager | Store secret, access from app. |
| 7 | Review | GCP + K8s integration diagram. |

---

# 12. PART J: CI/CD (WEEK 11) <a name="cicd"></a>

## 12.1 GitHub Actions (Free for Public Repos)

```yaml
name: CI/CD
on:
  push:
    branches: [main]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Build
        run: ./mvnw clean package -DskipTests

      - name: Docker Build
        run: docker build -t ghcr.io/${{ github.repository }}:${{ github.sha }} .

      - name: Docker Push
        run: |
          echo ${{ secrets.GITHUB_TOKEN }} | docker login ghcr.io -u ${{ github.actor }} --password-stdin
          docker push ghcr.io/${{ github.repository }}:${{ github.sha }}

      - name: Deploy to GKE
        run: |
          gcloud auth activate-service-account --key-file=${{ secrets.GCP_SA_KEY }}
          gcloud container clusters get-credentials my-cluster --zone=us-central1-a
          kubectl set image deployment/my-app app=ghcr.io/${{ github.repository }}:${{ github.sha }}
```

## 12.2 ArgoCD (GitOps)

```bash
# Install ArgoCD on cluster
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

## 12.3 Week 11 Daily Plan

| Day | Topic | Hands-On |
|-----|-------|----------|
| 1 | GitHub Actions | Create workflow for build + test. |
| 2 | Docker push | Push to GitHub Container Registry. |
| 3 | GKE deploy | Deploy to GKE from GitHub Actions. |
| 4 | ArgoCD install | Install ArgoCD on Minikube. |
| 5 | ArgoCD app | Connect Git repo, auto-sync. |
| 6 | Helm + CI/CD | Package with Helm, deploy via CI. |
| 7 | Review | Full pipeline: Git push → GKE deploy. |

---

# 13. PART K: CAPSTONE + CERTIFICATION (WEEK 12) <a name="capstone"></a>

## 13.1 Capstone Architecture

```
Internet
   │
   ▼
GCLB / Ingress
   │
   ├── Order API (3 replicas + HPA)
   │   └── Workload Identity
   │
   └── User API (3 replicas + HPA)
       └── Workload Identity
       │
       ▼
   GCP Pub/Sub (order-events topic)
       │
       ▼
   Fulfillment Service (K8s Deployment)
       └── Cloud SQL Proxy sidecar
       │
       ▼
   Cloud SQL PostgreSQL (private IP)
```

## 13.2 Certification Prep

| Day | Activity |
|-----|----------|
| 1 | Review ACE exam guide. Identify weak areas. |
| 2 | Practice 10 gcloud compute scenarios. |
| 3 | Practice 10 gcloud container + kubectl scenarios. |
| 4 | IAM deep dive: 20 least-privilege questions. |
| 5 | Networking: VPC, firewall, Cloud NAT troubleshooting. |
| 6 | Mock Exam #1 (50 questions). Review wrong answers. |
| 7 | Mock Exam #2 (50 questions). Target 40+/50. |

## 13.3 Free Mock Exam Resources

- [ExamTopics](https://www.examtopics.com/exams/google/associate-cloud-engineer/) — Free
- [Google Cloud Skills Boost](https://www.cloudskillsboost.google/) — Free tier
- [Killer.sh](https://killer.sh/) — Free tier for K8s

---

# APPENDIX A: COMPLETE YAML REFERENCE <a name="yaml-ref"></a>

## A.1 Deployment (Production-Ready)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-java-app
  labels:
    app: my-java-app
    version: v1.0.0
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  selector:
    matchLabels:
      app: my-java-app
  template:
    metadata:
      labels:
        app: my-java-app
        version: v1.0.0
    spec:
      serviceAccountName: my-java-app-sa
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 2000
      containers:
      - name: app
        image: my-java-app:1.0.0
        imagePullPolicy: Always
        ports:
        - name: http
          containerPort: 8080
          protocol: TCP
        - name: management
          containerPort: 8081
          protocol: TCP
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: "production"
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: my-java-app-config
              key: log.level
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-java-app-db-secret
              key: password
        startupProbe:
          httpGet:
            path: /actuator/health
            port: management
          failureThreshold: 30
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: management
          initialDelaySeconds: 60
          periodSeconds: 10
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: management
          initialDelaySeconds: 30
          periodSeconds: 5
          failureThreshold: 3
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
        volumeMounts:
        - name: config
          mountPath: /app/config
          readOnly: true
        - name: tmp
          mountPath: /tmp
      volumes:
      - name: config
        configMap:
          name: my-java-app-config
      - name: tmp
        emptyDir: {}
      terminationGracePeriodSeconds: 60
```

## A.2 Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-java-app
  labels:
    app: my-java-app
spec:
  type: ClusterIP
  selector:
    app: my-java-app
  ports:
  - name: http
    port: 80
    targetPort: 8080
    protocol: TCP
  - name: management
    port: 8081
    targetPort: 8081
    protocol: TCP
```

## A.3 Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-java-app
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - api.mycompany.com
    secretName: api-tls
  rules:
  - host: api.mycompany.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-java-app
            port:
              number: 80
```

## A.4 Route (OpenShift)

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: my-java-app
spec:
  host: api.mycompany.com
  to:
    kind: Service
    name: my-java-app
  port:
    targetPort: http
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Redirect
```

## A.5 ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-java-app-config
data:
  log.level: "INFO"
  server.port: "8080"
  application.properties: |
    server.port=8080
    management.server.port=8081
    logging.level.root=INFO
```

## A.6 Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-java-app-db-secret
type: Opaque
stringData:
  username: admin
  password: secret123
  url: jdbc:postgresql://db:5432/mydb
```

## A.7 PersistentVolumeClaim

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard
```

## A.8 HorizontalPodAutoscaler

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-java-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-java-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 60
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
```

## A.9 NetworkPolicy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-java-app-policy
spec:
  podSelector:
    matchLabels:
      app: my-java-app
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: postgres
    ports:
    - protocol: TCP
      port: 5432
```

## A.10 ServiceAccount + RBAC

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-java-app-sa
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: my-java-app-role
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-java-app-binding
subjects:
- kind: ServiceAccount
  name: my-java-app-sa
roleRef:
  kind: Role
  name: my-java-app-role
```

## A.11 Pod Disruption Budget

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-java-app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-java-app
```

---

# APPENDIX B: CLI REFERENCE <a name="cli-ref"></a>

## B.1 kubectl

```bash
# Cluster info
kubectl cluster-info
kubectl get nodes
kubectl top nodes

# Pods
kubectl get pods
kubectl get pods -o wide
kubectl get pods --show-labels
kubectl describe pod <name>
kubectl logs <name>
kubectl logs <name> -f
kubectl logs <name> --previous
kubectl exec -it <name> -- /bin/sh
kubectl port-forward pod/<name> 8080:8080
kubectl delete pod <name>

# Deployments
kubectl get deployments
kubectl describe deployment <name>
kubectl scale deployment <name> --replicas=5
kubectl set image deployment/<name> container=<new-image>
kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>
kubectl rollout undo deployment/<name>
kubectl rollout undo deployment/<name> --to-revision=2

# Services
kubectl get services
kubectl get endpoints
kubectl expose deployment <name> --type=ClusterIP --port=80

# Config & Secrets
kubectl get configmaps
kubectl get secrets
kubectl create configmap <name> --from-file=config.properties
kubectl create secret generic <name> --from-literal=password=secret

# Apply & Delete
kubectl apply -f manifest.yaml
kubectl delete -f manifest.yaml
kubectl apply -k kustomization/  # Kustomize

# Namespaces
kubectl get namespaces
kubectl create namespace <name>
kubectl config set-context --current --namespace=<name>

# Explain
kubectl explain deployment.spec
kubectl explain pod.spec.containers
kubectl explain service.spec
kubectl explain ingress.spec

# Generate YAML
kubectl create deployment test --image=nginx --dry-run=client -o yaml
kubectl expose deployment test --port=80 --dry-run=client -o yaml
kubectl create configmap test --from-literal=key=value --dry-run=client -o yaml
```

## B.2 oc (OpenShift)

```bash
# Projects
oc projects
oc project <name>
oc new-project <name>

# Status
oc status

# Build
oc start-build <name>
oc logs -f bc/<name>
oc get builds

# Routes
oc get routes
oc expose service <name> --hostname=api.example.com

# Debug
oc rsh <pod>
oc debug <pod>

# Policy
oc adm policy add-scc-to-user nonroot -z <sa>
oc adm policy add-role-to-user admin <user> -n <project>
```

## B.3 gcloud

```bash
# Auth
 gcloud auth login
 gcloud config set project <project-id>

# Compute
 gcloud compute instances list
 gcloud compute ssh <instance>

# GKE
 gcloud container clusters create <name> --zone=<zone>
 gcloud container clusters list
 gcloud container clusters get-credentials <name> --zone=<zone>
 gcloud container clusters delete <name> --zone=<zone>

# IAM
 gcloud iam service-accounts create <name>
 gcloud iam service-accounts list

# Storage
 gsutil ls
 gsutil cp file.txt gs://bucket/

# Build
 gcloud builds submit --tag gcr.io/project/image:tag
```

## B.4 Helm

```bash
helm create <name>
helm template <name> ./chart
helm install <release> ./chart -f values.yaml
helm upgrade <release> ./chart -f values.yaml
helm upgrade --install <release> ./chart -f values.yaml
helm rollback <release> <revision>
helm history <release>
helm uninstall <release>
helm list
helm lint ./chart
helm package ./chart
```

---

# APPENDIX C: TROUBLESHOOTING PLAYBOOK <a name="troubleshooting"></a>

## C.1 Pod Won't Start

```bash
# 1. Check status
kubectl get pod <name>

# 2. Read events (THE MOST IMPORTANT STEP)
kubectl describe pod <name>
# Look at the bottom: "Events" section

# 3. Common statuses:
# Pending → Insufficient resources, taints, PVC not bound
# ContainerCreating → Image pull issue, volume mount problem
# CrashLoopBackOff → App crashing, check logs
# ImagePullBackOff → Wrong image name, auth issue
# OOMKilled → Memory limit too low
```

## C.2 Common Errors & Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `ImagePullBackOff` | Wrong image name or tag | `kubectl describe pod` to see exact error |
| `CrashLoopBackOff` | App exits immediately | Check logs: `kubectl logs --previous` |
| `OOMKilled` | Memory limit exceeded | Increase memory limit, tune JVM `-XX:MaxRAMPercentage` |
| `CreateContainerConfigError` | Missing ConfigMap/Secret | Create the missing resource |
| `FailedScheduling` | No node has enough resources | Scale cluster or reduce requests |
| `InvalidImageName` | Special chars in tag | Use only lowercase, numbers, hyphens, dots |
| `RunContainerError` | Container runtime issue | Check `kubectl describe` for details |

## C.3 Network Issues

```bash
# Test from inside pod
kubectl exec -it <pod> -- /bin/sh
ping <service-name>
curl -v http://<service-name>:80/health
nslookup <service-name>

# Check service endpoints
kubectl get endpoints <service-name>
# If empty, selector doesn't match any pods

# Check DNS
kubectl run -it --rm debug --image=busybox:1.28 --restart=Never -- nslookup kubernetes.default
```

## C.4 Performance Issues

```bash
# Check resource usage
kubectl top pods
kubectl top nodes

# Check if HPA is firing
kubectl get hpa
kubectl describe hpa <name>

# Check for throttling
kubectl describe pod <name> | grep -i throttle
```

---

# APPENDIX D: FREE RESOURCE INDEX <a name="free-resources"></a>

## Documentation (Always Free)

| Resource | URL |
|----------|-----|
| Kubernetes Docs | https://kubernetes.io/docs/home/ |
| Kubernetes API Reference | https://kubernetes.io/docs/reference/kubernetes-api/ |
| OpenShift Docs | https://docs.openshift.com/ |
| GCP Docs | https://cloud.google.com/docs |
| Spring Cloud GCP | https://spring.io/projects/spring-cloud-gcp |
| Helm Docs | https://helm.sh/docs/ |
| Docker Docs | https://docs.docker.com/ |

## Interactive Learning (Free)

| Resource | URL |
|----------|-----|
| Kubernetes Basics (Official) | https://kubernetes.io/docs/tutorials/kubernetes-basics/ |
| Katacoda Kubernetes | https://www.katacoda.com/courses/kubernetes |
| KodeKloud Free Tier | https://kodekloud.com/ |
| Google Cloud Skills Boost | https://www.cloudskillsboost.google/ |

## YouTube Channels (Free)

| Channel | Content |
|---------|---------|
| TechWorld with Nana | Kubernetes explained visually |
| freeCodeCamp | Full courses |
| Kubernetes Official | Deep dives |
| That DevOps Guy | Practical tutorials |

## Practice Exams (Free)

| Resource | URL |
|----------|-----|
| ExamTopics ACE | https://www.examtopics.com/exams/google/associate-cloud-engineer/ |
| Killer.sh | https://killer.sh/ |

---

# FINAL CHECKLIST

By the end of this guide, you should be able to:

- [ ] Build optimized multi-stage Docker images for Spring Boot
- [ ] Write any Kubernetes YAML from scratch
- [ ] Deploy apps on Minikube and GKE
- [ ] Use ConfigMaps, Secrets, and volumes correctly
- [ ] Configure liveness, readiness, and startup probes
- [ ] Set resource requests/limits and HPA
- [ ] Package apps with Helm
- [ ] Implement RBAC and NetworkPolicies
- [ ] Set up Prometheus + Grafana monitoring
- [ ] Connect GKE to Cloud SQL and Pub/Sub
- [ ] Configure Workload Identity (no keys in pods)
- [ ] Build CI/CD pipelines with GitHub Actions
- [ ] Debug any pod crash or network issue
- [ ] Pass ACE mock exams at 80%+

---

*This is the only guide you need. Start at Week 1. Build every day. Document everything. By Week 12, you'll be the person others ask for help.*
