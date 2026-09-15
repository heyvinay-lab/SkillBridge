# SkillBridge — System Architecture

This document moves from “what SkillBridge should do” to:

"How are we going to build it?"

This file defines the agreed technical structure so the team can start implementing with a common vision.

---

## Table of contents

1. [High-Level Architecture](#high-level-architecture)
2. [Frontend](#frontend)
3. [Backend](#backend)
4. [Database](#database)
5. [Authentication](#authentication)
6. [GitHub Integration](#github-integration)
7. [Evidence Engine Architecture](#evidence-engine-architecture)
8. [AI Layer](#ai-layer)
9. [AI Architecture](#ai-architecture)
10. [Job Matching Architecture](#job-matching-architecture)
11. [Resume Architecture](#resume-architecture)
12. [Employer Architecture](#employer-architecture)
13. [Complete Architecture](#complete-architecture)
14. [Recommended Technology Stack](#recommended-technology-stack)
15. [Very Important: Separate the layers](#very-important-separate-the-layers)

---

## 1. High-Level Architecture

Recommended high-level components and flow:

```
                  USER
                   │
                   ▼
            ┌─────────────┐
            │  Frontend   │
            │ React/Next  │
            └──────┬──────┘
                   │
                HTTPS/API
                   │
                   ▼
            ┌─────────────┐
            │ Spring Boot │
            │   Backend   │
            └──────┬──────┘
                   │
     ┌─────────────┼──────────────┐
     │             │              │
     ▼             ▼              ▼
PostgreSQL       AI Layer     External APIs
     │             │              │
     │             │              ├── GitHub
     │             │              └── Future APIs
     │             │
     ▼             ▼
  pgvector       Ollama / AI Provider
```

This is the basic architecture: frontend → backend → persistence/AI/external APIs.

---

## 2. Frontend

**Technology**
- Next.js / React
- Tailwind CSS

The frontend is the user-facing surface. It should not access the database directly — all access goes through the backend API.

Example surface (Student Dashboard):
- Profile
- Projects
- Skills
- GitHub
- Evidence
- Resumes
- Jobs
- Applications

Flow:

Frontend
  ↓
Backend API
  ↓
Database

---

## 3. Backend

**Technology**
- Java
- Spring Boot

The backend is the main business layer and enforces authorization, validation, and business rules.

Example request flow:

Frontend
  │
  │ POST /api/projects
  ▼
Spring Boot
  ├── Firebase Authentication
  ├── Validation
  ├── Business Logic
  ├── Evidence
  ├── Resume
  ├── Matching
  └── Applications
  ▼
PostgreSQL

The backend decides what is allowed and performs server-side checks before changing state.

---

## 4. Database

**Primary choice**: PostgreSQL

Optionally use pgvector for semantic/vector search later.

Core entities (high level):
- User
- Profile
- Education
- Experience
- Skill
- Project
- Evidence
- Resume
- ResumeVersion
- Company
- Employer
- Job
- JobSkill
- Application
- Interview
- ExternalAccount

Detailed relationships will be designed in a separate database document.

---

## 5. Authentication

Do not build password authentication from scratch for the MVP unless there's a very strong reason.

Suggested flow for MVP:

Frontend
  ↓
Firebase Auth
  ↓
Authenticated User
  ↓
Spring Boot
  ↓
PostgreSQL

The backend must still verify the authenticated user's identity and permissions before allowing protected operations.

---

## 6. GitHub Integration

GitHub integration must go through the backend. Do not call the GitHub API directly from the browser.

Incorrect:

Browser
 ↓
GitHub API

Preferred:

Browser
 ↓
Spring Boot
 ↓
GitHub API
 ↓
Spring Boot
 ↓
Database

Why use the backend as a mediator?
- OAuth / token handling
- Permissions
- Data normalization
- Rate limit handling
- Synchronization and retries
- Error handling

---

## 7. Evidence Engine Architecture

This component extracts and evaluates evidence from multiple sources about a candidate.

```
             Candidate Profile
                    │
                    ▼
             Evidence Engine
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    GitHub       Projects     Certificates
       │            │            │
       └────────────┼────────────┘
                    ▼
              Evidence Data
                    │
                    ▼
              Evidence Rules
                    │
                    ▼
             Evidence Status
```

AI can help interpret unstructured information, but AI should not be the sole decision-maker. Mix deterministic rules + AI interpretation + user confirmation.

Example flow (GitHub repo → evidence):
- Detect Java files
- Detect Spring Boot dependency
- Mark as relevant project
- Generate evidence candidate
- Student reviews
- Student accepts

---

## 8. AI Layer

AI should be used for augmenting capabilities, not as the core control plane. Examples:
- Parse job descriptions → extract skills/requirements
- Analyze resumes → claim analysis
- Candidate + Job → semantic matching and explanation

Traditional code should continue to manage authentication, permissions, storage, and workflow state.

---

## 9. AI Architecture

Abstract an AI service interface so implementations can change without touching business logic.

Example interface (Java-like):

```java
interface AIService {
    AnalysisResult analyzeJob(String jobDescription);
}

class OllamaAIService implements AIService { ... }
class OpenRouterAIService implements AIService { ... }
```

This allows switching providers or adding fallbacks without redesigning the application.

---

## 10. Job Matching Architecture

Simplified model:

Candidate
  ├─ Skills
  ├─ Experience
  ├─ Projects
  ├─ Education
  └─ Evidence
      ↓
Matching Engine ← Job (required/preferred skills, experience, education)

Result should include:
- Strengths
- Gaps
- Evidence
- Explanation

A numerical score can be added later.

---

## 11. Resume Architecture

The resume is a presentation layer derived from a Master Profile.

Master Profile
  ├─ Resume A
  ├─ Resume B
  └─ Resume C

When the Master Profile changes (e.g., Experience updated), resumes can be rebuilt from the canonical data.

---

## 12. Employer Architecture

Employer side model:

Employer → Company → Job → Applications → Candidates → ATS Pipeline

The meeting point between candidate and employer is the Job + Application.

---

## 13. Complete Architecture

High-level end-to-end view:

```
                         SKILLBRIDGE
                              │
                 ┌────────────┴────────────┐
                 │                         │
             STUDENT                   EMPLOYER
                 │                         │
                 ▼                         ▼
          Master Profile              Company Profile
                 │                         │
        ┌────────┼────────┐                │
        ▼        ▼        ▼                ▼
      GitHub   Projects  Skills           Jobs
        │        │        │                │
        └────────┼────────┘                │
                 ▼                         │
           Evidence Engine                 │
                 │                         │
                 ▼                         │
              Resumes                      │
                 │                         │
                 └──────────┬──────────────┘
                            ▼
                       Job Matching
                            │
                            ▼
                    Apply with SkillBridge
                            │
                            ▼
                       Application
                            │
                            ▼
                         ATS
                            │
                            ▼
                         Hired
```

---

## 14. Recommended Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js / React |
| UI | Tailwind CSS |
| Backend | Java + Spring Boot |
| Database | PostgreSQL |
| Vector Search | pgvector |
| Authentication | Supabase Auth (or Firebase Auth for MVP) |
| File Storage | Supabase Storage |
| AI | Ollama (+ optional provider fallback) |
| External Integration | GitHub API |
| API Testing | Postman / Bruno |
| Version Control | Git + GitHub |
| Containerization | Docker |
| Frontend Hosting | Vercel |
| Backend Hosting | Render (or a free-tier alternative) |
| Database Hosting | Supabase |

---

## 15. Very Important: Separate the layers

Keep the backend modular — avoid a single giant file. Suggested package layout (Java):

```
backend/
└── src/
    └── main/
        └── java/
            └── com/skillbridge/
                ├── auth/
                ├── profile/
                ├── project/
                ├── skill/
                ├── evidence/
                ├── resume/
                ├── job/
                ├── matching/
                ├── application/
                ├── employer/
                └── common/
```

Inside a module (example - resume):

- ResumeController
- ResumeService
- ResumeRepository
- ResumeEntity
- ResumeDTO

You'll learn what these mean when we start backend development.

---

