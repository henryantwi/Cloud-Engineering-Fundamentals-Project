# AWS Well-Architected and Cloud Adoption Framework Assessment

## About This Project

This project is an assessment of migrating a two-tier web application to AWS. The application has a frontend and a database backend. I reviewed the current on-premises setup and designed an improved architecture using AWS best practices.

## What's in This Repository

```
README.md                      - This file
aws_waf_caf_assessment.md      - Main report with all tasks completed
architecture-diagram.png       - Visual diagram of the new architecture
```

## My Approach

### Step 1: Reviewed the Current Setup

I started by listing out what the existing system has: a web server, a database, some networking, and storage. Then I looked for problems. The biggest issues were having only one server (single point of failure), no backups, and security rules that were too open.

### Step 2: Applied the Well-Architected Framework

For each of the five pillars, I asked: what's working, what's broken, and how do we fix it?

- **Operational Excellence** - No automation or monitoring. Fix: CloudFormation, CloudWatch.
- **Security** - Open firewall rules, no encryption. Fix: IAM, WAF, KMS.
- **Reliability** - One server, no backups. Fix: Multi-AZ RDS, load balancer, S3 backups.
- **Performance** - Can't handle traffic spikes. Fix: Auto-scaling, caching.
- **Cost** - Paying for idle capacity. Fix: Pay-as-you-go, right-sizing.

### Step 3: Assessed Organizational Readiness

Using the Cloud Adoption Framework, I looked at six areas:

- **Business** - Does leadership support this? What's the expected ROI?
- **People** - Does the team have cloud skills? What training do they need?
- **Governance** - What rules and policies need to be in place?
- **Platform** - What's the technical foundation look like?
- **Security** - How do we protect data and systems?
- **Operations** - How will we run and monitor things day-to-day?

### Step 4: Designed a Better Architecture

Based on what I found, I designed a new setup with:

- Two availability zones so one failure doesn't take everything down
- Auto-scaling so capacity matches demand
- RDS Multi-AZ for database high availability
- CloudFront and ElastiCache for faster performance
- WAF, private subnets, and encryption for security
- CloudWatch for monitoring

## Key Improvements

| Area | Before | After |
|------|--------|-------|
| Availability | Single server | Multi-AZ with load balancing |
| Scaling | Manual | Automatic based on traffic |
| Security | Basic firewall | WAF, private subnets, encryption |
| Backups | None | Automated daily backups to S3 |
| Monitoring | Limited | CloudWatch dashboards and alerts |
| Cost Model | Fixed hardware costs | Pay for what you use |

## Deliverables Completed

- WAF assessment table (Task 2)
- CAF readiness summary for all six perspectives (Task 3)
- Improved architecture diagram and description (Task 4)
- Reflection on what I learned

## My References

- AWS Well-Architected Framework: https://aws.amazon.com/architecture/well-architected/
- AWS Cloud Adoption Framework: https://aws.amazon.com/professional-services/CAF/
