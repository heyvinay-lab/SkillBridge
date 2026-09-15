# SkillBridge — Environment & Setup Guide

**Version:** 1.0  
**Status:** Team Development Standard  
**Repository:** `heyvinay-lab/SkillBridge`

---

## 1. Purpose

This document defines the standard development environment for SkillBridge.

Every team member should be able to:

1. Clone the repository.
2. Configure the development environment.
3. Connect to the development services.
4. Run the frontend.
5. Run the backend.
6. Authenticate with Firebase.
7. Connect to Supabase PostgreSQL.
8. Use Supabase Storage.
9. Run tests.
10. Create a branch and begin development.

The goal is to avoid:

> "It works on my laptop."

---

# 2. Development Architecture

The development environment uses:

```text
Developer Computer
       │
       ├── Next.js / React
       │
       └── Spring Boot
              │
              ├── Firebase Authentication
              │
              └── Supabase
                    ├── PostgreSQL
                    └── Storage

External services:

GitHub API
AI service

are accessed through the backend where appropriate.

3. Required Software

Each developer should have:

Software	Purpose
Git	Version control
GitHub account	Repository collaboration
Node.js	Frontend runtime
npm	Frontend package management
JDK	Java development
Maven or Gradle	Backend build
IDE	Development
Docker	Optional/local infrastructure
Postman/Bruno	API testing
Modern browser	Frontend testing

The team must agree on the exact versions before development starts.

4. Version Consistency

The team should use the same major versions of:

Node.js
Java
Spring Boot
Next.js
Package manager

Version requirements must be documented in the repository.

Recommended files:

.nvmrc
.mvn/
gradle/

or the corresponding project configuration.

Do not let every developer use a different runtime version without a reason.

5. Recommended Repository Structure
SkillBridge/
│
├── frontend/
│
├── backend/
│
├── docs/
│
├── .gitignore
├── .env.example
└── README.md

The final structure may evolve, but developers should not arbitrarily create top-level folders.

6. Clone the Repository

The repository is:

https://github.com/heyvinay-lab/SkillBridge

Clone it:

git clone https://github.com/heyvinay-lab/SkillBridge.git

Then:

cd SkillBridge

Verify:

git status

Expected:

On branch main
7. Git Configuration

Configure Git once on each computer:

git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"

Verify:

git config --global --list

The email should correspond appropriately to the developer's GitHub identity.

8. Branch Before Development

Do not start feature development directly on main.

Start with:

git switch main
git pull

Then create a feature branch:

git switch -c feature/<feature-name>

Example:

git switch -c feature/student-profile
9. Frontend Setup

The SkillBridge frontend will use:

Next.js / React

Navigate to:

cd frontend

Install dependencies:

npm install

Start development server:

npm run dev

The development URL will normally be displayed by Next.js in the terminal.

Use the URL provided by the project rather than assuming a fixed port if the configuration changes.

10. Frontend Environment Variables

The frontend must use an environment file for development configuration.

Example:

frontend/.env.local

Never commit this file when it contains secrets or environment-specific credentials.

Use:

.env.example

as the team template.

Example structure:

NEXT_PUBLIC_FIREBASE_API_KEY=
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=
NEXT_PUBLIC_FIREBASE_PROJECT_ID=
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=
NEXT_PUBLIC_FIREBASE_APP_ID=
NEXT_PUBLIC_API_BASE_URL=

Only values intended to be exposed to the browser should use the appropriate public environment-variable mechanism.

11. Firebase Configuration

SkillBridge uses:

Firebase Authentication

The Firebase project must be created and configured by an authorized project administrator.

The team should configure the required authentication providers centrally.

Possible providers:

Email / Password
Google

Only providers actually enabled for SkillBridge should be documented as active.

12. Firebase Client Configuration

The frontend initializes Firebase using the project configuration.

The Firebase client configuration may be present in the frontend because Firebase client configuration is designed to identify the Firebase project.

However:

Firebase client configuration is not a replacement for backend authorization.

The backend must still verify Firebase ID tokens.

13. Firebase Admin Credentials

Backend Firebase Admin credentials are sensitive.

They must:

Never be committed to GitHub
Never be placed inside frontend code
Never be exposed in API responses
Never be placed in public environment variables

Use secure server-side configuration.

14. Backend Setup

The backend uses:

Java
Spring Boot

Navigate to:

cd backend

The team must standardize on either:

Maven

or:

Gradle

Do not maintain two build systems unless there is a specific requirement.

For Maven, typical commands are:

./mvnw clean install
./mvnw spring-boot:run

On Windows, the wrapper may be invoked as:

mvnw.cmd spring-boot:run

The repository's actual build instructions are authoritative once the backend project is created.

15. Backend Environment Variables

Backend configuration should use environment variables rather than hardcoded credentials.

Typical configuration includes:

DATABASE_URL=
DATABASE_USERNAME=
DATABASE_PASSWORD=

FIREBASE_PROJECT_ID=
FIREBASE_CLIENT_EMAIL=
FIREBASE_PRIVATE_KEY=

SUPABASE_URL=
SUPABASE_SERVICE_KEY=

GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=

AI_PROVIDER=
AI_API_KEY=

Only the variables actually required by the implementation should be added.

16. Secret Handling

Never commit:

.env
.env.local
Firebase private key files
service-account JSON files
GitHub client secrets
Supabase service keys
database passwords
AI API keys

The repository should contain:

.env.example

with placeholders only.

17. PostgreSQL

SkillBridge uses:

Supabase PostgreSQL

The database is accessed through the backend.

Architecture:

Next.js
   ↓
Spring Boot
   ↓
Supabase PostgreSQL

The frontend must not directly perform unrestricted application-table database operations.

18. Database Environment

Backend developers must configure the development database connection using environment variables.

Example conceptual configuration:

DATABASE_URL=<development database connection>
DATABASE_USERNAME=<development username>
DATABASE_PASSWORD=<development password>

The exact connection values must be supplied through the team's secure configuration process.

19. Development vs Production Database

Never develop against the production database unless there is a specific, controlled administrative reason.

Prefer:

Development
     ≠
Production

where infrastructure permits.

This protects real data from development mistakes.

20. Database Migrations

Database schema changes must use:

Flyway

Example:

backend/
└── db/migration/
    ├── V1__initial_schema.sql
    ├── V2__add_profile_tables.sql
    ├── V3__add_github_tables.sql
    └── ...

Developers must not manually modify shared database structures without a corresponding migration.

21. Running Migrations

When the backend starts, Flyway should apply pending migrations according to the project configuration.

The team must ensure:

Migration
   ↓
Test
   ↓
Commit
   ↓
Review
   ↓
Merge

Migrations should be deterministic and safe to run in the intended environment.

22. Supabase Storage

SkillBridge uses Supabase Storage for files.

Examples:

Profile photos
Resume PDFs
Certificates
Evidence documents

The database stores metadata.

The actual binary file belongs in Supabase Storage.

23. Storage Bucket Design

The team should create appropriate buckets for the application's file categories.

A possible logical design is:

skillbridge-private

with structured paths such as:

users/{userId}/profile/
users/{userId}/resumes/
users/{userId}/certificates/
users/{userId}/evidence/

The exact bucket/path convention must be finalized before implementation.

Do not create random file locations in individual features.

24. Storage Credentials

Privileged Supabase credentials must remain server-side.

The frontend must not receive credentials that grant unrestricted access to private storage or database resources.

The backend performs:

Authentication
       ↓
Ownership check
       ↓
Authorization
       ↓
File access
25. File Upload Development

A developer implementing uploads must follow:

User
 ↓
Firebase authentication
 ↓
Spring Boot
 ↓
Validate file
 ↓
Check ownership/purpose
 ↓
Supabase Storage
 ↓
Save metadata

Do not allow the frontend to bypass the backend's business authorization.

26. GitHub Integration

GitHub integration will be implemented through the backend where credentials, OAuth state, and sensitive integration logic are involved.

Conceptually:

Student
   ↓
Frontend
   ↓
Spring Boot
   ↓
GitHub OAuth/API
   ↓
Normalize data
   ↓
PostgreSQL
27. GitHub OAuth Configuration

Development requires the appropriate GitHub OAuth application configuration.

At minimum, document:

Client ID
Client secret
Callback URL
Requested scopes

The client secret must remain private.

The callback URL must match the configured GitHub OAuth application.

28. AI Configuration

The application may use:

Ollama

and, if later enabled:

external AI provider

The AI provider must be accessed through a controlled service abstraction.

Conceptually:

Spring Boot
    ↓
AI Service
    ├── Local/Ollama
    └── External provider

The application should not spread provider-specific API calls throughout the codebase.

29. Local AI

If Ollama is used locally:

SkillBridge
   ↓
Local AI service
   ↓
Ollama

Developers should use the model version specified by the project configuration.

Don't allow every developer to silently use a different model when model behavior affects application results.

30. API Testing

Use:

Postman

or:

Bruno

for API testing.

Examples:

POST /api/v1/profile
GET /api/v1/profile/me
POST /api/v1/projects
GET /api/v1/jobs
POST /api/v1/applications

The API contract is defined in:

docs/05-api-specification.md
31. Authentication Testing

Protected requests should include:

Authorization: Bearer <Firebase ID Token>

Testing should cover:

Valid token
Missing token
Expired/invalid token
Wrong role
Wrong resource owner
32. Frontend ↔ Backend Configuration

The frontend should reference the backend through a configurable API base URL.

Example:

NEXT_PUBLIC_API_BASE_URL=http://localhost:<backend-port>/api/v1

The actual port must come from the project's configuration.

Do not hardcode production URLs into components.

33. CORS Development Configuration

During development, Spring Boot should allow the configured frontend development origin.

Example conceptual setup:

Frontend:
http://localhost:<frontend-port>

Backend:
http://localhost:<backend-port>

The exact origin and ports must match the running project.

Production origins must be explicitly configured separately.

34. Local Services

A developer should know which services are local and which are hosted.

Local
Next.js
Spring Boot
Optional Ollama
Managed/cloud
Firebase Authentication
Supabase PostgreSQL
Supabase Storage
GitHub

This distinction should remain documented.

35. Optional Docker

Docker may be used to make local development more consistent.

However, the team should not introduce Docker complexity unless there is a clear benefit.

Possible future structure:

docker-compose.yml

for local supporting services where required.

The project should still document whether Docker is:

Required
Optional
Not currently used

Do not leave developers guessing.

36. First-Time Setup

A new team member should follow approximately this sequence:

1. Install Git
2. Install required Node.js version
3. Install required JDK
4. Install project build tool/wrapper
5. Install IDE
6. Clone SkillBridge
7. Open repository
8. Create local environment configuration
9. Configure Firebase development project
10. Configure Supabase development access
11. Verify database connectivity
12. Start backend
13. Start frontend
14. Test Firebase login
15. Test one protected API
16. Run tests
17. Create feature branch
18. Pick an assigned Issue
37. Setup Verification

Before a developer begins feature work, they should verify:

[ ] Repository clones successfully
[ ] Git identity configured
[ ] Frontend starts
[ ] Backend starts
[ ] Firebase authentication works
[ ] Firebase ID token reaches backend
[ ] Backend verifies token
[ ] PostgreSQL connection works
[ ] Supabase Storage access works where required
[ ] API can be tested
[ ] Tests run successfully
38. New Developer Checklist

A new developer is ready when:

Developer
   ↓
Clone
   ↓
Install
   ↓
Configure
   ↓
Run
   ↓
Authenticate
   ↓
Database connection
   ↓
Test API
   ↓
Create branch

Only after this should they receive a feature task.

39. Environment Files

Recommended:

SkillBridge/
├── frontend/
│   ├── .env.example
│   └── .env.local        ← local only
│
├── backend/
│   └── .env.example      ← template/reference
│
└── .gitignore

Do not assume the exact environment-file loading mechanism until the backend/frontend implementation is created.

40. .gitignore Rules

At minimum, local/generated content should be excluded where applicable:

node_modules/
.next/
target/
build/
dist/
.env
.env.*
!.env.example

The final .gitignore should be created for the actual technology stack.

Be careful not to ignore files the project needs to commit.

41. IDE

Each developer may use their preferred IDE.

Examples:

VS Code
IntelliJ IDEA
WebStorm
Eclipse

The team should not require everyone to use the same IDE.

However, formatting/linting configuration should be shared through the repository wherever practical.

42. Code Formatting

Formatting should be automated.

The repository should define formatter/linter configuration rather than relying on:

"I prefer this style."

The team should use the same formatting rules for:

Java
TypeScript/JavaScript
JSON
YAML
Markdown

where tooling is available.

43. Dependency Installation

When adding a new dependency:

Need identified
 ↓
Check whether existing dependency can solve it
 ↓
Evaluate security/maintenance
 ↓
Add dependency
 ↓
Test
 ↓
PR

Do not install libraries just because they look useful.

44. Dependency Updates

Dependency updates should be deliberate.

Before upgrading a critical dependency:

Current version
     ↓
Compatibility check
     ↓
Tests
     ↓
Upgrade
     ↓
Integration test

Do not randomly upgrade framework versions during feature development.

45. Local Data

Use development/test data for local development.

Avoid using real personal data such as:

Real student resumes
Real phone numbers
Real certificates
Real recruiter notes
Real private credentials

in publicly accessible development environments.

46. Test Accounts

The team should maintain clearly identified test accounts.

Example:

student-test
employer-test
admin-test

Do not share passwords through GitHub or commit them into documentation.

Use an approved team password-management process if shared credentials are genuinely necessary.

47. Firebase Test Users

Firebase Authentication should contain appropriate development/test accounts where needed.

Production users must not be used casually for development testing.

48. Supabase Access

Only team members who need database/storage access should receive it.

Differentiate:

Developer access
Administrator access
Production access

Don't give every developer unrestricted production credentials.

49. Production Credentials

Production secrets are not development secrets.

Examples:

Production database password
Production Firebase credentials
Production GitHub OAuth secret
Production Supabase service credential

They must not be copied into:

WhatsApp
GitHub Issues
README
source code
50. Troubleshooting Rule

When setup fails:

1. Read the error
2. Identify which service failed
3. Check environment variables
4. Check versions
5. Check credentials
6. Check network/service availability
7. Check documentation
8. Ask the team with the exact error

Do not repeatedly reinstall everything without understanding the failure.

51. Team Support Rule

When asking for help, provide:

Operating system
Command executed
Expected result
Actual result
Exact error
Relevant configuration (without secrets)

Good:

Windows 11
Backend startup failed
Error: database connection refused
DATABASE_URL is configured

Bad:

"Backend isn't working."

52. Never Share Secrets While Debugging

Before posting logs/screenshots:

Remove:
API keys
Tokens
Passwords
Private URLs
Service credentials
Personal data

A screenshot can leak a secret just as easily as source code.

53. Development Ports

The project should define actual ports in configuration files.

Avoid hardcoding assumptions such as:

Frontend = 3000
Backend = 8080

unless the implementation explicitly standardizes them.

The setup guide should always reflect the actual project configuration.

54. Health Check

The backend should eventually expose a basic health endpoint, such as:

GET /actuator/health

if Spring Boot Actuator is enabled.

The exact health endpoint is an implementation decision and should be documented once configured.

55. Local Development Flow

After setup, the normal workflow is:

Open terminal
   ↓
git switch main
   ↓
git pull
   ↓
git switch -c feature/<task>
   ↓
Start backend
   ↓
Start frontend
   ↓
Develop
   ↓
Test
   ↓
Commit
   ↓
Push
   ↓
PR
56. Before Every Development Session

Recommended:

git switch main
git pull
git switch <your-feature-branch>

If the feature branch is old, update it according to the team's branch-update strategy before continuing.

Developers should avoid starting new work from stale code.

57. Before Creating a PR

Run the project's standard checks:

[ ] Build succeeds
[ ] Unit tests pass
[ ] Relevant integration tests pass
[ ] Lint/format checks pass
[ ] No secrets added
[ ] No unrelated files changed
[ ] API contract still matches
[ ] Database migration included if needed

Then create the PR.

58. "Works on My Machine" Prevention

The team should maintain:

Exact runtime versions
Shared environment templates
Consistent commands
Documented dependencies
Database migrations
Automated tests
Standard formatting
Clear setup instructions

The objective is:

Developer A
Developer B
Developer C
Developer D
Developer E

        ↓

Same repository
Same rules
Compatible environment
Predictable setup
59. Final Setup Architecture
                    DEVELOPER
                        │
             ┌──────────┴──────────┐
             │                     │
        Next.js / React       Spring Boot
             │                     │
             │              ┌──────┼──────┐
             │              │      │      │
             │              ▼      ▼      ▼
             │          Firebase  DB    Storage
             │             Auth    │        │
             │                     │        │
             └─────────────────────┴────────┘
                                   │
                              Supabase
                          ┌────────┴────────┐
                          ▼                 ▼
                     PostgreSQL          Storage

Spring Boot
     │
     ├── GitHub API
     └── AI service
60. Final New-Developer Checklist

A developer must be able to answer yes to all of these before taking a normal development task:

[ ] I can clone SkillBridge.
[ ] Git works.
[ ] I can create a branch.
[ ] Frontend runs.
[ ] Backend runs.
[ ] Firebase login works.
[ ] Backend verifies my Firebase token.
[ ] I can access the development database.
[ ] I understand Supabase Storage access.
[ ] I can call a protected API.
[ ] I can run tests.
[ ] I know where the requirements are.
[ ] I know where the API specification is.
[ ] I know how to create a PR.
[ ] I know where to report a blocker.
61. Current Architecture Decisions

These are now locked:

Frontend
→ Next.js / React

Backend
→ Java + Spring Boot

Authentication
→ Firebase Authentication

Authentication transport
→ Firebase ID Token / Bearer

User roles
→ Exactly one role per account

Database
→ Supabase PostgreSQL

File storage
→ Supabase Storage

Version control
→ Git + GitHub

API style
→ REST

API version
→ /api/v1

API response
→ Standard success/error structure

Database migrations
→ Flyway

Vector search
→ pgvector where required

Still intentionally not locked:

Production hosting
Exact AI provider/fallback policy
Exact runtime versions
Exact Supabase bucket structure
Exact development/production environment topology
