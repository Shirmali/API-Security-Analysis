
## 1. API Security Fundamentals

APIs are the backbone of modern applications, enabling communication between mobile apps, web apps, microservices, and third-party integrations. Because they often expose sensitive operations and data, APIs are a frequent target for attackers.

### Core Principles

- **Authentication** – Verify who is making the request (e.g., OAuth2, JWT).
- **Authorization** – Enforce what that user or system is allowed to do.
- **Data Protection** – Encrypt data in transit (TLS) and at rest.
- **Input Validation** – Reject malformed or malicious requests.
- **Rate Limiting** – Prevent abuse, scraping, or denial-of-service.
- **API Inventory Management** – Track versions, endpoints, and dependencies.
- **Monitoring & Logging** – Detect anomalies and enable incident response.
- **Least Privilege** – Give only the access necessary, nothing more.

---

## 2. OWASP API Security Top 10 (2023)

### API1:2023 – Broken Object Level Authorization (BOLA)

**Risk:**  
Attackers manipulate object IDs in requests to access or modify other users’ data.

**Example:**  
