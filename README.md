# 🛒 RoboShop — Automated Microservices Deployment with Ansible

<div align="center">

[![Ansible](https://img.shields.io/badge/Ansible-2.15%2B-EE0000?logo=ansible&logoColor=white)](https://www.ansible.com/)
[![AWS](https://img.shields.io/badge/Cloud-AWS-FF9900?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Linux](https://img.shields.io/badge/OS-RHEL%20%2F%20CentOS-EE0000?logo=linux&logoColor=white)](https://www.linux.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

**Automated Configuration, Microservices Orchestration, and Multi-Database Provisioning for RoboShop E-Commerce**

---

</div>

## 📌 Executive Summary

This repository contains automated Ansible playbooks for provisioning and managing the complete **RoboShop multi-tier e-commerce platform**. RoboShop is an enterprise-grade microservices demo consisting of 11 distinct components: frontend web servers, polyglot application backends (Node.js, Java Spring Boot, Python, Go), and multiple distributed datastores (MongoDB, Redis, MySQL, RabbitMQ).

**Core Capabilities:**
- ⚙️ **Automated Microservice Setup**: Playbooks configure users, clone repositories, install package dependencies, compile binaries, and configure systemd services.
- 📦 **Polyglot Database Management**: Automated installation, authentication, schema imports, and access restriction for 4 diverse databases.
- 🔄 **Route 53 DNS & Service Discovery**: Integrates with private Route 53 zones to provide seamless DNS resolution across all microservices.

---

## 🏛 Architecture Diagram

![RoboShop Microservices Architecture](docs/architecture-diagram.png)

---

## 🧰 Microservices & Component Map

| Layer | Component | Runtime / Tool | Port | Storage / Messaging Backend |
| :--- | :--- | :--- | :--- | :--- |
| **Web Proxy** | **Frontend** | Nginx Reverse Proxy | `80` | Routes `/api/*` traffic to backend services |
| **Microservice** | **Catalogue** | Node.js (Express) | `8080` | MongoDB (`27017`) product catalog data |
| **Microservice** | **User** | Node.js (Express) | `8080` | MongoDB (`27017`) user accounts & Redis tokens |
| **Microservice** | **Cart** | Node.js (Express) | `8080` | Redis In-Memory Cache (`6379`) |
| **Microservice** | **Shipping** | Java Spring Boot | `8080` | MySQL 8.0 (`3306`) transactional orders |
| **Microservice** | **Payment** | Python 3 | `8080` | RabbitMQ AMQP Broker (`5672`) |
| **Microservice** | **Dispatch** | Go (Golang) | `8080` | RabbitMQ Queue Consumer (`5672`) |
| **NoSQL DB** | **MongoDB** | MongoDB Community | `27017` | Pre-loaded with catalogue and user collections |
| **RDBMS** | **MySQL** | MySQL Community 8.0 | `3306` | Pre-loaded with shipping schemas and cities |
| **Cache** | **Redis** | Redis Server | `6379` | High-throughput ephemeral key-value store |
| **Broker** | **RabbitMQ** | Erlang / RabbitMQ | `5672` | Asynchronous decoupled order processing queue |

---

## 🚀 Execution & Usage

### 1. Provision Infrastructure & Private DNS
```bash
ansible-playbook -i inventory.ini ec2-r53.yaml
```

### 2. Configure Database Tier First
```bash
ansible-playbook -i inventory.ini mongodb.yaml
ansible-playbook -i inventory.ini mysql.yaml
ansible-playbook -i inventory.ini redis.yaml
ansible-playbook -i inventory.ini rabbitmq.yaml
```

### 3. Deploy Application Microservices & Frontend
```bash
ansible-playbook -i inventory.ini catalogue.yaml
ansible-playbook -i inventory.ini user.yaml
ansible-playbook -i inventory.ini cart.yaml
ansible-playbook -i inventory.ini shipping.yaml
ansible-playbook -i inventory.ini payment.yaml
ansible-playbook -i inventory.ini dispatch.yaml
ansible-playbook -i inventory.ini frontend.yaml
```

---

## 🛡️ Reliability & Security Design
- **Process Isolation**: Microservices run under a dedicated `roboshop` system user with `/app` directory ownership.
- **Failover & Recovery**: Handled via systemd unit files with `Restart=always` and `RestartSec=5s`.
- **Private Subnet Placement**: Databases listen strictly on internal network interfaces with firewall isolation.
