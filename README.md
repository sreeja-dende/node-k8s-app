# Node.js CI/CD → Docker → Kubernetes 🚀

A DevOps pipeline project. A small Express app is unit-tested, packaged as a Docker image, pushed to Docker Hub and deployed to a Kubernetes cluster (Minikube). The same flow is implemented both as a **Jenkins** pipeline and as **GitLab CI**.

![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white)
![Jest](https://img.shields.io/badge/Jest-C21325?style=flat-square&logo=jest&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=flat-square&logo=jenkins&logoColor=white)
![GitLab CI](https://img.shields.io/badge/GitLab_CI-FC6D26?style=flat-square&logo=gitlab&logoColor=white)

## Pipeline

```
GitHub ──► Install deps ──► Build Docker image ──► Push to Docker Hub ──► kubectl apply ──► Service on NodePort 32000
```

| Stage | Jenkins (`Jenkinsfile`) | GitLab CI (`.gitlab-ci.yml`) |
|---|---|---|
| Install | `npm install` | `node:18` job, caches `node_modules` |
| Build | `docker build`, tagged with the build number | Docker-in-Docker build |
| Push | `docker push` to Docker Hub | Logs in with CI variables, then pushes |
| Deploy | Starts Minikube if needed, then applies the `k8s/` manifests | Same, on a self-hosted shell runner with Minikube |

## Kubernetes

- **`k8s/deployment.yaml`:** 2 replicas of the app container (port 8080), `imagePullPolicy: Always`
- **`k8s/service.yaml`:** a `NodePort` service exposing the app on port **32000**

## Run it yourself

```bash
# Tests
npm install
npm test

# Docker
docker build -t my-k8s-app .
docker run -p 8080:8080 my-k8s-app      # http://localhost:8080

# Kubernetes (Minikube)
minikube start
kubectl apply -f k8s/deployment.yaml -f k8s/service.yaml
minikube service my-k8s-app-service
```

## What I learned

- Writing declarative Jenkins pipelines and the equivalent GitLab CI stages
- Building and tagging Docker images per build, and pushing them with credentials kept in CI variables
- Deploying with Kubernetes Deployments and Services, and scaling with replicas
