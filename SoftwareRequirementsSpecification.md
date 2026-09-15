# Software Requirements Specification (SRS)

**Project:** Blood Bank Management System (BBMS) — Web Application with Role-Based Access Control, Inventory Tracking, and Hospital Blood Request Management (Node.js / Express / SQLite / Tailwind CSS)  
**Authors:** Uttam (PES1UG24CS697), Abhinav K (PES1UG24CS701), Akshay Arcot (PES1UG24CS705), Navaneeth Tanuboddi (PES1UG24CS709) — Team T9  


---


## Table of Contents

1. [Introduction](#1-introduction)
2. [Overall description](#2-overall-description)
3. [External interface requirements](#3-external-interface-requirements)
4. [System features (Overview)](#4-system-features-overview)

---

# 1. Introduction

## 1.1 Purpose
This document is a Software Requirements Specification (SRS) for the Blood Bank Management System (BBMS), an enterprise-grade, role-based full-stack web application developed using Node.js, Express, SQLite, and Tailwind CSS. It defines the functional and non-functional requirements, external interfaces, security objectives and controls, system architectural models, and the verification criteria against which the delivered system will be assessed. The intended readers are the developer team (Team T9), reviewing faculty instructor, course coordinator, and laboratory evaluators.

## 1.2 Scope
The Blood Bank Management System encompasses the end-to-end operational lifecycle of voluntary blood donation, blood unit testing and processing, inventory management, hospital blood requisition, and emergency dispatch. The system provides role-based web portals for Donors, Hospital Representatives, Blood Bank Staff, and System Administrators.

Key in-scope subsystems include: donor pre-screening health questionnaires, appointment scheduling, digital donor cards, blood unit collection tracking with unique Bag IDs, mandatory serological infection screening (HIV, Hepatitis B/C, Syphilis, Malaria), component separation (PRBC, Platelets, Fresh Frozen Plasma), First-Expired-First-Out (FEFO) stock monitoring, hospital requisition submission with clinical urgency prioritization, automated ABO/Rh cross-matching verification, cold-chain dispatch handover manifests, immutable audit logging, and a public availability dashboard.

Explicitly out of scope: Direct hardware automation of centrifuges/refrigeration IoT sensors, online monetary payment gateways (blood is donated voluntarily without commercial sale), and direct clinical transfusion administration inside operating theaters.

## 1.3 Audience
Developer Team (Team T9), Software Quality Assurance / Test Engineers, Course Instructors, Academic Evaluators, and Hospital Blood Transfusion Committees.

## 1.4 Definitions and Acronyms

| Term | Meaning |
| :--- | :--- |
| **BBMS** | Blood Bank Management System — integrated web platform for blood lifecycle tracking. |
| **RBAC** | Role-Based Access Control — security architecture restricting access based on user role. |
| **PRBC** | Packed Red Blood Cells — red cell component used to treat acute hemorrhage and severe anemia. |
| **FFP** | Fresh Frozen Plasma — plasma frozen within 8 hours, containing labile coagulation factors. |
| **Platelet Concentrate** | Thrombocytes harvested to prevent bleeding in thrombocytopenic patients (5-day shelf life). |
| **Serology Screening** | Laboratory diagnostic assays for transfusion-transmissible infections (TTIs). |
| **Quarantine** | Holding status isolating blood units pending test results or discarding non-conforming units. |
| **Cross-Matching** | Compatibility testing between donor erythrocytes and recipient serum prior to issue. |
| **FEFO** | First-Expired-First-Out — inventory distribution strategy prioritizing closest expiration date. |
| **RTM** | Requirements Traceability Matrix — mapping requirements to modules, test cases, and status. |

---

# 2. Overall description

## 2.1 Product perspective
The Blood Bank Management System operates as a centralized, self-contained client-server web application. The backend is powered by Node.js and Express RESTful services backed by an ACID-compliant SQLite relational database running in Write-Ahead Logging (WAL) mode for high concurrent read throughput. The frontend utilizes responsive semantic HTML5, vanilla modern JavaScript (ES6+), and Tailwind CSS for mobile-friendly UI rendering. The application is completely containerizable and can run locally or behind reverse proxies (Nginx, Cloudflare Tunnels) for secure HTTPS access.

## 2.2 Major product functions
- Donor registration, medical eligibility self-screening, and digital donor card generation.
- Blood donation appointment booking and blood drive camp scheduling.
- Blood collection logging with unique barcode-compatible Bag IDs and donor linkage.
- Laboratory serology testing entry with automated quarantine enforcement for reactive units.
- Blood component separation tracking (PRBC, Platelets, FFP) with distinct temperature and shelf lives.
- Real-time blood stock inventory management categorized by 8 blood groups and component types.
- First-Expired-First-Out (FEFO) inventory allocation and proactive expiration threshold alerts.
- Hospital blood requisition workflow supporting Emergency/STAT and routine priority queues.
- Automated ABO/Rh compatibility cross-matching engine.
- Cryptographically signed dispatch manifests and chain-of-custody delivery verification.
- Immutable system audit logging and regulatory compliance reporting.

## 2.3 User roles and characteristics
- **Public Donor** — Voluntary blood donor. Expects intuitive self-service portal, eligibility guidance, simple appointment booking, and instant access to digital donor cards.
- **Hospital Representative** — Authorized hospital physician or blood bank coordinator. Submits urgent blood requisitions, tracks fulfillment status, and confirms custody transfer.
- **Blood Bank Staff / Lab Technician** — Laboratory professional logging blood collections, recording infectious disease screening assays, separating components, and managing stock allocations.
- **System Administrator** — IT/Operations lead managing user roles, configuring blood bank parameters, inspecting audit trails, and generating regulatory compliance reports.
- **Course Evaluator / Inspector** — Academic evaluator reviewing source code modularity, test coverage, relational schemas, and adherence to Software Engineering standards.

## 2.4 Operating environment
- **Server:** Node.js 18+ runtime on macOS (Apple Silicon / Intel), Linux (Ubuntu 22.04 LTS), or Windows 10/11 Server.
- **Database:** SQLite 3.x embedded database with WAL mode enabled; zero external database daemon required.
- **Client:** Modern evergreen web browsers (Chrome 100+, Safari 15+, Firefox 100+, Edge 100+) on desktop, tablet, and mobile devices.
- **Network:** Standard HTTP/1.1 and HTTP/2 over TCP ports 3000 / 443 with TLS encryption.

## 2.5 Constraints and assumptions
- Standards compliance: Clean architectural separation between REST controllers, business service logic, and database access models.
- Relational integrity: Foreign keys strictly enforced across all database tables (donors, units, requests, logs).
- Safe dispensing: The system strictly blocks allocation of expired, un-tested, or serologically reactive blood units.
- Physical pre-condition: Clinical vitals (hemoglobin ≥ 12.5 g/dL, blood pressure, weight ≥ 45 kg) are verified by physical nursing staff at collection.
- Regulatory compliance: Complete audit logging of all inventory changes to fulfill National Blood Transfusion Council guidelines.

---

# 3. External interface requirements

## 3.1 User interfaces
The web interface is engineered with responsive, accessible Tailwind CSS components. Key screens include:
- **Public Landing & Availability Portal:** Real-time stock summary, donor educational guidelines, and blood camp schedules.
- **Donor Portal:** Clean health questionnaire, appointment selector with time-slot reservation, and printable digital donor card.
- **Staff Operations Dashboard:** Rapid unit logging with Bag ID scanner support, serology results checklist, and quarantine status toggles.
- **Hospital Order Portal:** Structured requisition form with blood group selectors, unit counts, priority indicators, and delivery tracking.
- **Admin Control Center:** Interactive stock analytics, user role administration, and immutable audit trail tables.

## 3.2 Hardware Interfaces
- **Client Workstation / Mobile Device:** Minimum 1024x768 resolution for staff dashboard; responsive down to 360px width for donor portal.
- **Barcode / QR Scanner:** Optional standard USB/Bluetooth HID keyboard emulation for rapid Bag ID and Donor Card scanning.
- **Host Server:** Standard computing hardware (minimum 1 vCPU, 1 GB RAM, 10 GB storage).

## 3.3 Software Interfaces
- **Operating System:** macOS, Linux (Debian/Ubuntu/CentOS), or Windows 10/11.
- **Runtime Environment:** Node.js (v18.x or v20.x LTS).
- **Database Engine:** SQLite3 embedded database engine.
- **Tunnel / Proxy:** Cloudflare Tunnel daemon / Nginx for reverse proxying and automated SSL/TLS termination.

## 3.4 Communications Interfaces
RESTful JSON APIs over HTTP/HTTPS. Stateful session management via HTTP-only, SameSite secure cookies and JWT tokens. Standard CORS policies configured to prevent unauthorized cross-origin requests.

---

# 4. System features (Overview)
Detailed functional specifications (BBMS-F-001 through BBMS-F-025), non-functional requirements (BBMS-NF-001 through BBMS-NF-005), security controls (BBMS-SR-001 through BBMS-SR-006), UML diagrams, and the Requirements Traceability Matrix are elaborated in the complete 9-page system deliverable.


---

# 5. Security

## 5.1 Security

The Blood Bank Management System processes sensitive medical information, confidential donor contact details, and critical hospital blood inventory. The system threat model addresses both external malicious actors (unauthorized web access, data tampering) and internal threats (unauthorized role escalation or accidental release of quarantined blood).

### 5.1.1 Security Objectives

| ID | Security Objective |
| :--- | :--- |
| **SO-1** | **Confidentiality of Donor Health Information:** Donor health screening questionnaires, serology results, and personal identifiable information (PII) shall be visible only to authorized medical staff and the donor themselves. |
| **SO-2** | **Integrity of Blood Inventory and Quarantine:** Blood unit safety statuses (Quarantined, Available, Expired) shall only be modifiable through authenticated, logged transactions. No expired or infected unit shall ever be issued. |
| **SO-3** | **Account Authentication & Credential Protection:** User passwords shall be hashed using salted bcrypt with a minimum of 10 rounds; plaintext passwords shall never appear in logs or responses. |
| **SO-4** | **Audit Non-Repudiation:** All inventory allocations, status transitions, and hospital dispatches shall generate immutable audit trail records. |

> **Stated limitation:** Server operating system-level physical security and file permissions are assumed to be managed by the system administrator. Database file tampering directly on disk bypasses application-level controls; SQLite file permissions must be restricted to the node process owner (`0600` on POSIX).

### 5.1.2 Security Requirements

| Req ID | Requirement | Type | Priority | Acceptance Criteria / Test Case Ref |
| :--- | :--- | :--- | :--- | :--- |
| **BBMS-SR-001** | All user passwords shall be stored only as salted bcrypt hashes with at least 10 salt rounds; plaintext passwords shall never be logged or echoed. | Security | High | Database inspection reveals only `$2b$` hashes; no plaintext password in server logs. Test: `TC-SEC-01` |
| **BBMS-SR-002** | All SQL queries shall utilize parameterized prepared statements; dynamic string concatenation in SQL queries shall not appear in the codebase. | Security | High | Code review and sqlmap injection scan confirm zero SQL injection vulnerabilities. Test: `TC-SEC-02` |
| **BBMS-SR-003** | Session authentication tokens and cookies shall use `HttpOnly`, `SameSite=Strict`, and `Secure` flags to prevent XSS session hijacking. | Security | High | Browser DevTools inspection verifies `HttpOnly` and `SameSite` cookie attributes. Test: `TC-SEC-03` |
| **BBMS-SR-004** | Role authorization middleware shall protect every non-public API endpoint, returning HTTP `403` for unauthorized privilege escalation attempts. | Security | High | Automated privilege escalation tests verify that donor accounts cannot access `/api/staff` or `/api/admin`. Test: `TC-SEC-04` |
| **BBMS-SR-005** | All user inputs shall be validated against strict schemas (whitelisted characters, length limits, email regex) before backend processing. | Security | High | Malformed payloads and cross-site scripting (XSS) probe strings are rejected with `400 Bad Request`. Test: `TC-SEC-05` |
| **BBMS-SR-006** | Authentication endpoints shall enforce rate limiting (max 10 requests per minute per IP) to mitigate automated brute-force attacks. | Security | Medium | Rapid successive login attempts receive HTTP `429 Too Many Requests` response. Test: `TC-SEC-06` |

---

# 6. Quality Attributes & Acceptance Tests

## Exit Criteria for Acceptance

- Every high-priority functional requirement (**BBMS-F-001 through BBMS-F-025**) is fully implemented and passes all automated verification test suites.
- No non-functional requirement fails. In particular, the automated **72-hour stress test** and response latency benchmarks (**< 250 ms p95**) are blocking.
- All six security requirements (**BBMS-SR-001 to BBMS-SR-006**) pass with zero critical or high vulnerabilities identified during static analysis and dynamic penetration testing.
- The Requirements Traceability Matrix (RTM) in Section 8 demonstrates **100% bidirectional coverage** with all test cases evaluated with status **A (Accepted)**.

## Acceptance Test Suites

| Test Suite | Coverage |
| :--- | :--- |
| **TC-AUTH** | Authentication and session lifecycle |
| **TC-DNR** | Donor profile, questionnaire, and appointment scheduling |
| **TC-INV** | Blood collection, serology testing, component separation, and FEFO stock logic |
| **TC-REQ** | Hospital requisitions, cross-matching, allocation, and dispatch |
| **TC-AUD** | Audit trail persistence and reporting |
| **TC-NF** | Performance, reliability, usability, and portability |
| **TC-SEC** | Password security, SQL injection, XSS, RBAC enforcement, and rate limiting |

## Verification Methods

- Automated end-to-end API integration tests using **Node.js Test Runner** and **Supertest**
- Unit tests verifying cross-matching logic
- Load testing via **ApacheBench**
- Accessibility audits using **Lighthouse**
- Manual walkthroughs of all role-based UI dashboards

---

# 7. System Models and Diagrams

## 7.1 Use-Case Diagram — Donor and Hospital Portal Workflows

Figure 7.1 illustrates the external-facing operational boundary of the Blood Bank Management System. The primary external actors are the **Voluntary Donor** and the **Hospital Representative**.

The **Donor** interacts with the public portal to:

- Register an account
- Complete the pre-donation medical eligibility questionnaire
- Schedule or reschedule donation appointments
- View past donation history
- Access their digital donor card

The appointment scheduling use case automatically includes **eligibility verification**.

The **Hospital Representative** authenticates via a verified institutional account to:

- Submit routine or emergency (**STAT**) blood requisitions
- Track real-time fulfillment status
- Acknowledge blood unit delivery upon arrival

### Figure 7.1 — Use-Case Diagram: Donor and Hospital Portal Workflows

![Figure 7.1 — Use-Case Diagram](./images/figure-7.1-use-case-donor-hospital.png)

---

## 7.2 Use-Case Diagram — Blood Bank Staff & Administrator Workflows

Figure 7.2 illustrates the internal operations and administrative boundary. The primary actors are the **Blood Bank Staff / Lab Technician** and the **System Administrator**.

The **Blood Bank Staff / Lab Technician** can:

- Record incoming physical blood collections with unique Bag IDs
- Log serological diagnostic assay results
- Flag and discard contaminated or reactive units via quarantine controls
- Separate whole blood into red cell, platelet, and plasma components
- Dispatch allocated blood to hospitals

The **System Administrator** can:

- Authenticate with elevated credentials
- Manage user accounts
- Assign role permissions
- Configure inventory threshold alerts
- Review immutable system audit trails
- Generate statutory blood supply reports

### Figure 7.2 — Use-Case Diagram: Blood Bank Staff & Administrator Workflows

![Figure 7.2 — Use-Case Diagram](./images/figure-7.2-use-case-staff-admin.png)

---

# 8. Requirements Traceability Matrix (RTM)

**Status Legend:**

- `N` — Not Run
- `P` — Pass
- `A` — Accepted / Approved

| Req ID | Requirement Short Name | Section Ref | Module | Test Case(s) | Status | Comments |
| :--- | :--- | :--- | :--- | :--- | :---: | :--- |
| **BBMS-F-001** | User authentication | 4.1 | Auth / Session | TC-AUTH-01 | A | bcrypt hashing & session tokens verified |
| **BBMS-F-002** | Role-Based Access Control | 4.1 | Auth / RBAC | TC-AUTH-02 | A | Donor/Hospital/Staff/Admin permissions enforced |
| **BBMS-F-003** | Failed login lockout | 4.1 | Auth / Security | TC-AUTH-03 | A | Lockout after 5 failed attempts verified |
| **BBMS-F-004** | Password reset flow | 4.1 | Auth / Recovery | TC-AUTH-04 | A | 15-min cryptographic token reset verified |
| **BBMS-F-005** | Session logout termination | 4.1 | Auth / Session | TC-AUTH-05 | A | Immediate cookie/token invalidation verified |
| **BBMS-F-006** | Donor profile capture | 4.2 | Donor Module | TC-DNR-01 | A | Unique donor record and blood group stored |
| **BBMS-F-007** | Medical eligibility screening | 4.2 | Donor Screening | TC-DNR-02 | A | Clinical eligibility questionnaire enforced |
| **BBMS-F-008** | 90-day donation interval | 4.2 | Donor Scheduling | TC-DNR-03 | A | Interval check prevents premature booking |
| **BBMS-F-009** | Digital donor card generation | 4.2 | Donor Portal | TC-DNR-04 | A | Digital card with QR code verified |
| **BBMS-F-010** | Donation history & cancel | 4.2 | Donor Portal | TC-DNR-05 | A | Donation history and appointment cancel verified |
| **BBMS-F-011** | Blood collection logging | 4.3 | Collection / Lab | TC-INV-01 | A | Unique alphanumeric Bag IDs generated |
| **BBMS-F-012** | Serology screening tests | 4.3 | Testing / Lab | TC-INV-02 | A | Mandatory 5-infection testing verified |
| **BBMS-F-013** | Quarantine reactive units | 4.3 | Lab / Quarantine | TC-INV-03 | A | Reactive units locked and prevented from issue |
| **BBMS-F-014** | Component separation | 4.3 | Inventory Engine | TC-INV-04 | A | PRBC, Platelets, and FFP shelf lives enforced |
| **BBMS-F-015** | FEFO stock management | 4.3 | Inventory Engine | TC-INV-05 | A | First-expired-first-out prioritization verified |
| **BBMS-F-016** | Hospital requisition submit | 4.4 | Hospital Portal | TC-REQ-01 | A | Routine and STAT blood requests processed |
| **BBMS-F-017** | ABO/Rh cross-matching | 4.4 | Matching Engine | TC-REQ-02 | A | Cross-match compatibility matrix enforced |
| **BBMS-F-018** | Request review & reserve | 4.4 | Staff / Inventory | TC-REQ-03 | A | Inventory reservation upon approval verified |
| **BBMS-F-019** | Dispatch manifest generate | 4.4 | Dispatch Module | TC-REQ-04 | A | Printable handover manifest generated |
| **BBMS-F-020** | Delivery confirmation | 4.4 | Dispatch / Custody | TC-REQ-05 | A | Hospital receipt confirmation updates ledger |
| **BBMS-F-021** | Immutable audit trail | 4.5 | Audit / Security | TC-AUD-01 | A | Append-only audit logs for all transactions |
| **BBMS-F-022** | Analytics dashboard | 4.5 | Admin / UI | TC-AUD-02 | A | Real-time charts and KPI metrics verified |
| **BBMS-F-023** | CSV / PDF report export | 4.5 | Reporting Module | TC-AUD-03 | A | Regulatory compliance reports exported |
| **BBMS-F-024** | Public availability portal | 4.5 | Public Portal | TC-AUD-04 | A | Live stock counts visible without login |
| **BBMS-F-025** | Database backup & integrity | 4.5 | Database / Storage | TC-AUD-05 | A | SQLite WAL integrity check passes |
| **BBMS-NF-001** | API response performance | 5.0 | API / Server | TC-NF-PERF-01 | A | p95 latency < 250 ms verified |
| **BBMS-NF-002** | System availability & uptime | 5.0 | Core Architecture | TC-NF-REL-01 | A | Zero crashes in continuous stress tests |
| **BBMS-NF-003** | UI responsiveness & WCAG | 5.0 | Frontend UI | TC-NF-UX-01 | A | Mobile responsive; WCAG AA compliant |
| **BBMS-NF-004** | Cross-platform portability | 5.0 | Platform Runtime | TC-NF-PORT-01 | A | Operates on macOS, Linux, and Windows |
| **BBMS-NF-005** | Modular architectural design | 5.0 | Codebase / Clean Arch | TC-NF-MNT-01 | A | Layered architecture; zero lint errors |
| **BBMS-SR-001** | Salted bcrypt password hash | 5.1 | Security / Auth | TC-SEC-01 | A | Zero plaintext passwords in storage/logs |
| **BBMS-SR-002** | Parameterized SQL queries | 5.1 | Security / DB | TC-SEC-02 | A | Prepared statements prevent SQL injection |
| **BBMS-SR-003** | HttpOnly & SameSite cookies | 5.1 | Security / Session | TC-SEC-03 | A | Cookie flags protect against XSS hijacking |
| **BBMS-SR-004** | RBAC route authorization | 5.1 | Security / Middleware | TC-SEC-04 | A | Strict route access checks prevent escalation |
| **BBMS-SR-005** | Input validation & sanitization | 5.1 | Security / Input | TC-SEC-05 | A | Strict schema validation on all endpoints |
| **BBMS-SR-006** | Rate limiting & brute force defense | 5.1 | Security / Network | TC-SEC-06 | A | HTTP 429 triggered on rapid attempts |
