# Software Requirements Specification (SRS) — Phase 1 (First 4 Pages)

**Project:** Blood Bank Management System (BBMS) — Web Application with Role-Based Access Control, Inventory Tracking, and Hospital Blood Request Management (Node.js / Express / SQLite / Tailwind CSS)  
**Version:** 1.0  
**Authors:** Uttam (PES1UG24CS697), Abhinav K (PES1UG24CS701), Akshay Arcot (PES1UG24CS705), Navaneeth Tanuboddi (PES1UG24CS709) — Team T9  
**Date:** 14-09-2026  
**Status:** Draft for review  

---

## Direct Downloads
- 📄 **[Download Word Document (4 Pages)](./SRS_Blood_Bank_Management_System.docx)**
- 📕 **[Download PDF Report (4 Pages)](./SRS_Blood_Bank_Management_System.pdf)**

---

## Revision history

| Version | Date | Author | Change summary | Approval |
| :--- | :--- | :--- | :--- | :--- |
| 0.1 | 09-09-2026 | Team T9 | Initial draft: project scope, user roles, core entities | Approved |
| 0.5 | 11-09-2026 | Team T9 | Added functional requirements (FRs), inventory rules, and API specifications | Approved |
| 1.0 | 14-09-2026 | Team T9 | Complete SRS with security requirements, UML use-case diagrams, test cases, and RTM | Pending |

## Approvals

| Role | Name | Signature / Email | Date |
| :--- | :--- | :--- | :--- |
| Course Coordinator | Prof. Dept of CSE | coordinator.cse@pes.edu | 14-09-2026 |
| Course Instructor | Faculty Advisor | instructor.cse@pes.edu | 14-09-2026 |
| Team Lead | Navaneeth Tanuboddi | navaneeth.tanuboddi@gmail.com | 14-09-2026 |

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

