---
title: "Event 2 - Agentic AI Build Week Community Day"
date: 2025-08-13
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Event Report: Agentic AI Build Week Community Day

### Event Information

- **Time:** 09:00, August 13, 2025
- **Location:** 26th Floor, Bitexco Tower, 02 Hai Trieu Street, Saigon Ward, Ho Chi Minh City
- **Role:** Attendee

The event brought teams together to share products, architectures, and lessons from Agentic AI Build Week. Four sessions demonstrated how AI agents can support solution design, corporate-signal analysis, crowd operations, and multi-channel ordering.

## 1. Solution Architect Professional AI Native App — Plan V

**Members:** Pham Tien Thuan Phat, Huynh Hoang Long, Le Minh Nghia, Tran Dai Vi, and Nguyen An.

Plan V addressed the time-consuming work of reading requirements, drafting architecture, producing diagrams, and estimating cloud costs under tight deadlines.

### Key capabilities

- Accept natural-language and structured project requirements.
- Extract requirements and produce a **Requirements Catalogue** within minutes.
- Draft high-level, hybrid-cloud-aware architecture options.
- Generate editable Draw.io and official AWS Architecture Icon diagrams.
- Produce directional AWS cost estimates for `ap-southeast-1`.
- Refine outputs through chat and project-specific instructions.

Instead of starting from a blank page, a Solution Architect receives a grounded first draft to review with customers. The solution can also assist with Infrastructure as Code and generate cost estimates alongside the architecture.

## 2. Signal Scout — Detecting strategic corporate change

**Members:** Le Tan Luc, Do Hoang Hieu, Trieu Quoc Hao, Nguyen Van Duy Khiem, Nguyen Cong Minh, and Nguyen Tran Minh Quan.

Signal Scout collects and connects scattered corporate signals to identify strategic shifts, restructuring activity, and emerging risks.

### Key capabilities

- Collect evidence and analyze financial and operational metrics.
- Connect signals into verifiable timelines and narratives.
- Support **Maintain, Adapt, or Accelerate** decisions while keeping humans in control.
- Deliver dashboards, reports, and risk alerts to strategy, risk, and competitive-intelligence teams.
- Use services such as Amazon Bedrock, AgentCore, Amplify, DynamoDB, Lambda, API Gateway, S3, Cognito, CloudWatch, and WAF.
- Compare workload levels and optimize the architecture for operating cost.

An AI decision-support system is valuable only when each conclusion is tied to transparent evidence. Architecture decisions must balance model quality, cost, traceability, and human authority.

## 3. Hackathon Journey and S.H.E.P.H.E.R.D — Team 3KA

**Members:** Huynh An Khuong, Nguyen Quoc Huy, Ngo Quang Khoi, Hoang Le Thanh Duc, Dang Nguyen Phuoc Loc, and Dang Truong Hung.

Team 3KA shared the experience of building, failing, debugging, and delivering an MVP within 24 hours. S.H.E.P.H.E.R.D helps venue operators monitor crowd density and respond to congestion early.

### The solution

- Analyze camera streams and track people using YOLO and ByteTrack.
- Measure crowd density, queue conditions, and early congestion signals.
- Predict overcrowding, create proactive alerts, and recommend staff actions.
- Combine Amazon SageMaker, Amazon Bedrock AgentCore, Strands Agent, and a React monitoring dashboard.
- Use an Autonomous Monitor for continuous analysis and an Operator Copilot for natural-language questions grounded in live metrics.

### Hackathon lessons

- Major challenges included limited AI/AWS experience, tight time, broken code, inference latency, and tracking reliability.
- A clear definition of done, prepared accounts and templates, assigned roles, and an early demo plan reduce risk.
- One small, finished capability is more valuable than a large but broken idea.
- Hackathons build collaboration and adaptability, and the people met during the journey matter as much as prizes.

## 4. KFC Bot Agent — One Team

**Members:** Anh Duy, Tran Dong, Doan Trung, Minh Viet, and Anshul Roy.

One Team presented a conversational ordering agent that lets customers order through channels such as Zalo and Messenger without switching applications or creating another account.

### Key capabilities

- Understand items, quantities, variants, vouchers, cart state, and business errors.
- Follow an agent loop of **Goal → Plan → Tools → Act → Verify**.
- Add new channels through adapters, new businesses through connectors, and new capabilities through tools.
- Retrieve trusted business data, update carts, apply promotions, and verify the real order state.
- The slides reported reference figures of approximately **USD 0.006 per order**, **USD 88 per month**, **3–5 seconds end-to-end latency**, and roughly **60% less infrastructure code** with AgentCore.

Conversational ordering is more than a chatbot response. A useful agent must take action, enforce business rules, verify real system state, and prevent mistakes that create financial loss.

## Value gained

The four sessions reinforced five lessons:

1. Begin with a specific user problem and measurable value before selecting technology.
2. Design agents around tools, trusted data, and verification—not model responses alone.
3. Treat cost, latency, observability, and human control as architecture requirements from the beginning.
4. Keep MVP scope small, assign roles clearly, and prepare the demo early when working under time pressure.
5. AI agents can support many domains, but their outputs must remain transparent, verifiable, and grounded in real business workflows.

## Participation evidence

![Photo taken while attending Agentic AI Build Week Community Day](/images/4-EventParticipated/4.2-Event2/event2-evidence.png)

*Photo taken during a sharing session at the event.*

