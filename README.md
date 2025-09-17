
## 1. API Security Fundamentals

APIs are the backbone of modern applications, enabling communication between mobile apps, web apps, microservices, and third-party integrations. Because they often expose sensitive operations and data, APIs are a frequent target for attackers.

One of the most common types of APIs is **REST (Representational State Transfer)**. REST is not a protocol but an architectural style that uses standard HTTP methods such as:

- `GET` → retrieve data  
- `POST` → create new data  
- `PUT/PATCH` → update existing data  
- `DELETE` → remove data  

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

If user A can access user B’s data; that's BOLA.

**Real-World Case:**  
- In 2021, **Peloton** exposed workout and profile data of users due to BOLA. Attackers could access other users’ account info just by changing IDs.  

**Mitigations:**
- Enforce object-level access control on every request.
- Validate ownership of resources against the authenticated user.

---

### API2:2023 – Broken Authentication

**Risk:**  
Weak or flawed authentication allows account compromise or impersonation.

**Examples:**
- Predictable session tokens.
- Unlimited failed login attempts.
- Accepting expired JWTs.

**Real-World Case:**  
- **Facebook 2018 breach**: attackers exploited flaws in access tokens to hijack 50M accounts.  
- Multiple mobile apps have been found using hardcoded API keys that grant direct backend access.

**Mitigations:**
- Implement OAuth2 / OpenID Connect.
- Use short-lived, signed tokens with rotation.
- Require MFA for sensitive actions.
- Lock accounts after repeated failed attempts.

---

### API3:2023 – Broken Object Property Level Authorization (BOPLA)

**Risk:**  
APIs expose or allow modification of sensitive object properties without proper authorization.

**Example:**  

```
PATCH /api/v1/users/12345
{
"name": "Alice",
"role": "admin"
}
```

If unchecked, a user could escalate privileges.

**Real-World Case:**  
- **Uber 2016 breach** involved attackers gaining admin-level access to internal systems partly due to exposed properties in API payloads.  

**Mitigations:**
- Define allowlists of permitted fields.
- Enforce property-level authorization checks.

---

### API4:2023 – Unrestricted Resource Consumption

**Risk:**  
APIs without usage limits are vulnerable to denial-of-service or abuse.

**Examples:**
- Large file uploads.
- Excessive requests to search endpoints.
- Bulk data exports without restrictions.

**Real-World Case:**  
- **GitHub DDoS 2018** (1.35 Tbps): although not purely API-based, highlighted the scale of abuse possible when services don’t limit consumption.  
- Many financial APIs have suffered outages due to bots hammering rate-free endpoints.

**Mitigations:**
- Implement rate limits and quotas.
- Restrict payload sizes.
- Use pagination with capped result sets.
- Monitor and throttle abnormal traffic.

---

### API5:2023 – Broken Function Level Authorization

**Risk:**  
Sensitive functions are accessible without verifying user roles.

**Example:**  

```
DELETE /api/v1/admin/users/12345
Authorization: Bearer <normal_user_token>
```

If accepted, normal users could perform admin actions.

**Real-World Case:**  
- **Snapchat 2014 breach**: attackers accessed internal admin functions due to poor authorization checks.  

**Mitigations:**
- Enforce role-based or attribute-based access control.
- Separate admin and user endpoints.
- Apply deny-by-default for sensitive actions.

---

### API6:2023 – Unrestricted Access to Sensitive Business Flows

**Risk:**  
Attackers abuse legitimate workflows in unintended ways, causing business harm.

**Examples:**
- Reserving unlimited tickets without payment.
- Repeated coupon redemption.

**Real-World Case:**  
- **Ticketmaster bots** exploited purchase APIs to scalp concert tickets in bulk, disrupting fair access.  

**Mitigations:**
- Apply rate limits and business logic restrictions.
- Monitor for suspicious patterns.
- Implement anti-automation controls (e.g., CAPTCHA).

---

### API7:2023 – Server-Side Request Forgery (SSRF)

**Risk:**  
APIs fetching remote resources can be tricked into accessing internal services.

**Example:**  

```
POST /api/v1/fetch
{
"url": "http://169.254.169.254/latest/meta-data
"
}
```

This could expose cloud instance metadata.

**Real-World Case:**  
- **Capital One 2019 breach**: attacker exploited an SSRF in AWS WAF to access sensitive AWS metadata and steal 100M+ records.  

**Mitigations:**
- Enforce allowlists for outbound requests.
- Block access to private/internal IP ranges.
- Apply network-level egress filtering.
- Sanitize and validate all URLs.

---

### API8:2023 – Security Misconfiguration

**Risk:**  
Improper configurations leak sensitive information or weaken defenses.

**Examples:**
- Default admin credentials.
- Verbose error messages with stack traces.
- Overly permissive CORS (`Access-Control-Allow-Origin: *`).

**Real-World Case:**  
- Thousands of **Elasticsearch** and **MongoDB** databases have been breached due to misconfigured APIs exposed to the internet.  

**Mitigations:**
- Enforce secure configuration baselines.
- Disable verbose errors in production.
- Regularly patch and update frameworks.
- Perform configuration audits.

---

### API9:2023 – Improper Inventory Management

**Risk:**  
Forgotten or undocumented APIs increase exposure and attack surface.

**Examples:**
- `/api/v1/` endpoints still active after `/api/v2/` release.
- Debug or test endpoints left open.

**Real-World Case:**  
- In 2018, **T-Mobile** exposed customer data via an outdated API endpoint that was not properly decommissioned.  

**Mitigations:**
- Maintain a full API inventory (including versions).
- Use API gateways to manage exposure.
- Document and update API specifications.
- Retire deprecated endpoints securely.

---

### API10:2023 – Unsafe Consumption of APIs

**Risk:**  
Consuming untrusted external APIs without validation exposes systems to malicious data.

**Example:**  
A currency conversion API returns manipulated exchange rates, leading to financial loss.

**Real-World Case:**  
- In 2020, several **cryptocurrency exchanges** lost funds after relying on compromised third-party price feed APIs.  

**Mitigations:**
- Validate and sanitize all external responses.
- Enforce schema validation for third-party data.
- Apply zero-trust principles for integrations.
- Use API gateways or middleware filters.

---

## 📊 Summary Table

| Risk | Example | Real-World Case | Mitigation |
|------|---------|-----------------|------------|
| BOLA | Accessing `/users/67890` with another user’s token | Peloton 2021 data exposure | Enforce object-level access checks |
| Broken Auth | Predictable tokens | Facebook 2018 access token flaw | Strong auth (OAuth2, MFA, lockouts) |
| BOPLA | Updating `role=admin` via PATCH | Uber 2016 privilege escalation | Property-level access control |
| Resource Consumption | Unlimited file uploads | GitHub 2018 DDoS | Rate limiting, payload caps |
| Function Auth | Normal user calling admin endpoint | Snapchat 2014 admin exposure | RBAC/ABAC, deny-by-default |
| Sensitive Flows | Infinite seat reservations | Ticketmaster bot scalping | Business logic checks, monitoring |
| SSRF | Fetching `169.254.169.254` | Capital One 2019 | Allowlists, block internal IPs |
| Misconfig | Verbose error messages | MongoDB/Elasticsearch breaches | Harden configs, disable debug |
| Inventory Mgmt | Forgotten `/v1/` endpoints | T-Mobile 2018 API leak | Maintain API inventory, deprecate old APIs |
| Unsafe Consumption | Malicious external API | Crypto exchange price-feed hacks | Validate external responses |

---


