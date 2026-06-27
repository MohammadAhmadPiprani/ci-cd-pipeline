# GitOps-Driven CI/CD Pipeline Architecture

A secure, cloud-native Node.js microservice architecture automated via a complete **GitOps CI/CD pipeline**. This project combines Jenkins for Continuous Integration (automated containerization, optimization, and manifest shifting) with ArgoCD for declarative Continuous Delivery into a managed Kubernetes cluster.

## 🏗️ System & Pipeline Architecture

The workflow leverages the **separation of concerns** principle, dividing the infrastructure layer from the application lifecycle engine:

1. **Continuous Integration (Jenkins):** Triggered upon commits to the source code repo. It builds the runtime image using multi-stage security best practices, labels it, and pushes it to the registry. The pipeline concludes by systematically updating the image tag version in the declarative `deployment.yaml` manifest.
2. **Continuous Delivery (ArgoCD GitOps):** ArgoCD monitors the target branch within this repository. Upon detecting a difference between the state inside Git (`image: sample-app:<tag>`) and the live cluster execution state, it pulls down changes and initiates zero-downtime rolling updates.

---

## 📦 Component Breakdowns

### 1. Optimized Application Layer (`app.js`, `package.json`)
* **Express Microservice:** Features a core root API endpoint delivering structural JSON payloads (`message`, `version`, `timestamp`).
* **Resilient Probes:** Explicitly implements a `/health` endpoint to interface natively with the orchestrator's health system.

### 2. Hardened Container Manifest (`Dockerfile`)
* **Minimal Base Engine:** Utilizes `node:16-alpine` to maintain a tiny attack surface and light runtime image sizes.
* **Least-Privilege Security:** Shifts process ownership from root context down to an unprivileged worker (`USER node`). Files are strictly owned via `--chown=node:node` arguments to prevent directory privilege escalations.
* **Production Dependencies Only:** Installs explicitly using `--only=production` flags, bypassing bulky development tools.

### 3. Declarative Kubernetes Objects
* **Namespace Boundary (`Namespace`):** Sets up a distinct `sample-app` isolation barrier to protect adjacent resource stacks.
* **Self-Healing Deployment (`Deployment`):** Maintains 2 persistent replicas with embedded HTTP `livenessProbe` and `readinessProbe` metrics.
* **Service Router (`Service`):** Exposes the deployment internally across the target port matrix over a stable `ClusterIP`.

---

## 🚀 Deployment & Local Verification

### Prerequisites
* Kubernetes Cluster (Minikube / Kind)
* ArgoCD Controller installed in-cluster
* Jenkins Automation Engine with Docker execution privileges

### 1. Manual Local Cluster Provisioning
If you are staging or auditing this layout outside the automated pipeline engine, create the resources manually:

```bash
# Provision isolated administrative boundary
kubectl apply -f namespace.yaml

# Cascade app configs and operational policies
kubectl apply -f deployment.yaml -n sample-app
kubectl apply -f service.yaml -n sample-app
