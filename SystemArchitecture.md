SkillBridge System Architecture

Now we move from “what SkillBridge should do” to:

“How are we going to build it?”

This document is extremely important because your team needs one agreed technical structure before everyone starts coding.

1. High-Level Architecture

For SkillBridge, I recommend:

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
    pgvector       Ollama /
                   AI Provider

This is the basic architecture.

2. Frontend
Technology
Next.js / React
Tailwind CSS

The frontend is what the user sees.

For example:

Student Dashboard
        │
        ├── Profile
        ├── Projects
        ├── Skills
        ├── GitHub
        ├── Evidence
        ├── Resumes
        ├── Jobs
        └── Applications

The frontend should not directly access the database.

Instead:

Frontend
    ↓
Backend API
    ↓
Database
3. Backend
Technology
Java
Spring Boot

The backend is the main business layer.

For example:

Frontend
    │
    │ POST /api/projects
    ▼
Spring Boot
    │
    ├── Firebase Authentication
    ├── Validation
    ├── Business Logic
    ├── Evidence
    ├── Resume
    ├── Matching
    └── Applications
    │
    ▼
PostgreSQL

The backend should decide what is allowed to happen.

4. Database

Use:

PostgreSQL

Potentially with:

pgvector

for semantic/vector search later.

Core entities:

User
Profile
Education
Experience
Skill
Project
Evidence
Resume
ResumeVersion
Company
Employer
Job
JobSkill
Application
Interview
ExternalAccount

Relationships will be designed separately in the database document.

5. Authentication

Don't build password authentication from scratch unless there is a strong reason.

For the MVP:

Frontend
    ↓
Firebase Auth
    ↓
Authenticated User
    ↓
Spring Boot
    ↓
PostgreSQL

The backend should still verify the authenticated user's identity before allowing protected operations.

6. GitHub Integration

GitHub should communicate through the backend.

Don't do this:

Browser
   ↓
GitHub API

Prefer:

Browser
   ↓
Spring Boot
   ↓
GitHub API
   ↓
Spring Boot
   ↓
Database

Why?

Because you need to control:

OAuth/token handling
permissions
data normalization
rate limits
synchronization
error handling
7. Evidence Engine Architecture

This is where SkillBridge becomes interesting.

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

AI can help interpret unstructured information.

But AI shouldn't independently decide everything.

For example:

GitHub repository
       ↓
Java files detected
       ↓
Spring Boot dependency detected
       ↓
Relevant project
       ↓
Evidence generated
       ↓
Student reviews
       ↓
Accepted

This gives you a combination of:

Deterministic evidence + AI interpretation + user confirmation

which is much safer than simply asking an LLM:

"Is this person good at Java?"

8. AI Layer

Don't make the entire application dependent on AI.

Use AI for tasks such as:

Job Description
      ↓
AI
      ↓
Skills / Requirements

and:

Resume
  ↓
AI
  ↓
Claim analysis

and:

Candidate + Job
       ↓
Semantic analysis
       ↓
Match explanation

But traditional code handles:

Authentication
Permissions
Database
Applications
Resume versions
Job status
ATS stages
9. AI Architecture

For development, you can use:

SkillBridge
     │
     ▼
AI Service Interface
     │
     ├── Ollama
     │
     └── External AI provider

This is important.

Don't write your entire application directly around one AI provider.

Instead:

interface AIService {
    AnalysisResult analyzeJob(String jobDescription);
}

Then your implementation can change later.

AIService
   │
   ├── OllamaAIService
   └── OpenRouterAIService

That gives you flexibility.

10. Job Matching Architecture

A simplified version:

Candidate
    │
    ├── Skills
    ├── Experience
    ├── Projects
    ├── Education
    └── Evidence
             │
             ▼
       Matching Engine
             ▲
             │
           Job
             │
             ├── Required Skills
             ├── Preferred Skills
             ├── Experience
             └── Education

The result should contain:

Match
├── Strengths
├── Gaps
├── Evidence
└── Explanation

Later you can add a numerical score.

11. Resume Architecture

The resume should not be the primary source of candidate information.

Instead:

                Master Profile
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
     Resume A     Resume B     Resume C
      Backend      SDE        Internship

This is a critical design decision.

If the student updates:

Experience

the Master Profile changes.

Then they can update/rebuild relevant resumes from the new data.

12. Employer Architecture

Employer side:

Employer
   │
   ▼
Company
   │
   ▼
Job
   │
   ▼
Applications
   │
   ▼
Candidates
   │
   ▼
ATS Pipeline

The candidate and employer systems meet at:

Job
  +
Application
13. Complete Architecture

Put everything together:

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
14. Recommended Technology Stack
Layer	Technology
Frontend	Next.js / React
UI	Tailwind CSS
Backend	Java + Spring Boot
Database	PostgreSQL
Vector Search	pgvector
Authentication	Supabase Auth
File Storage	Supabase Storage
AI	Ollama + optional provider fallback
External Integration	GitHub API
API Testing	Postman/Bruno
Version Control	Git + GitHub
Containerization	Docker
Frontend Hosting	Vercel
Backend Hosting	Render/free-tier alternative
Database	Supabase
15. Very Important: Separate the layers

Your backend shouldn't become one giant file.

Use something like:

backend/
└── src/
    └── main/
        └── java/
            └── com/skillbridge/
                │
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

Inside a module:

resume/
├── ResumeController
├── ResumeService
├── ResumeRepository
├── ResumeEntity
└── ResumeDTO

You'll learn what these mean when we start backend development.
