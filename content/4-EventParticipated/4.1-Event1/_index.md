---
title: "Event 1 - AWS Cloud, Monitoring, and Security Agent"
date: 2025-08-13
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Event Report: AWS Cloud, Monitoring, and Security Agent

### Event Information

- **Time:** 09:00, July 11, 2026
- **Location:** 26th Floor, Bitexco Tower, 02 Hai Trieu Street, Saigon Ward, Ho Chi Minh City
- **Role:** Attendee

The event featured three sessions covering foundational AWS knowledge, service monitoring from the user's perspective, and the use of an AI security agent to protect web applications.

## 1. Inside the Exam: AWS Cloud Practitioner

**Speaker:** Ngo Le Tan Huy

The first session explained the AWS Certified Cloud Practitioner (CLF-C02) exam and provided a practical preparation strategy for learners beginning their AWS journey.

### Key topics

- The exam includes **65 questions**, lasts **90 minutes**, and requires a passing score of **700 out of 1,000**.
- Its four domains cover Cloud Concepts; Security and Compliance; Cloud Technology and Services; and Billing, Pricing, and Support.
- Cloud Concepts includes the benefits of AWS Cloud, the AWS Well-Architected Framework, and the AWS Cloud Adoption Framework.
- Security and Compliance emphasizes the Shared Responsibility Model, IAM, infrastructure security, and compliance.
- Cloud Technology and Services covers global infrastructure, compute, storage, databases, and networking.
- Billing, Pricing, and Support introduces EC2 pricing models, cost-management tools, and AWS Support plans.

### Exam preparation lessons

- Associate each AWS service with its **use cases and keywords**, rather than memorizing definitions alone.
- Review incorrect practice-test answers and pay close attention to decisive phrases such as “least cost” and “most scalable.”
- Use AWS Free Tier to gain hands-on experience with services such as EC2, S3, and IAM.
- Reinforce learning through AWS Skill Builder, mock exams, and suitable study materials.

## 2. SLA and Monitoring: From SLA to Monitoring What Really Matters

**Speaker:** Nguyen Huynh Son

The second session explained Service Level Agreements and demonstrated why healthy infrastructure does not necessarily produce a healthy user experience.

### Key topics

- An SLA defines the expected service level and supports accountability, risk management, and performance measurement.
- Monitoring should begin with the **customer journey** and business metrics, followed by the application, infrastructure, and AWS service layers.
- A dashboard may show healthy CPU, memory, and HTTP status metrics while users are still unable to sign in or complete transactions.
- An `/api` endpoint may return `200 OK` while `/login` fails because of a database connection issue.
- A useful alerting workflow connects an important metric to a CloudWatch Alarm, an SNS topic, and email or Slack notifications before customers report the problem.

### Lessons learned

- **Healthy infrastructure is not the same as a healthy user experience.**
- Teams must understand what their SLA covers and which responsibilities remain with application and operations teams.
- Monitoring should include critical user actions such as login, search, payment, and checkout—not only infrastructure utilization.
- Systems should be designed with failure in mind so a single fault does not stop the entire service.

## 3. Securing Your Web Apps with AWS Security Agent

**Speaker:** Thinh Nguyen

The final session introduced AWS Security Agent as a frontier agent that can automate security work across the software development lifecycle.

### Key topics

- Manual penetration tests may take weeks, cost significantly more, and vary according to the specialist's experience.
- The agent can plan, reason, and execute security tasks with limited human intervention.
- It supports three major activities: **design review**, **code review**, and **automated penetration testing**.
- Design Security Review checks Markdown documents or Terraform code against managed packs such as PCI DSS, NIST CSF, and AWS Well-Architected.
- Code Security Review integrates with GitHub or GitLab pull requests, comments on vulnerable lines, and suggests patches.
- Automated Pentesting can authenticate like a real user, test multi-step exploit chains, and provide verifiable evidence.
- The session presented task-hour pricing and free-tier allowances for design review, code review, and agent trials.

### Important limitations

- MFA, biometrics, and mTLS may prevent automated authentication.
- Business-logic vulnerabilities can be difficult to detect without deep domain context.
- Task-hours may accumulate quickly, so usage and cost must be monitored.
- AI agents extend a security team's capabilities but do not completely replace human expertise and governance.

## Value gained

The three sessions connected foundational cloud knowledge with operations and security:

1. Build structured AWS knowledge through the Cloud Practitioner learning path.
2. Shift monitoring from resource health to real customer journeys and outcomes.
3. Understand how AI agents can assist architecture review, source-code review, and penetration testing.
4. Recognize that reliable cloud systems require knowledge, observability, and security throughout their lifecycle.

## Participation evidence

![Photo taken while attending the AWS Cloud, Monitoring, and Security Agent event](/images/4-EventParticipated/4.1-Event1/event1-evidence.jpg)

*Photo taken at the event venue.*

