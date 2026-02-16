# AWS Well-Architected Framework and Cloud Adoption Framework Assessment

## Summary

This report covers the migration of a two-tier web application from on-premises servers to AWS. The application has a frontend web server and a backend database. I used the AWS Well-Architected Framework (WAF) and Cloud Adoption Framework (CAF) to review the current setup and design a better solution.

---

## Task 1: Review of Existing Architecture

### Current Setup

The on-premises application has four main parts:

| Component | What It Does |
|-----------|--------------|
| Frontend | Web server that runs the user interface |
| Backend Database | Stores all application data (MySQL or PostgreSQL) |
| Network | Firewall and internal networking |
| Storage | Local hard drives for files and database |

### Problems Found

| Problem | What Could Happen | Risk Level |
|---------|-------------------|------------|
| Single server | If it fails, everything goes down | High |
| No backups | Data could be lost forever | High |
| One location only | A power outage takes down the whole app | High |
| Weak security rules | Hackers could get in | High |
| Manual scaling | Site crashes during busy periods | Medium |
| No monitoring | Problems go unnoticed until users complain | Medium |
| No disaster plan | Long downtime if something major breaks | High |
| Manual updates | Security patches get delayed | Medium |

---

## Task 2: AWS Well-Architected Framework Evaluation

### Five Pillars Assessment Table

| Pillar | What I Found | What Should Change | AWS Services to Use |
|--------|--------------|-------------------|---------------------|
| Operational Excellence | Everything is done manually, no monitoring | Set up automated deployments and alerts | CloudFormation, CloudWatch, Systems Manager |
| Security | Security rules are too open, no encryption | Lock down access, encrypt data | IAM, WAF, KMS, Secrets Manager |
| Reliability | Single server, no backups, no failover | Add redundancy across multiple zones | RDS Multi-AZ, Load Balancer, S3 for backups |
| Performance Efficiency | Fixed capacity, no caching | Add auto-scaling and caching | EC2 Auto Scaling, ElastiCache, CloudFront |
| Cost Optimization | Paying for hardware whether we use it or not | Switch to pay-as-you-go model | Cost Explorer, Trusted Advisor, Savings Plans |

### Detailed Analysis

#### Operational Excellence

What works now: The application runs and serves users.

What needs fixing: There is no way to deploy changes quickly or see if something is wrong. Everything is manual.

How to fix it:
- Use CloudFormation to define infrastructure as code
- Set up CloudWatch dashboards to monitor the system
- Use Systems Manager for automated patching

#### Security

What works now: There is a basic firewall.

What needs fixing: The firewall rules let in too much traffic. Data is not encrypted. There is no proper access control.

How to fix it:
- Set up IAM roles so people only have access to what they need
- Turn on encryption with KMS
- Add AWS WAF to block common attacks
- Store passwords in Secrets Manager

#### Reliability

What works now: The app has been running without major crashes.

What needs fixing: If the server dies, everything stops. There are no backups. No plan for disasters.

How to fix it:
- Use RDS Multi-AZ so the database has a backup copy
- Put a load balancer in front to spread traffic
- Set up automatic backups to S3
- Write a disaster recovery plan

#### Performance Efficiency

What works now: The server handles normal traffic fine.

What needs fixing: During busy times, the site slows down or crashes. No caching means the database gets hit for every request.

How to fix it:
- Add auto-scaling so more servers spin up when needed
- Use ElastiCache to store frequently accessed data in memory
- Use CloudFront to serve static files faster

#### Cost Optimization

What works now: Costs are predictable since we own the hardware.

What needs fixing: We pay the same amount whether we use 10% or 100% of the servers. No visibility into waste.

How to fix it:
- Use Cost Explorer to see where money goes
- Buy Savings Plans for workloads that run all the time
- Use Trusted Advisor to find oversized resources

---

## Task 3: Cloud Adoption Framework Readiness Assessment

### Business Perspective

Moving to AWS makes sense from a business standpoint. Right now, a big chunk of the IT budget goes to maintaining old servers instead of building new features. The cloud lets us pay only for what we use, which should cut costs by around 30% over three years. But this only works if leadership is on board. The finance team and IT managers need to agree on what success looks like. We should track monthly cloud spending and compare it to what we used to spend on hardware. Regular reviews will help us catch any surprises early and make sure the move is actually saving money as expected.

### People Perspective

The IT team knows on-premises infrastructure well but has little cloud experience. This is a problem because cloud works differently. We need to invest in training before the migration starts. At minimum, a few team members should get AWS Solutions Architect certification. It also helps to pick a few people to be cloud experts who can guide the rest of the team. Change is hard, and some staff might resist learning new tools. Management needs to explain why this move matters and give people time to learn. Hiring an AWS consultant for the first few months could speed things up.

### Governance Perspective

Before moving anything to the cloud, we need rules in place. This includes deciding who can access what, how to classify data, and how to track costs by project. AWS has tools like Config and CloudTrail that help with compliance and auditing. We should also set up a tagging system so every resource is labeled with the project and owner. This makes it easy to see who is spending what. A small governance team with people from IT, security, and finance should approve major changes. This prevents one team from making decisions that affect everyone else.

### Platform Perspective

The goal is to build a solid foundation on AWS that other teams can use. The architecture will include EC2 for compute, RDS for the database, and a VPC with separate subnets for security. All infrastructure should be defined in code using CloudFormation so deployments are repeatable. We will also set up a multi-account structure with Control Tower to keep production separate from testing. CI/CD pipelines with CodePipeline and CodeDeploy will automate releases. The platform team handles the shared stuff like logging and monitoring so application teams can focus on their code.

### Security Perspective

Security in the cloud is a shared job. AWS secures the physical data centers, but we are responsible for securing our applications and data. The plan is to use multiple layers of protection. Security groups and network ACLs control what traffic can reach our servers. All data gets encrypted, both when stored and when moving over the network. IAM controls who can do what, and everyone with admin access needs multi-factor authentication. GuardDuty watches for threats, and Security Hub gives a single view of all security issues. We also need runbooks so the team knows what to do if something bad happens.

### Operations Perspective

Running things in the cloud requires new skills. The ops team will shift from managing physical servers to managing cloud resources. CloudWatch becomes the main tool for monitoring. It tracks metrics, stores logs, and sends alerts when something looks wrong. Systems Manager handles patching and configuration. We need to define SLAs for uptime (aiming for 99.9%) and track how we are doing against those targets. When incidents happen, we do a review afterward to figure out what went wrong and how to prevent it. Over time, the goal is to automate repetitive tasks so the team can focus on improvements instead of firefighting.

---

## Task 4: Improved Architecture Design

### Overview

The new design fixes the problems found in the assessment. It spreads the application across two availability zones so a failure in one zone does not take everything down. Auto-scaling handles traffic spikes. Managed services reduce the maintenance burden.

### Architecture Diagram

![]

### Key Components

| Component | Purpose | Which Pillar It Helps |
|-----------|---------|----------------------|
| CloudFront | Delivers content faster by caching at edge locations | Performance |
| AWS WAF | Blocks common web attacks | Security |
| Load Balancer | Spreads traffic across servers | Reliability |
| EC2 Auto Scaling | Adds or removes servers based on demand | Performance, Cost |
| RDS Multi-AZ | Keeps a backup database ready for failover | Reliability |
| ElastiCache | Caches data to reduce database load | Performance |
| S3 | Stores backups and static files | Reliability, Cost |
| CloudWatch | Monitors everything and sends alerts | Operations |
| CloudTrail | Logs all API calls for auditing | Security, Operations |

### How This Design Addresses Each Pillar

1. **Operational Excellence**: CloudWatch monitors the system. CloudFormation makes deployments repeatable. CloudTrail logs all changes.

2. **Security**: WAF blocks attacks. Private subnets hide servers from the internet. KMS encrypts data. IAM controls access.

3. **Reliability**: Two availability zones mean one failure does not break everything. RDS has automatic failover. S3 stores backups.

4. **Performance Efficiency**: CloudFront caches content. ElastiCache reduces database hits. Auto-scaling matches capacity to demand.

5. **Cost Optimization**: Auto-scaling means we only pay for what we use. S3 lifecycle rules move old data to cheaper storage.

---

## Reflection

Working through this assessment taught me that moving to the cloud is more than just setting up servers somewhere else. The Well-Architected Framework gave me a structured way to look at the system and find gaps I would have missed otherwise. For example, I knew backups were important, but the framework pushed me to also think about how fast we could recover and whether we had tested the process. The Cloud Adoption Framework showed me that technology is only part of the picture. If the team does not have the right skills, or if there are no governance rules, the migration will struggle. I also learned how the different pillars connect. Good security supports reliability because encrypted data is protected from corruption. This kind of thinking will help me design better systems in the future.

---

## AWS Services Reference

| Service | Category | What We Use It For |
|---------|----------|-------------------|
| EC2 | Compute | Run the web servers |
| RDS | Database | Managed database with automatic failover |
| Elastic Load Balancer | Networking | Distribute traffic to healthy servers |
| CloudFront | CDN | Cache and serve static content |
| WAF | Security | Block malicious requests |
| ElastiCache | Caching | Speed up data access |
| S3 | Storage | Store backups and files |
| CloudWatch | Monitoring | Track metrics and logs |
| CloudTrail | Audit | Log all API activity |
| IAM | Identity | Control who can access what |
| KMS | Security | Manage encryption keys |
| Secrets Manager | Security | Store database passwords |
| Config | Governance | Check compliance |
| CloudFormation | Infrastructure | Define resources as code |
