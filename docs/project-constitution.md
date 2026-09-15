# SkillBridge — Project Constitution

**Version:** 1.0  
**Status:** Team Working Agreement  
**Project:** SkillBridge  
**Repository:** `heyvinay-lab/SkillBridge`  
**Primary Branch:** `main`

---

## Table of contents

1. [Purpose](#1-purpose)  
2. [Product Identity](#2-product-identity)  
   - [Product Name](#21-product-name)  
   - [Product Definition](#22-product-definition)  
   - [Core Product Idea](#23-core-product-idea)  
3. [Product Principles](#3-product-principles)  
4. [Project Scope](#4-project-scope)  
   - [MVP](#41-mvp)  
   - [Post-MVP Features](#5-post-mvp-features)  
   - [MVP Freeze Rule](#6-mvp-freeze-rule)  
5. [System & Authentication Architecture](#7-system--authentication-architecture)  
   - [System Architecture](#7-system-architecture)  
   - [Authentication Architecture](#8-authentication-architecture)  
6. [User Roles](#9-user-roles)  
7. [API Standards & Security](#10-api-standards--api-security-rules)  
   - [API Standards](#10-api-standards)  
   - [API Security Rules](#11-api-security-rules)  
8. [Database Rules & Design](#12-database-rules--database-design-principles)  
   - [Database Rules](#12-database-rules)  
   - [Database Design Principle](#13-database-design-principle)  
   - [Database Change Rule](#14-database-change-rule)  
9. [Core Entities & Evidence](#15-core-database-entities--evidence-rules)  
   - [Core Database Entities](#15-core-database-entities)  
   - [Evidence Rules](#16-evidence-rules)  
10. [AI Usage & Failure Handling](#17-ai-rules--ai-failure-rule)  
11. [External Integrations](#19-external-integration-rules)  
12. [Repository, Branching, Commits & PRs](#20-git-repository-rules--branch-naming--commit--pr-rules)  
13. [Development Process, Boards & Roles](#30-project-board--task-ownership--team-structure)  
14. [Testing, Security, Privacy, and History](#41-testing-philosophy--security-and-privacy)  
15. [Decision Making & Changing the Constitution](#50-decision-making-process--changing-the-constitution)  
16. [Documentation & Final Rules](#52-documentation-hierarchy--final-team-rule)  

---

## 1. Purpose

This document defines the rules, principles, technical standards, development workflow, and decision-making process that all SkillBridge team members must follow.

The purpose is to ensure five developers work as one engineering team rather than as five independent developers. This document is the team's working contract.

---

## 2. Product Identity

### 2.1 Product Name

**SkillBridge**

### 2.2 Product Definition

SkillBridge is an evidence-based career intelligence and talent matching platform connecting students/candidates and employers through:

- Professional profiles
- Skills
- Projects
- Evidence
- Dynamic resumes
- Job intelligence
- Job matching
- Applications
- Employer candidate discovery
- Hiring workflow

### 2.3 Core Product Idea

Build Skills  
↓  
Prove Skills  
↓  
Present Skills  
↓  
Find Relevant Jobs  
↓  
Apply  
↓  
Interview  
↓  
Improve  
↓  
Get Hired

The resume is a presentation/output layer, not the master source of candidate information.

---

## 3. Product Principles

3.1 Evidence Before Claims
- Distinguish between self-reported information, supporting evidence, verified evidence, and unverified claims.
- The system must never fabricate evidence.

3.2 Truthful Career Representation
- The system may improve presentation but must not invent:
  - Skills, Experience, Projects, Achievements, Metrics, Responsibilities, Certifications

3.3 Explainability
- Important intelligent outputs should provide understandable reasons (e.g., "Strong Match", "Partial Match", "Missing Skill", "Supporting Evidence").

3.4 User Control
- Candidates retain control over professional information and visibility.
- Private information must never become public simply because it exists in the database.

3.5 Security by Default
- Authentication, authorization, validation, and ownership checks belong to the backend.

---

## 4. Project Scope

### 4.1 MVP

The MVP contains:

Authentication
- Firebase Authentication
- User initialization
- One role per account: Student, Employer, Admin

Student features:
- Professional profile, education, experience, projects, skills, certifications, achievements
- External account connections (GitHub integration)
- Evidence management
- Resume management and versions
- Resume Truth Checker
- Job analysis & matching
- Apply with SkillBridge, application tracking

Employer features:
- Company profile & membership
- Job creation, publishing & management
- Candidate search & profile viewing
- Application review, shortlisting, hiring pipeline, notes
- Interview management

Platform features:
- Role-based authorization
- Validation, error handling, testing, deployment, documentation

### 5. Post-MVP Features

Not part of the initial MVP unless explicitly added via the change process:
- College dashboards & analytics
- Rankings, hackathons, career events, scholarships
- Internship marketplace, sponsored skill programs
- Advanced interview simulation, project generation, recommendation systems, analytics

These belong to the future roadmap.

### 6. MVP Freeze Rule

When the MVP is frozen:
1. New idea → create GitHub Issue
2. Evaluate → potentially add to future roadmap

A new feature must not enter the active sprint simply because someone has an idea—this prevents scope creep.

---

## 7. System & Authentication Architecture

### 7.1 System Architecture

High-level architecture:

USERS  
↓  
Next.js / React (frontend)  
↓  
Spring Boot API (backend)  
├─ PostgreSQL (with pgvector where required)  
├─ AI Layer (Ollama / AI provider)  
└─ External APIs (e.g., GitHub)

### 8. Authentication Architecture

Authentication flow:

User  
↓  
Firebase Authentication  
↓  
Firebase ID Token  
↓  
Authorization: Bearer <token>  
↓  
Spring Boot verifies Firebase Token  
↓  
Extract Firebase UID → Find SkillBridge User → Check Role → Authorize Request

- Firebase is responsible for identity/authentication.
- PostgreSQL stores application data.

---

## 9. User Roles

- Each account has exactly one role: STUDENT, EMPLOYER, or ADMIN.
- The role must be determined by backend/database state. The frontend must never be trusted to determine permissions.

---

## 10. API Standards & API Security Rules

### 10.1 API Standards

- Style: REST
- Base path: `/api/v1`
- Example: `GET /api/v1/profile/me`

Authentication:
- Protected APIs receive `Authorization: Bearer <Firebase ID Token>`

Response format:

Success:
```json
{
  "success": true,
  "data": {},
  "message": "Operation completed successfully"
}
```

Error:
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message"
  }
}
```
- `error.code` should be stable and suitable for frontend logic.

### 11. API Security Rules

The backend must not trust these client-supplied values:
- User ID, Firebase UID, Role, Resource ownership, Company ownership, Evidence verification status

The backend must derive them from:
- Authenticated identity + database state + business rules

---

## 12. Database Rules

- DB: PostgreSQL
- Primary keys: UUID
- Firebase UID: external identity, unique, not the PK
- Timestamps: TIMESTAMPTZ
- Important historical business records should use soft deletes/status rather than physical deletion (e.g., jobs with applications, resume versions).

## 13. Database Design Principle

- Authoritative candidate data belongs in structured domain tables:
  - Profile → Education, Experience, Projects, Skills, Certifications, Achievements, Evidence
- Resumes are generated from structured data. A resume must not become the authoritative source.

## 14. Database Change Rule

Process for schema changes:
1. Requirement
2. Check existing schema
3. Discuss proposed change
4. Update database design
5. Create migration
6. Test migration
7. PR → Review → Merge

- Migrations should be version controlled. Recommended tool: Flyway.

---

## 15. Core Database Entities

Current core model includes (non-exhaustive):
- users, roles, profiles, educations, experiences, projects, skills, profile_skills, project_skills, certifications, achievements, evidence, external_accounts, github_repositories, project_repositories, resumes, resume_versions, companies, company_memberships, jobs, job_skills, job_questions, applications, application_answers, application_status_history, application_notes, interviews, candidate_preferences

The database design document is authoritative for fields and relationships.

---

## 16. Evidence Rules

- Evidence sources: GitHub, projects, certifications, experience, coding profiles, documents, self-reported info, assessments.
- Possible evidence states:
  - SELF_REPORTED, SUPPORTED, VERIFIED, UNVERIFIED, REJECTED
- Do not mark evidence VERIFIED solely because an AI model believes it is genuine—verification requires a defined mechanism.

---

## 17. AI Rules

- AI is assistive, not authoritative.
- Permitted uses:
  - Job description analysis, skill extraction, resume analysis, claim analysis, evidence interpretation, semantic matching, interview question generation, feedback
- Application logic controls:
  - Authentication, Authorization, Ownership, DB integrity, Resume versioning, Hiring pipeline, Validation, Security

## 18. AI Failure Rule

If AI provider is unavailable:
- Controlled error → user informed → user data remains safe
- Core CRUD should not depend on AI availability.

---

## 19. External Integration Rules

- Implement external integrations only after verifying provider API/access rules.
- Initial priority: GitHub
- Do not assume an API exists simply because a website exists.

---

## 20. Git Repository Rules

- Repository: `heyvinay-lab/SkillBridge`
- Primary branch: `main` — should represent stable, integrated code.
- Direct pushes to `main` should be disabled for normal development.

### Branch naming examples

Features:
- `feature/student-profile`
- `feature/github-integration`
- `feature/evidence-engine`
- `feature/resume-builder`
- `feature/job-matching`

Bug fixes:
- `fix/login-error`
- `fix/github-sync`
- `fix/application-duplicate`

Documentation:
- `docs/api-specification`
- `docs/database-design`

### Commit rules

- Commits should describe one logical change, use imperative wording:
  - Good: `Add student profile API`
  - Avoid: `update`, `final`, `latest`

### Pull Request rules

Workflow:
Issue → Branch → Implementation → Testing → Commit → Push → PR → Review → Approval → Merge

PR should state:
- What changed, why, how tested, related issue (example format provided in original document).

Code review:
- At least one other developer should review meaningful PRs; focus on correctness, security, authorization, validation, DB integrity, tests, performance, maintainability.

Sensitive changes (extra review):
- Firebase auth, DB migrations, personal-data access, file access, GitHub OAuth, external credentials, application permissions.

---

## 21. Issue Rules & Definitions

- Work should be represented as GitHub Issues and include: Title, Goal, Requirements, Acceptance Criteria, Dependencies, Owner, Priority.
- Definition of Ready: requirement clear, expected behavior defined, dependencies known, acceptance criteria exist, owner assigned, relevant API/DB design known.
- Definition of Done:
  - Requirement implemented, validation & auth checked, tests completed, PR reviewed & merged, integration checked, documentation updated.

---

## 22. Project Board & Task Ownership

Project Board columns:
- BACKLOG → TODO → IN PROGRESS → IN REVIEW → TESTING → DONE

Task ownership:
- Every active task has one primary owner (responsible for driving to completion, not necessarily doing all work).

---

## 23. Team Structure & Communication

Recommended five-member ownership:
- Member 1: Frontend + Resume UI
- Member 2: Backend + Firebase security
- Member 3: Database + GitHub integration
- Member 4: AI + Evidence + Matching
- Member 5: Employer + ATS + Testing/DevOps

No knowledge silos — critical modules should be understood by more than one member (Authentication, Database, GitHub integration, Evidence Engine, Application workflow, Deployment).

Communication:
- Use GitHub for permanent technical info (Issues, PRs, docs).
- Use team chat for quick coordination; record important chat decisions in GitHub/docs.

Standups & sprints:
- Daily standup: Yesterday/Today/Blocked
- Weekly sprint planning: 1-week sprints by default, review backlog → select tasks → assign owners → identify dependencies → start sprint

---

## 24. Dependency Management & API Contract Changes

- Track dependencies explicitly and use agreed API contracts/mock data when possible.
- API contract changes must be communicated prior to implementation (discuss, update API spec, update clients, implement, test).

---

## 25. Environment, Secrets & Local Development

- Never commit secrets to GitHub (credentials, API keys, private configs).
- Use environment variables & repository `.env.example` only as examples.
- Local development requirement: every developer must be able to clone, configure, run frontend/backend, connect to dev DB, and run tests.

---

## 26. Testing Philosophy

Testing levels:
- Unit → Integration → API → End-to-end
- Critical user journeys should be covered end-to-end eventually (e.g., student login → profile → resume → job → application → employer shortlist).

---

## 27. Bug Management, Security & Privacy

Bug workflow:
- Bug found → Reproduce → Issue → Assign → Fix branch → Test → PR → Review → Merge

Security priority:
- Issues involving cross-user access, auth bypass, private data exposure, credential leakage, DB corruption have highest priority.

Privacy rules:
- Candidate info exposed per authentication + authorization + visibility settings + business rules.
- Employer notes must never be exposed through student-facing APIs.

---

## 28. Resource Ownership, History & Resume Rules

- For user-owned resources, backend must derive ownership from authenticated Firebase UID → SkillBridge user → profile/resource ownership → permission check.
- Avoid destructive deletion of important records (use status flags).
- When submitting resume versions with applications, submitted versions must remain identifiable for historical integrity.

Application & resume history:
- Maintain application status history (APPLIED → SCREENING → SHORTLISTED → INTERVIEW → SELECTED).

---

## 29. External Service Failure Handling

- Treat external APIs as unreliable: on failure, surface a controlled error & useful user message; existing data remains safe and the app must not crash.

---

## 30. Decision-Making Process & Changing the Constitution

Decision process:
1. Requirement → Options → Trade-offs → Decision → Documentation
- Team lead coordinates decisions with technical reasoning.

Changing this constitution:
1. Proposal → Team discussion → Impact assessment → Agreement → Update document → Commit/PR
- Significant changes must follow this process.

---

## 31. Documentation Hierarchy & Conflict Resolution

Documentation order (canonical):
1. 01-requirements.md
2. 02-user-flows.md
3. 03-system-architecture.md
4. 04-database-design.md
5. 05-api-specification.md
6. 06-development-workflow.md
7. 07-roadmap.md
8. 08-project-constitution.md

If documents conflict:
- STOP implementation → Identify conflict → Determine intended behavior → Update documents → Continue implementation

---

## 32. Roles & Responsibilities

Team lead responsibilities:
- Maintain scope, architecture consistency, task ownership, progress, PR quality, documentation, integration, risk management.

Developer responsibilities:
- Understand requirements, write maintainable code, test changes, follow Git workflow, create clear PRs, review teammates' work, report blockers, protect secrets, document decisions.

---

## 33. Final Team Rule & Status

- When uncertain about database structure, API contracts, auth, privacy, product scope, or data integrity, ask rather than assume.
- This constitution becomes effective when the team agrees. After approval: Status → ACTIVE, Version → 1.0. Future major changes must be versioned.

---

## 34. Current Project Documentation

docs/
```
01-requirements.md
02-user-flows.md
03-system-architecture.md
04-database-design.md
05-api-specification.md
06-development-workflow.md
07-roadmap.md
08-project-constitution.md
```

---

(End of document)
