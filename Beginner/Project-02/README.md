# TIER 1 — Beginner (Linux & AWS Foundation)

**Project:** Deploy Scalable VPC Architecture on AWS | Phase: 1 — Architecture & Setup

## 🔍 What We Are Building

**Project:** Deploy a Modular and Scalable Virtual Network Architecture on AWS

This project builds a **production-grade, dual-VPC AWS architecture** with the following highlights:

- **VPC 1 (Bastion VPC — 192.168.0.0/16):** A secure management network. A Bastion Host lives here, accessible via a VPN/Transit Gateway connection. DevOps engineers SSH into private app servers through this host.

- **VPC 2 (App VPC — 172.32.0.0/16):** The main application network. Highly available app servers run in **private subnets** across two Availability Zones (1a and 1b), behind a **Network Load Balancer** in a public subnet. An **Auto Scaling Group** ensures the app scales automatically. A **NAT Gateway** allows private servers to reach the internet for updates without being exposed publicly.

- **Transit Gateway:** Connects both VPCs privately so the Bastion Host can reach app servers without going over the public internet.

- **Golden AMI:** A pre-baked EC2 image with Apache, AWS CLI, CloudWatch Agent, Git, and SSM Agent pre-installed — used by the Auto Scaling Group.

- **CloudWatch Flow Logs:** Both VPCs send network traffic logs to CloudWatch for monitoring and auditing.

- **Route53:** Domain name routes traffic to the NLB so users access the app via a clean URL.


## 🏗️ Architecture Analysis & My Recommendation

The uploaded diagram matches the project well. However, I want to propose **one improvement**:

> **Original:** Uses a Network Load Balancer (NLB — Layer 4)
> **My Recommendation:** Use an **Application Load Balancer (ALB — Layer 7)** instead

**Why ALB is better here:**
- This is an HTTP web application — ALB is designed for HTTP/HTTPS traffic
- ALB supports path-based routing, health checks on HTTP, and better access logs
- ALB integrates natively with AWS WAF for security
- NLB is ideal for TCP/UDP (databases, gaming, etc.) — not web apps

**I recommend to use ALB instead of NLB.** It is a more realistic, production-appropriate choice for a web application portfolio project.

