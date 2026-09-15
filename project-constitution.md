# SkillBridge — Project Constitution

**Version:** 1.0  
**Status:** Team Working Agreement  
**Project:** SkillBridge  
**Repository:** `heyvinay-lab/SkillBridge`  
**Primary Branch:** `main`

---

## 1. Purpose

This document defines the rules, principles, technical standards, development workflow, and decision-making process that all SkillBridge team members must follow.

The purpose is to ensure that five developers work as **one engineering team** rather than as five independent developers.

This document is the team's working contract.

---

# 2. Product Identity

## 2.1 Product Name

**SkillBridge**

## 2.2 Product Definition

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

## 2.3 Core Product Idea

```text
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

3. Product Principles

SkillBridge follows these principles:

3.1 Evidence Before Claims

The system should distinguish between:

Self-reported information
Supporting evidence
Verified evidence
Unverified claims

The system must never fabricate evidence.

3.2 Truthful Career Representation

SkillBridge may improve presentation of candidate information but must not invent:

Skills
Experience
Projects
Achievements
Metrics
Responsibilities
Certifications
3.3 Explainability

Important intelligent outputs should provide understandable reasons.

For example, job matching should explain:

Strong Match
Partial Match
Missing Skill
Supporting Evidence

rather than presenting only a mysterious score.

3.4 User Control

Candidates retain control over their professional information and visibility.

Private information must never become publicly visible simply because it exists in the database.

3.5 Security by Default

Authentication, authorization, validation, and ownership checks belong to the backend.

4. Project Scope
4.1 MVP

The MVP contains:

Authentication
Firebase Authentication
User initialization
One role per account
Student
Employer
Admin
Student
Professional profile
Education
Experience
Projects
Skills
Certifications
Achievements
External account connections
GitHub integration
Evidence management
Resume management
Resume versions
Resume Truth Checker
Job analysis
Job matching
Apply with SkillBridge
Application tracking
Employer
Company profile
Company membership
Job creation
Job publishing
Job management
Candidate search
Candidate profile
Application review
Shortlisting
Hiring pipeline
Application notes
Interview management
Platform
Role-based authorization
Validation
Error handling
Testing
Deployment
Documentation
5. Post-MVP Features

The following are not part of the initial MVP unless explicitly added through the change process:

College dashboards
College analytics
Rankings
Hackathons
Career events
Scholarships
Internship marketplace
Sponsored skill programs
Advanced interview simulation
Advanced project generation
Advanced recommendation systems
Advanced analytics

These belong to the future roadmap.

6. MVP Freeze Rule

Once the team declares the MVP frozen:

New idea
   ↓
GitHub Issue
   ↓
Evaluate
   ↓
Future roadmap

A new feature must not enter the active sprint simply because someone has an idea.

This prevents scope creep.

7. System Architecture

The agreed high-level architecture is:

                         USERS
                           │
                           ▼
                    Next.js / React
                           │
                           ▼
                    Spring Boot API
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
       PostgreSQL        AI Layer      External APIs
             │             │             │
             │             │             └── GitHub
             │             │
             │             └── Ollama / AI provider
             │
             └── pgvector where required
8. Authentication Architecture

Authentication will use:

Firebase Authentication

Flow:

User
 ↓
Firebase Authentication
 ↓
Firebase ID Token
 ↓
Authorization: Bearer <token>
 ↓
Spring Boot
 ↓
Verify Firebase Token
 ↓
Extract Firebase UID
 ↓
Find SkillBridge User
 ↓
Check Role
 ↓
Authorize Request

Firebase is responsible for identity/authentication.

PostgreSQL stores SkillBridge application data.

9. User Roles

Each SkillBridge account has exactly one application role.

Allowed roles:

STUDENT
EMPLOYER
ADMIN

The role must be determined by backend/database state.

The frontend must never be trusted to determine its own permissions.

10. API Standards
API style

SkillBridge uses:

REST API

Version

All API endpoints start with:

/api/v1

Example:

GET /api/v1/profile/me
Authentication

Protected APIs receive:

Authorization: Bearer <Firebase ID Token>
Response format
Success
{
  "success": true,
  "data": {},
  "message": "Operation completed successfully"
}
Error
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message"
  }
}

The error.code should be stable and suitable for frontend logic.

11. API Security Rules

Backend must never trust the following values supplied by the client:

User ID
Firebase UID
Role
Resource ownership
Company ownership
Evidence verification status

The backend derives these from:

Authenticated identity
+
Database state
+
Business rules
12. Database Rules

Database:

PostgreSQL

Primary keys:

UUID

Firebase UID:

External identity
Unique
Not the PostgreSQL primary key

Timestamps:

TIMESTAMPTZ

Important historical business records should not be physically deleted unnecessarily.

Examples:

Jobs with applications
Applications
Application history
Submitted resume versions

Use status/soft deletion where appropriate.

13. Database Design Principle

The authoritative candidate data belongs in structured profile/domain tables.

Profile
 ├── Education
 ├── Experience
 ├── Projects
 ├── Skills
 ├── Certifications
 ├── Achievements
 └── Evidence

Resumes are generated/presented from this information.

Therefore:

A resume must not become the authoritative source for candidate data.

14. Database Change Rule

No developer should casually change the schema.

The process is:

Requirement
   ↓
Check existing schema
   ↓
Discuss proposed change
   ↓
Update database design
   ↓
Create migration
   ↓
Test migration
   ↓
PR
   ↓
Review
   ↓
Merge

Database migrations should be version controlled.

Recommended migration tool:

Flyway

15. Core Database Entities

The current core model includes:

users
roles
profiles
educations
experiences
projects
skills
profile_skills
project_skills
certifications
achievements
evidence
external_accounts
github_repositories
project_repositories
resumes
resume_versions
companies
company_memberships
jobs
job_skills
job_questions
applications
application_answers
application_status_history
application_notes
interviews
candidate_preferences

The database design document is the authority for exact fields and relationships.

16. Evidence Rules

Evidence is a core SkillBridge concept.

Evidence can originate from sources such as:

GitHub
Projects
Certifications
Experience
Coding profiles
Portfolio
Assessments
Documents
Self-reported information

Possible states include:

SELF_REPORTED
SUPPORTED
VERIFIED
UNVERIFIED
REJECTED

The system must not mark evidence as VERIFIED simply because an AI model believes it is genuine.

Verification requires a defined verification mechanism.

17. AI Rules

AI is an assistive component, not the authority over business truth.

AI may be used for:

Job description analysis
Skill extraction
Resume analysis
Claim analysis
Evidence interpretation
Semantic matching
Interview question generation
Interview feedback

Traditional application logic controls:

Authentication
Authorization
Ownership
Database integrity
Application state
Resume versioning
Hiring pipeline
Validation
Security
18. AI Failure Rule

AI failure must not corrupt or destroy user data.

If an AI provider is unavailable:

AI unavailable
      ↓
Controlled error
      ↓
User data remains safe

Core CRUD functionality should not depend unnecessarily on AI availability.

19. External Integration Rules

External integrations must be implemented only after confirming the provider's current API/access rules.

Initial priority:

GitHub

Potential future integrations require separate validation.

The team must not assume that an API exists merely because a website exists.

20. Git Repository Rules

Repository:

heyvinay-lab/SkillBridge

Primary branch:

main

main should represent stable, integrated code.

Direct pushes to main should be disabled for normal development.

21. Branch Naming
Features
feature/student-profile
feature/github-integration
feature/evidence-engine
feature/resume-builder
feature/job-matching
Bug fixes
fix/login-error
fix/github-sync
fix/application-duplicate
Documentation
docs/api-specification
docs/database-design
22. Commit Rules

Commits should describe one logical change.

Good:

Add student profile API
Implement GitHub repository sync
Fix duplicate application validation
Add resume version history

Avoid:

update
changes
final
final2
latest

Use imperative wording.

23. Pull Request Rules

All meaningful feature work should go through a Pull Request.

Workflow:

Issue
 ↓
Branch
 ↓
Implementation
 ↓
Testing
 ↓
Commit
 ↓
Push
 ↓
PR
 ↓
Review
 ↓
Approval
 ↓
Merge
24. Pull Request Requirements

A PR should clearly state:

What changed?
Why was it changed?
How was it tested?
Which issue does it address?

Example:

## What changed

- Added student profile API
- Added validation
- Added authorization checks

## Testing

- Tested create profile
- Tested update profile
- Tested unauthorized access

## Related Issue

Closes #25
25. Code Review

At least one other developer should review meaningful PRs before merging.

Reviewers should consider:

Correctness
Security
Authorization
Validation
Database integrity
API contract
Tests
Performance where relevant
Maintainability
Scope

Review comments must focus on the implementation, not the person.

26. Sensitive Changes

Additional review should be used when practical for:

Firebase authentication
Authorization
Database migrations
Personal-data access
File access
GitHub OAuth
External credentials
Application permissions
27. Issue Rules

Work should be represented as GitHub Issues.

An issue should contain:

Title
Goal
Requirements
Acceptance Criteria
Dependencies
Owner
Priority

Example:

Title:
Implement GitHub Repository Sync

Goal:
Import permitted GitHub repository information.

Acceptance Criteria:
- OAuth works
- Repositories can be fetched
- Data is normalized
- Existing data is updated safely
- Errors are handled
- Tests exist
28. Definition of Ready

A task can enter active development only when:

Requirement is clear
Expected behavior is defined
Dependencies are known
Acceptance criteria exist
Owner is assigned
Relevant API/database design is known

A developer should not start an ambiguous task just because it was assigned.

29. Definition of Done

A task is considered complete only when:

✓ Requirement implemented
✓ Validation implemented
✓ Authorization checked
✓ Tests completed
✓ API/database contract respected
✓ PR reviewed
✓ PR merged
✓ Integration checked
✓ Documentation updated where necessary
30. Project Board

The GitHub Project Board should use:

BACKLOG
   ↓
TODO
   ↓
IN PROGRESS
   ↓
IN REVIEW
   ↓
TESTING
   ↓
DONE

The board is the team's primary visual progress tracker.

31. Task Ownership

Every active task has one primary owner.

Example:

Student Profile API
→ Member 2

GitHub Integration
→ Member 3

Evidence Engine
→ Member 4

Resume UI
→ Member 1

Employer ATS
→ Member 5

Ownership does not mean nobody else can help.

It means one person is responsible for ensuring the task reaches completion.

32. Team Structure

Recommended five-member ownership:

Member	Primary responsibility
Member 1	Frontend + Resume UI
Member 2	Backend + Firebase security
Member 3	Database + GitHub integration
Member 4	AI + Evidence + Matching
Member 5	Employer + ATS + Testing/DevOps

Responsibilities may overlap during integration.

33. No Knowledge Silos

Critical modules should be understood by more than one team member.

At minimum:

Authentication
Database
GitHub integration
Evidence Engine
Application workflow
Deployment

Documentation and cross-review should be used to maintain shared knowledge.

34. Communication Rules

Use GitHub for permanent technical information:

Issues
PRs
Technical discussions
Architecture decisions
Documentation

Use team chat for:

Quick questions
Meeting coordination
Short updates
Immediate blockers

Important decisions made in chat should be recorded in GitHub/docs.

35. Daily Standup

Keep daily standups short.

Each member reports:

Yesterday:
What did I complete?

Today:
What am I doing?

Blocked:
Do I need help?

The team lead focuses on removing blockers rather than turning the standup into a long meeting.

36. Weekly Sprint

Default planning unit:

1 week

At the beginning of a sprint:

Review backlog
 ↓
Select achievable tasks
 ↓
Assign owners
 ↓
Identify dependencies
 ↓
Start sprint

At the end:

Review completed work
 ↓
Review unfinished work
 ↓
Test integrated features
 ↓
Update roadmap
37. Dependency Management

The team should explicitly track dependencies.

Example:

Database
   ↓
Backend API
   ↓
Frontend

However, developers should use agreed API contracts/mock data when possible rather than unnecessarily waiting for every other component to finish.

38. API Contract Changes

An API change must be communicated before implementation.

Example:

Changing:

GET /api/v1/profile/me

to:

GET /api/v1/user/profile

is not an informal implementation detail.

The team must:

Discuss
 ↓
Update API specification
 ↓
Update affected clients
 ↓
Implement
 ↓
Test
39. Environment and Secrets

Never commit secrets to GitHub.

Examples:

Firebase private credentials
GitHub client secret
Database password
AI API key
Storage credentials

Use environment variables/secrets.

The repository may contain:

.env.example

but not real credentials.

40. Local Development Requirement

Every developer must be able to:

Clone repository
 ↓
Configure environment
 ↓
Run frontend
 ↓
Run backend
 ↓
Connect to development database
 ↓
Run tests

The project must not depend on one developer's personal computer.

41. Testing Philosophy

Testing should occur at multiple levels:

Unit
 ↓
Integration
 ↓
API
 ↓
End-to-End

Critical user journeys should eventually be covered end-to-end.

Example:

Student
 ↓
Login
 ↓
Profile
 ↓
Resume
 ↓
Job
 ↓
Application
 ↓
Employer
 ↓
Shortlist
42. Bug Management

Every meaningful bug should become a GitHub Issue.

Workflow:

Bug Found
 ↓
Reproduce
 ↓
Issue
 ↓
Assign
 ↓
Fix Branch
 ↓
Test
 ↓
PR
 ↓
Review
 ↓
Merge

Do not silently fix important bugs without recording them when they affect project tracking.

43. Security Priority

Security issues involving:

cross-user access
authentication bypass
unauthorized employer access
private profile exposure
credential leakage
database corruption

receive the highest priority.

For example:

One candidate seeing another candidate's private resume is a critical issue, even if the UI otherwise works correctly.

44. Privacy Rules

Candidate information must be exposed according to:

Authentication
+
Authorization
+
Visibility settings
+
Business rules

The existence of information in PostgreSQL does not mean an API may expose it.

Employer responses must contain only recruiter-authorized candidate information.

Employer notes must never be exposed through student-facing APIs.

45. Resource Ownership

For user-owned resources:

Request
 ↓
Authenticated Firebase UID
 ↓
SkillBridge User
 ↓
Profile/resource ownership
 ↓
Permission check
 ↓
Operation

The client must not be allowed to modify another user's:

Profile
Projects
Resumes
Evidence
Applications
Other private resources
46. Database History

Avoid destructive deletion of important historical records.

For example:

Job
 ↓
Application
 ↓
Interview

Closing a job should normally mean:

status = CLOSED

rather than deleting the job and destroying application history.

47. Resume History

When a candidate applies using a resume version, the submitted version must remain identifiable.

Therefore:

Application
 ├── Resume
 └── Resume Version

The candidate may subsequently edit the resume without changing the historical version associated with the application.

48. Application History

Current status alone is not enough.

The system should maintain:

Application
     ↓
Application Status History

Example:

APPLIED
 ↓
SCREENING
 ↓
SHORTLISTED
 ↓
INTERVIEW
 ↓
SELECTED

This allows the platform to preserve the hiring timeline.

49. External Service Failure

External APIs must be treated as unreliable dependencies.

For GitHub or AI failures:

External service fails
       ↓
Controlled error
       ↓
Useful user message
       ↓
Existing SkillBridge data remains safe

The entire application must not crash merely because an external service temporarily fails.

50. Decision-Making Process

When the team faces an important technical decision:

Requirement
 ↓
Options
 ↓
Trade-offs
 ↓
Decision
 ↓
Documentation

Decisions should consider:

Project requirements
Security
Maintainability
Cost
Complexity
Development time
Team capability

The team lead coordinates decisions but should not make arbitrary decisions without technical reasoning.

51. Changing the Constitution

This document itself can evolve.

A significant change should follow:

Proposal
 ↓
Team discussion
 ↓
Impact assessment
 ↓
Agreement
 ↓
Update document
 ↓
Commit/PR

Do not silently change a major architecture rule inside an implementation PR.

52. Documentation Hierarchy

The SkillBridge project should use the following documentation hierarchy:

01-requirements.md
        ↓
02-user-flows.md
        ↓
03-system-architecture.md
        ↓
04-database-design.md
        ↓
05-api-specification.md
        ↓
06-development-workflow.md
        ↓
07-roadmap.md
        ↓
08-project-constitution.md

When documents conflict, the team must stop and resolve the conflict rather than letting developers choose different interpretations.

53. Conflict Resolution Between Documents

If two documents disagree:

STOP IMPLEMENTATION
        ↓
Identify conflict
        ↓
Determine intended product behavior
        ↓
Update affected documents
        ↓
Continue implementation

No developer should silently choose whichever document they prefer.

54. Team Lead Responsibilities

The team lead is responsible for maintaining:

Scope
Architecture consistency
Task ownership
Dependencies
Progress
Technical coordination
PR quality
Documentation
Integration
Risk management

The team lead is not responsible for personally writing all important code.

55. Developer Responsibilities

Every developer is responsible for:

Understanding assigned requirements
Writing maintainable code
Testing changes
Following Git workflow
Creating clear PRs
Reviewing teammates' work
Reporting blockers early
Protecting secrets
Following security rules
Documenting important technical decisions
56. What "Done" Means for the Team

The team should never declare:

"My code is done."

The correct statement is:

"The task is implemented, tested, reviewed, integrated, and meets its acceptance criteria."

That is the team's shared definition of completion.

57. Core SkillBridge Development Loop

The complete engineering lifecycle is:

                    REQUIREMENT
                         ↓
                     USER FLOW
                         ↓
                     ARCHITECTURE
                         ↓
                   DATABASE / API
                         ↓
                    GITHUB ISSUE
                         ↓
                     ASSIGNMENT
                         ↓
                      BRANCH
                         ↓
                       CODE
                         ↓
                       TEST
                         ↓
                       COMMIT
                         ↓
                        PUSH
                         ↓
                        PR
                         ↓
                      REVIEW
                         ↓
                   APPROVE / FIX
                         ↓
                       MERGE
                         ↓
                INTEGRATION TEST
                         ↓
                        DONE
58. Final Team Rule

When there is uncertainty, the developer should ask before assuming when the decision can affect:

Database structure
API contracts
Authentication
Authorization
Privacy
Product scope
External integrations
Data integrity

Small implementation decisions can be made independently.

High-impact architectural decisions must be discussed.

59. Constitution Status

This constitution becomes effective when the team agrees to it.

Until then, it is a proposed team standard.

Once approved:

Status → ACTIVE
Version → 1.0

All future major changes must be versioned.

60. Current Project Documentation
docs/
│
├── 01-requirements.md
├── 02-user-flows.md
├── 03-system-architecture.md
├── 04-database-design.md
├── 05-api-specification.md
├── 06-development-workflow.md
├── 07-roadmap.md
└── 08-project-constitution.md
