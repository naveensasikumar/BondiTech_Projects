# Victim Compensation Portal Suite — Architecture + CI/CD Overview

This repository contains high-level architecture and workflow diagrams for a multi-portal victim compensation system built for end-to-end claim intake, document handling, messaging, and internal case processing.

📄 **Diagrams (PDF):** `BondiTech_Project_Workflow.pdf`

---

## What this system is

A secure, multi-portal platform where external users submit and track claims, and internal caseworkers process them end-to-end:

### External Portals (Angular)
- **Victim Services Portal (VSP):** victims/claimants apply for reimbursement, upload documents, track statuses, and communicate with the program.
- **Advocate Portal:** advocates view/manage claims based on role-based access (agency/circuit/county) and generate court/restitution letters.
- **Provider Portal:** providers submit sexual assault/child assault/forensic interview related claims, upload bills, and track payment status.

### Internal Portal (CAWEB)
- **CAWEB (Claims Assistant Web):** internal caseworker portal for intake, eligibility checks, workflow processing, bill review, letter generation, reporting, and document management.

---

## Core capabilities

### Identity & Security
- Registration + login with **MFA**
- **SSO support** (CAWEB)
- Session management (auto logout on inactivity)

### Claims & Documents
- Online application with **auto-generated case number**
- Download application as **PDF**
- Upload and manage supporting documents
- Link claims submitted outside the portal (e.g., paper applications)

### Communication & Self-Service
- Message Center (send/receive messages and upload docs)
- FAQ + profile management
- Language settings (100+)
- Important banners/announcements
- Chatbot for common questions and status lookups

---

## High-level architecture (diagram reference)

The system is designed in layers:

### 1) Presentation Layer
- Angular frontends for VSP / Advocate / Provider (external) and CAWEB (internal)

### 2) API Layer
- **.NET REST APIs** for core business workflows, case processing, and background jobs
- **FastAPI microservices** for specialized services (and in some flows, FastAPI calls the .NET APIs)

### 3) Services Layer
- Notification engine
- Task engine
- Email services
- Background jobs

### 4) Data & Storage Layer
- **SQL Server** for system-of-record data
- **Redis** for sessions/caching
- **MeiliSearch** for fast filtering/search over large datasets
- **Amazon S3** for document storage (uploads, generated PDFs, exports)

📄 See: `BondiTech_Project_Workflow.pdf`

---

## Portals → CAWEB data flow (diagram reference)

External portals submit and update claim information (victim/claimant details, crime info, insurance, expenses, attachments, messages).  
CAWEB receives the consolidated case data for internal processing, including:
- Eligibility checklist and status progression (with status updates back to external users)
- Restitution/subrogation details
- Award calculations
- Letter generation per department workflow
- Bill review and EOB generation for finance/payment processing
- Case activity tree view + auditability

📄 See: `BondiTech_Project_Workflow.pdf`

---

## CI/CD Deployment (diagram reference)

Deployment is containerized and automated:

1. Code is stored in **GitHub**
2. **GitHub Actions** runs `build/deploy.yml`
3. Pipeline builds **Docker images** (tagged by commit/environment)
4. Images are pushed to **AWS ECR**
5. **AWS EC2** instances pull the latest images and deploy (restart services + health checks)

Runtime traffic flow (high-level):
- Users → **ALB (HTTPS 80/443)** → Portal websites
- Portals → **.NET/FastAPI** → SQL Server / Redis / MeiliSearch / S3
- Logs/metrics → Cloud monitoring (if enabled)

📄 See: `BondiTech_Project_Workflow.pdf`

---

## Notes
This repo is intentionally focused on **architecture and workflows** to support portfolio visibility. Implementation code may live in separate repositories depending on environment/security constraints.
