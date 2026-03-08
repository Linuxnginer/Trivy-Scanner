# Docker Image CI – DevSecOps Pipeline

This repository contains a **DevSecOps-focused Continuous Integration (CI) workflow** that automatically builds, scans, and publishes Docker images whenever code is pushed or a pull request is opened against the `main` branch.

The pipeline is designed for **on-premise environments** using a **self-hosted GitHub Actions runner** and a **local Docker registry**.

---

# Overview

This CI pipeline performs the following steps:

1. Checkout Source Code
2. Generate a Unique Image Tag
3. Build the Docker Image
4. Run Security Scanning (Trivy)
5. Push the Image to a Local Docker Registry

The workflow ensures every build is **traceable, scanned for vulnerabilities, and stored in a private registry**.

---

# Architecture
