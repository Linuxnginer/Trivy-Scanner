# Trivy-Scanner
Docker Image CI – DevSecOps Pipeline
This repository contains a DevSecOps-focused Continuous Integration (CI) workflow that automatically builds, scans, and publishes Docker images whenever code is pushed or a pull request is opened against the main branch.
The pipeline is designed for on-premise environments using a self-hosted GitHub Actions runner and a local Docker registry.
Overview
This CI pipeline performs the following steps:
Checkout Source Code
Generate a Unique Image Tag
Build the Docker Image
Run Security Scanning (Trivy)
Push the Image to a Local Docker Registry
The workflow ensures every build is traceable, scanned for vulnerabilities, and stored in a private registry.
Architecture
Developer Push / PR
        │
        ▼
GitHub Actions Workflow
(Self-Hosted Runner)
        │
        ├── Build Docker Image
        │
        ├── Security Scan (Trivy)
        │
        └── Push Image
              │
              ▼
     Local Docker Registry
     localhost:5000
CI Workflow Triggers
The pipeline runs automatically on:
Push to main
Pull requests targeting main
on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]
Environment Variables
Variable	Description
IMAGE_NAME	Name of the Docker image
REGISTRY	Docker registry endpoint
Example:
env:
  IMAGE_NAME: tickrtxt-app
  REGISTRY: localhost:5000
Image Tagging Strategy
Each build generates a unique timestamp-based tag:
TAG=$(date +%s)
Example image tag:
tickrtxt-app:1719859382
After tagging for the registry:
localhost:5000/tickrtxt-app:1719859382
This ensures:
Unique build artifacts
Easy rollback
Traceable deployments
Security Scanning (DevSecOps)
The pipeline integrates container vulnerability scanning using Trivy.
Scan Scope
The scan checks for:
HIGH vulnerabilities
CRITICAL vulnerabilities
Example command used:
trivy image --severity HIGH,CRITICAL $IMAGE_NAME:$TAG
Non-Blocking Mode
Currently the scan does not fail the pipeline:
|| true
This approach allows:
Visibility into vulnerabilities
CI pipeline continuity
Gradual security enforcement
In production environments, you may want to enforce blocking on critical vulnerabilities.
Docker Image Build
The Docker image is built directly from the repository Dockerfile.
docker build . --file Dockerfile --tag $IMAGE_NAME:$TAG
Docker Registry Push
After building and scanning, the image is pushed to the local Docker registry.
docker tag $IMAGE_NAME:$TAG $REGISTRY/$IMAGE_NAME:$TAG
docker push $REGISTRY/$IMAGE_NAME:$TAG
Example result:
localhost:5000/tickrtxt-app:1719859382
Self-Hosted Runner Requirement
This workflow runs on a self-hosted runner:
runs-on: self-hosted
The runner must have the following installed:
Docker
Trivy
Git
Network access to the registry
Prerequisites
Before using this pipeline, ensure the following are available on the runner host:
Docker
Install Docker and verify:
docker --version
Trivy
Install Trivy for vulnerability scanning:
trivy --version
Install example:
sudo apt install trivy
Local Docker Registry
Run a local registry if not already available:
docker run -d -p 5000:5000 --name registry registry:2
Verify:
http://localhost:5000/v2/_catalog
Example Workflow File
Location:
.github/workflows/docker-image-ci.yml
This workflow handles the full CI process from build → scan → publish.
DevSecOps Best Practices Implemented
This pipeline follows several DevSecOps principles:
Shift-left security via automated vulnerability scanning
Immutable build artifacts
Private container registry
Automated CI validation
Self-hosted infrastructure control
Potential Improvements
Future enhancements could include:
Failing builds on critical vulnerabilities
SBOM generation
Container signing (Cosign)
SAST scanning
Dependency scanning
Kubernetes deployment automation
Image retention policies
