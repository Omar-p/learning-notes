# 📏 API Performance Measures: SLIs, SLOs, and SLAs

When designing and operating APIs, it's essential to define and monitor performance from the perspective of both **internal stakeholders (developers, operations)** and **external stakeholders (users, customers, partners)**.

To align expectations and drive accountability, three key concepts are used:

* **SLI** – *Service Level Indicator* (What we measure)
* **SLO** – *Service Level Objective* (The target we aim for)
* **SLA** – *Service Level Agreement* (The formal commitment)

---

## 🔍 Service Level Indicators (SLIs)

> **SLIs are precise, quantifiable metrics that indicate the performance of a service.**

They answer the question: **“How is our system performing right now?”**

| **Indicator**         | **Definition**                                                          | **Example**                                        |
| --------------------- | ----------------------------------------------------------------------- | -------------------------------------------------- |
| **Request Latency**   | Time taken to return a response for a request.                          | `p95 latency < 300ms for search endpoint`          |
| **Error Rate**        | Ratio of failed requests to total requests within a time window.        | `HTTP 5xx errors / total requests`                 |
| **System Throughput** | Number of requests processed per second/minute/hour.                    | `1,000 requests per second`                        |
| **Availability**      | Percentage of time the service is accessible and functioning correctly. | `99.9% availability over 30 days`                  |
| **Durability**        | Probability that data is preserved without loss over time.              | `11 nines of durability for stored receipts in S3` |

> Choose SLIs that are observable, measurable, and reflect the user’s experience.

---

## 🎯 Service Level Objectives (SLOs)

> **SLOs are specific goals set for SLIs that represent acceptable service performance.**

They answer the question: **“What level of service should we provide?”**

SLOs serve as internal targets for engineering teams and are used for prioritization, alerting, and incident management.

### 🔸 Example:

> “99% of all search requests should return a response within 200 milliseconds.”

Or in a mathematical format:

$$
\text{P99}(\text{Latency}) < 200 \, \text{ms}
$$

### ✅ Best Practices for Defining SLOs:

* **Simplicity & Clarity**: Make sure your SLOs are easily understood by both engineers and business teams.
* **Focus on Impact**: Only define SLOs for indicators that impact user experience.
* **Avoid Absolutes**: Instead of `"100% uptime"`, use `"99.95% uptime"`—real-world systems always fail at some point.
* **Set Error Budgets**: An *error budget* defines how much unreliability is acceptable. Example: if your SLO is 99.9% uptime, you have 43 minutes and 49 seconds of acceptable downtime per month.

---

## 🤝 Service Level Agreements (SLAs)

> **SLAs are formal, legally binding contracts between service providers and consumers.**

They define the **expected level of service** and outline **remedies or penalties** if commitments aren’t met.

### ✍️ What SLAs Include:

* **Service Scope**: What’s being offered (e.g., API endpoints, storage, support).
* **Performance Metrics**: Uptime, latency, throughput, etc.
* **Monitoring & Reporting**: How performance is measured and reported.
* **Remedies**: Service credits, discounts, or penalties if the SLA is violated.

### 🧾 Example SLA Statement:

> “The API will be available 99.95% of the time, calculated monthly. If availability falls below this threshold, the client is entitled to a 10% service credit.”

### ⚠️ Note:

> SLAs are often **less aggressive** than SLOs. You might target 99.95% uptime as an SLO internally, but only guarantee 99.9% in the SLA to allow room for operational flexibility.

---

## 🧠 Summary: Comparing SLI vs. SLO vs. SLA

| Term    | Description                              | Audience               | Binding? | Example                             |
| ------- | ---------------------------------------- | ---------------------- | -------- | ----------------------------------- |
| **SLI** | Measurable performance metric            | Engineers, Ops         | ❌        | `95% of requests < 300 ms latency`  |
| **SLO** | Internal target goal for a given SLI     | Engineering, Product   | ❌        | `Availability ≥ 99.9%`              |
| **SLA** | Formal service commitment with penalties | Customers, Legal Teams | ✅        | `Service credits if uptime < 99.5%` |

---

## 📚 Final Thought

Using SLIs, SLOs, and SLAs together helps you:

* Build more **reliable and trustworthy systems**.
* Establish **clear priorities** for development and operations.
* Provide **transparency** to customers and stakeholders.
* Avoid burnout by using **error budgets** to balance innovation and stability.
