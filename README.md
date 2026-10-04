MLOps Inference Platform: Architecture & Operations README

# Enterprise MLOps Inference Platform

## Table of Contents
1. [General Architecture](#1-general-architecture)
2. [Hardware Requirements](#2-hardware-requirements)
3. [Used Technologies](#3-used-technologies)
4. [System Components](#4-system-components)
5. [Host Filesystem Access & Storage](#5-host-filesystem-access--storage)
6. [SFTP Server Setup](#6-SFTP-server-setup)
7. [MLOps Implementation & Workflows](#7-mlops-implementation--workflows)
8. [Monitoring & Logging (EFK & Prometheus)](#8-monitoring--logging-efk--prometheus)
9. [Release Strategy & Operational SLOs](#9-release-strategy--operational-slos)

---

## 1. General Architecture
* **Managed Kubernetes (EKS):** The core environment operates on an AWS-managed Kubernetes cluster where applications run as containerized microservices grouped into dedicated namespaces[cite: 7].
* **Network Access & Security:** Outbound public internet access is strictly routed through an internal forward proxy with whitelisted communication paths[cite: 7].
* **Ingress & Load Balancing:** External access to model endpoints, administrative tools, and monitoring is handled via Elastic Load Balancers (ELBs) backed by Nginx Ingress Controllers[cite: 7].

<img width="10788" height="7324" alt="image" src="https://github.com/user-attachments/assets/584ea648-91b1-4ab9-b9e2-c355d633ea74" />

## 2. Hardware Requirements
* **EKS Node Pools:** Compute resources are managed via unified EC2 node pools, making long-term OS patching obsolete by allowing old nodes to be replaced with fully updated ones through Kubernetes native mechanisms[cite: 7].
* **Managed Services:** Managed services (e.g., AWS RDS for databases) are prioritized over self-hosted solutions wherever economically feasible and up-to-date[cite: 7].

## 3. Used Technologies
* **Docker & Kubernetes:** Container packaging and orchestration[cite: 7].
* **Jenkins & GitLab:** CI/CD release automation and configuration storage[cite: 7].
* **Terraform:** Modularized infrastructure as code for reproducible AWS deployments[cite: 7].
* **KServe, Knative & Triton:** Concurrency-based model serving and GPU node autoscaling[cite: 7].
* **Prometheus, Grafana & ELK:** System monitoring, SLI/SLO tracking, and centralized log aggregation[cite: 7].

## 4. System Components
* **Ingress / Load Balancers:** Reverse proxies managing HTTP/REST and monitoring traffic[cite: 7].
* **Identity Management:** Keycloak for single sign-on (SSO) authentication[cite: 7].
* **Data Persistence:** AWS RDS (PostgreSQL/MySQL), self-hosted MongoDB (>4.0), and Apache Kafka messaging[cite: 7].
* **Model Registry:** Amazon S3 object storage for model weights and version artifacts[cite: 7].

## 5. Host Filesystem Access & Storage
* **Local Volumes:** High-performance attached local data disks for latency-sensitive components like Kafka brokers and Elasticsearch/Prometheus indices[cite: 7].
* **Network Storage:** An NFS cluster backed by DRBD and AWS EBS snapshots to satisfy high-availability shared file-share requirements[cite: 7].

## 6. SFTP Server Setup
* **Isolation:** Hosted on a dedicated EC2 instance with strict IP whitelisting in security groups and chrooted POSIX ACL environments[cite: 7].
* **Security Mitigation:** Completely isolated from central IAM user management, utilizing syslog forwarding for audit logs[cite: 7].

## 7. MLOps Implementation & Workflows
* **Model Deployment Manifests:** Uses KServe `InferenceService` custom resources referencing S3 model URIs (`s3://company-mlops-model-registry/...`) with automatic GPU scheduling (`nvidia.com/gpu: "1"`)[cite: 7].
* **CI/CD Pipelines:** Automated GitHub Actions / Jenkins pipelines build model container images upon pushing to model repositories and push them to Amazon ECR[cite: 7].

## 8. Monitoring & Logging (EFK & Prometheus)
* **Metrics Collection:** Prometheus scrapes hardware telemetry (GPU temperature, VRAM, node metrics) and model SLIs (latency, throughput)[cite: 7].
* **Logging Pipeline:** Fluentbit daemonsets collect structured container stdout/stderr logs and ship them to Elasticsearch for analysis via Kibana[cite: 7].

## 9. Release Strategy & Operational SLOs
* **Canary Deployments:** New model versions are rolled out via GitOps configuration updates with weighted traffic splitting (e.g., 10% canary, 90% stable)[cite: 7].
* **Automated Rollbacks:** Multi-window multi-burn-rate alerts monitor error budgets; if latency or error thresholds are breached on a canary release, traffic automatically reverts to the stable version[cite: 7].


