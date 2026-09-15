# SkillBridge Development Workflow

## 1. Purpose

The SkillBridge development workflow defines:

- How work is assigned
- How GitHub is used
- How branches are created
- How commits are written
- How Pull Requests are created
- How code is reviewed
- How bugs are handled
- How database/API changes are coordinated
- How work moves from idea → production code
- Who is allowed to merge
- How the team communicates

The goal is:

```text
5 developers
   ↓
1 codebase
   ↓
1 agreed workflow
   ↓
predictable development
```

## 2. Golden Rule

The team's most important rule:

> No feature is considered complete until its code is implemented, tested, reviewed, integrated, and documented where necessary.

Therefore:

```text
Code written
    ≠
Feature completed

Code
 ↓
Test
 ↓
Review
 ↓
Merge
 ↓
Integration test
 ↓
DONE
```

## 3. GitHub Repository Structure

The SkillBridge repository should contain:

```text
SkillBridge/
│
├── frontend/
├── backend/
├── docs/
│
├── README.md
├── CONTRIBUTING.md
└── .gitignore
```

Later, additional folders can be added when actually required.

The `docs/` directory contains the project's controlled technical documentation.

## 4. Branch Strategy

For the MVP, keep the branch model simple.

### Main branch

`main`

Purpose: stable, integrated SkillBridge code.

- `main` should be protected.
- Developers should normally not push directly to `main`.

## 5. Feature Branches

Every feature gets its own branch.

Format:

`feature/<feature-name>`

Examples:

- `feature/student-profile`
- `feature/github-integration`
- `feature/evidence-engine`
- `feature/resume-builder`
- `feature/job-matching`
- `feature/employer-jobs`

## 6. Bug-Fix Branches

Format:

`fix/<bug-name>`

Examples:

- `fix/login-token-error`
- `fix/resume-preview`
- `fix/github-sync`

## 7. Documentation Branches

Format:

`docs/<topic>`

Examples:

- `docs/api-specification`
- `docs/database-design`
- `docs/setup-guide`

## 8. Branch Lifecycle

Every branch should follow:

```text
main
 ↓
Create branch
 ↓
Implement
 ↓
Test
 ↓
Push
 ↓
PR
 ↓
Review
 ↓
Merge
 ↓
Delete branch
```

After merging, the branch should normally be deleted.

## 9. Starting a New Task

Before coding:

```bash
git switch main
git pull
```

This ensures you start from the latest `main`.

Then:

```bash
git switch -c feature/student-profile
```

Now you're working on your feature branch.

## 10. Work in Small Changes

Avoid huge changes such as:

> "Implement entire student system"

Break the work down. Instead:

- Student profile API
- Student profile UI
- Education CRUD
- Experience CRUD
- Project CRUD

This makes reviews much easier.

## 11. Commit Rules

Every commit should represent one logical change.

### Good

- Add student profile entity
- Implement profile creation API
- Add profile form validation
- Fix profile update authorization

### Bad

- update
- changes
- final
- final-final

## 12. Commit Format

Use imperative language.

Recommended:

`<verb> <what changed>`

Examples:

- Add student profile API
- Implement GitHub repository sync
- Fix duplicate application validation
- Update resume editor layout
- Add application status history

Don't write giant commit messages for tiny changes.

## 13. The Basic Git Cycle

Every developer should understand:

```bash
git status
git add .
git commit -m "Add student profile API"
git push
```

Before starting more work:

```bash
git switch main
git pull
```

Then create/update the feature branch.

## 14. Pull Request Rules

Every feature merged into `main` should normally go through a Pull Request.

PR flow:

```text
Developer
   ↓
Push branch
   ↓
Create PR
   ↓
Review
   ↓
Changes requested?
   ├── Yes → Fix → Push → Review again
   └── No
          ↓
       Approve
          ↓
        Merge
```

## 15. PR Title

A PR title should clearly explain the change.

### Good

- Add GitHub repository synchronization
- Implement student profile APIs
- Fix duplicate application submission

### Bad

- Update code

## 16. PR Description

Every meaningful PR should explain:

- What changed?
- Why was it changed?
- How was it tested?

Recommended structure:

```md
## What changed

- Added student profile APIs
- Added profile validation

## Why

Provides the core student profile functionality.

## Testing

- Tested POST profile
- Tested GET profile
- Tested invalid requests

## Related Issue

Closes #12
```

## 17. Pull Request Size

Avoid enormous PRs.

Bad:

- 500+ changed files
- for one feature.

Prefer:

```text
Small feature
 ↓
small PR
 ↓
easy review
 ↓
easy debugging
```

A large architectural change may naturally require a large PR, but that should be the exception.

## 18. Code Review

The reviewer should check at least:

- **Correctness**: Does the code actually solve the requirement?
- **Security**: Can another user access this data?
- **Validation**: Does the backend validate input?
- **Architecture**: Does the code belong in the correct layer?
- **Database**: Could this cause duplicates, broken references, or data loss?
- **API**: Does implementation match the API specification?
- **Tests**: Is the important behavior tested?
- **Scope**: Does the PR contain unrelated changes?

## 19. Review Comments

Review should be about the code, not the person.

Bad:

- "This code is bad."
- "Why did you do this?"

Better:

- "Can we move this authorization check into the service layer so it isn't dependent on the controller?"
- "Could you explain whether this field can be null? The database design currently treats it as optional."

The goal is improvement, not winning an argument.

## 20. Approval Rule

For SkillBridge MVP:

- At least one other team member should review a PR before merging it into `main`.

For particularly sensitive areas, such as:

- authentication
- authorization
- database migrations
- application access control
- external credentials

The team should have a second reviewer whenever practical.

## 21. Who Can Merge?

I recommend:

- Team Lead
- one delegated senior reviewer

can merge approved PRs.

But don't make one person permanently responsible for every merge if that creates a bottleneck.

The point is:

```text
Review → approval → merge
```

not:

> Team lead must personally approve 300 tiny UI changes.

## 22. `main` Protection

Configure GitHub so that `main` requires:

- ✓ Pull Request
- ✓ At least 1 approval
- ✓ Passing checks

and prevents:

- ✗ Direct pushes

We should configure this in GitHub when the team is ready.

## 23. Issues

Every meaningful piece of work should exist as a GitHub Issue.

Example:

### Issue #25

**Title**

Implement Student Profile API

**Description**

```md
## Goal

Allow students to create, view and update their professional profile.

## Requirements

- Create profile
- Get current profile
- Update profile
- Validate input
- Enforce ownership

## Done when

- API implemented
- Tests added
- API matches specification
- PR merged
```

## 24. Issue Assignment

Every task gets an owner.

Example:

| Issue | Task | Assigned |
| --- | --- | --- |
| #25 | Student Profile API | Member 2 |
| #26 | GitHub OAuth | Member 3 |
| #27 | Evidence Analysis | Member 4 |
| #28 | Resume Editor | Member 1 |
| #29 | Employer Job Creation | Member 5 |

One person owns the task.

Other people can contribute.

## 25. Labels

Use a small, controlled set.

| Category | Labels |
| --- | --- |
| Type | `feature`, `bug`, `docs`, `refactor`, `test` |
| Priority | `P0`, `P1`, `P2`, `P3` |
| Module | `frontend`, `backend`, `database`, `github`, `evidence`, `resume`, `matching`, `employer` |

Don't create 50 meaningless labels.

## 26. Priority

Define priority clearly.

| Priority | Definition |
| --- | --- |
| P0 | Critical blocker/security issue |
| P1 | Important MVP functionality |
| P2 | Normal work |
| P3 | Nice-to-have |

Example:

- Firebase authentication broken → P0
- Student profile → P1
- Resume animation → P3

## 27. Project Board

Use a GitHub Project board:

```text
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
```

Each issue moves through the board.

## 28. Definition of Ready

Before a task moves to TODO, it should have enough information to start.

A task should have:

- Requirement
- Expected behavior
- Relevant API/data requirements
- Acceptance criteria
- Owner
- Dependencies

Example:

> Build GitHub Repository Import

shouldn't be assigned before the team knows:

- Which GitHub data is required
- Where it is stored
- Which API endpoint is expected
- What happens when GitHub fails
- What the frontend needs

## 29. Definition of Done

A task is DONE only when:

- ✓ Requirement implemented
- ✓ Backend/frontend integrated
- ✓ Validation implemented
- ✓ Tests completed
- ✓ PR reviewed
- ✓ PR merged
- ✓ No known blocking bug
- ✓ Documentation updated if required

This is very important for you as team lead.

Otherwise developers will say:

> "My part is done."

while the feature still doesn't work.

## 30. Team Communication

Use different tools for different purposes.

### GitHub

For:

- Code
- Issues
- PRs
- Technical discussions
- Documentation

### Team chat

For:

- Quick questions
- Meeting coordination
- Blocker notifications
- General communication

Don't make technical decisions disappear inside a chat history.

If an important decision is made in chat, record it in the appropriate GitHub issue/document.

## 31. Daily Standup

Keep it short.

Each member answers:

- **Yesterday:** What did I finish?
- **Today:** What will I work on?
- **Blocked:** Do I need help?

Example:

> "Yesterday I implemented the profile API. Today I'll add validation. I'm blocked because the database migration isn't merged."

You, as team lead, then resolve the blocker.

## 32. Weekly Planning

At the beginning of the week:

```text
Review backlog
    ↓
Choose achievable work
    ↓
Assign owners
    ↓
Identify dependencies
    ↓
Start sprint
```

Don't assign 40 tasks because everyone feels productive.

Assign work the team can actually complete.

## 33. Sprint

For your college project, a 1-week sprint is practical.

Example:

### Sprint 1

- Authentication
- Project setup
- Database setup
- Basic profile

### Sprint 2

- Education
- Experience
- Projects
- Skills

### Sprint 3

- GitHub integration

### Sprint 4

- Evidence Engine

and so on.

The exact timeline will be finalized in the roadmap.

## 34. Handling Dependencies

This is one of your most important team-lead skills.

Example:

```text
Frontend Profile Page
        ↓
needs Profile API
        ↓
needs Database schema

Database
   ↓
Backend API
   ↓
Frontend
```

But don't make everyone wait unnecessarily.

Frontend can often use an agreed API contract/mock data while backend implementation is underway.

## 35. API Contract Rule

Suppose backend developer implements:

`GET /api/v1/profile/me`

Frontend developer builds against that exact contract.

If backend suddenly changes it to:

`GET /api/v1/user/profile`

without telling anyone, integration breaks.

Therefore:

- API changes must be communicated.
- The API specification must be updated.

## 36. Database Migration Rule

Only designated developers should make schema migrations.

Recommended:

- Member 3 → Database owner

But they don't have absolute authority to silently redesign the database.

Workflow:

```text
Need database change
       ↓
Check 04-database-design.md
       ↓
Discuss if schema changes
       ↓
Update design
       ↓
Create migration
       ↓
Test migration
       ↓
PR
       ↓
Merge
```

Never have one teammate manually modify another developer's local database and say:

> "Just add this column."

## 37. Environment Variables

Never commit:

- Firebase private credentials
- GitHub OAuth secrets
- AI API keys
- Database passwords

Instead use:

- `.env`
- environment-specific secret management

The repository should contain:

- `.env.example`

with placeholders:

```env
FIREBASE_PROJECT_ID=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
DATABASE_URL=
AI_API_KEY=
```

but never actual secrets.

## 38. Local Development

Every developer should be able to clone the repository and understand:

- How to install
- How to configure
- How to run frontend
- How to run backend
- How to connect database
- How to run tests

This should eventually go in:

`docs/setup.md`

A project that only runs on the Team Lead's laptop is a failed team workflow.

## 39. Testing Levels

Don't rely only on clicking around manually.

Use:

```text
Unit Tests
     ↓
Integration Tests
     ↓
API Tests
     ↓
End-to-End Testing
```

Example:

- **Unit test**
  - Check: Application status transition logic.
- **Integration test**
  - Check: Spring Boot + PostgreSQL interaction.
- **API test**
  - Check: `POST /api/v1/applications`
- **End-to-end test**
  - Check:

```text
Student
 ↓
Profile
 ↓
Resume
 ↓
Job
 ↓
Apply
 ↓
Employer
 ↓
Shortlist
```

## 40. Bug Workflow

When someone finds a bug:

```text
Bug discovered
      ↓
Create Issue
      ↓
Reproduce
      ↓
Assign
      ↓
Fix branch
      ↓
Test
      ↓
PR
      ↓
Review
      ↓
Merge
      ↓
Close Issue
```

Example:

`fix/duplicate-application`

## 41. Severity vs Priority

Don't confuse them.

- **Severity**: How badly does the bug affect the system?
- **Priority**: How urgently should the team fix it?

Example:

- Resume button has a 2-pixel alignment issue. → Low severity, low priority.
- Another student can see someone else's private resume. → Extremely high severity and priority.

## 42. Handling disagreements

Suppose two developers disagree:

- "We should use approach A."
- "No, approach B is better."

Don't resolve it by:

- "I'm team lead, so do A."

Instead:

```text
Requirement
   ↓
Technical reasoning
   ↓
Trade-offs
   ↓
Decision
   ↓
Document important decision
```

You may make the final decision when needed, but it should be based on the project's requirements and constraints.

## 43. Team Lead Responsibilities

Your job is primarily:

- PLAN
- COORDINATE
- REMOVE BLOCKERS
- REVIEW
- INTEGRATE
- TRACK

Not:

- Tell everyone exactly how to write every line.

You should create clarity, not micromanagement.

## 44. SkillBridge Team Ownership

For our current 5-person structure:

| Member | Primary Ownership |
| --- | --- |
| Member 1 | Frontend + Resume UI |
| Member 2 | Spring Boot + Security/API |
| Member 3 | PostgreSQL + GitHub integration |
| Member 4 | AI + Evidence + Matching |
| Member 5 | Employer/ATS + Testing/DevOps |

Each member also reviews other members' work.

## 45. Cross-review

Don't create this:

- Member 1 reviews only Member 1
- Member 2 reviews only Member 2

Instead:

- Member 1 → reviews Member 2
- Member 2 → reviews Member 3
- Member 3 → reviews Member 4
- Member 4 → reviews Member 5
- Member 5 → reviews Member 1

Or vary it by sprint.

This prevents knowledge silos.

## 46. Knowledge Sharing

For every critical module, at least two people should understand it.

Especially:

- Firebase authentication
- Database
- GitHub integration
- Evidence Engine
- Application workflow
- Deployment

You don't want:

> "Our GitHub integration expert left the team, so nobody knows how it works."

Document important technical decisions.

## 47. The complete team workflow

This is the diagram you should remember:

```text
                    REQUIREMENT
                         │
                         ▼
                    GITHUB ISSUE
                         │
                         ▼
                       ASSIGN
                         │
                         ▼
                    CREATE BRANCH
                         │
                         ▼
                        CODE
                         │
                         ▼
                       TEST
                         │
                         ▼
                      COMMIT
                         │
                         ▼
                       PUSH
                         │
                         ▼
                 CREATE PULL REQUEST
                         │
                         ▼
                    CODE REVIEW
                         │
                  ┌──────┴──────┐
                  │             │
               Changes       Approved
                  │             │
                  ▼             ▼
                Fix           Merge
                  │             │
                  └──────►      │
                                ▼
                              main
                                │
                                ▼
                       INTEGRATION TEST
                                │
                                ▼
                               DONE
```

## 48. What happens when YOU receive a PR?

Use this checklist:

- □ Does it solve the Issue?
- □ Does it match requirements?
- □ Does it match API/database design?
- □ Does it respect authentication/authorization?
- □ Does it validate input?
- □ Does it expose private data?
- □ Are tests present?
- □ Does it break unrelated functionality?
- □ Is the code understandable?
- □ Has the developer tested it?

Then:

- Approve
- or
- Request Changes

## 49. Your role during a sprint

Imagine this happens:

> Member 3: "GitHub integration is blocked because OAuth isn't configured."

You don't say:

> "Please finish it."

You ask:

- What exactly is missing?

They say:

> "We need GitHub OAuth credentials."

Then you create/resolve the configuration task.

Your job is to remove the obstacle.

## 50. What NOT to do as team lead

Avoid:

- ❌ Changing requirements every day
- ❌ Assigning vague tasks
- ❌ Directly editing everyone's branches
- ❌ Merging unreviewed code
- ❌ Keeping decisions only in WhatsApp
- ❌ Letting one person own all knowledge
- ❌ Allowing secrets into GitHub
- ❌ Starting features without requirements
- ❌ Building future features before MVP
- ❌ Ignoring documentation

## 51. SkillBridge development lifecycle

Our complete process is now:

```text
Requirements
    ↓
User Flows
    ↓
Architecture
    ↓
Database Design
    ↓
API Specification
    ↓
GitHub Issue
    ↓
Task Assignment
    ↓
Feature Branch
    ↓
Implementation
    ↓
Testing
    ↓
PR
    ↓
Code Review
    ↓
Merge
    ↓
Integration Testing
    ↓
Release
```

This is the process your team will follow.

## 52. Current documents

Your project documentation is now:

```text
docs/
│
├── 01-requirements.md
├── 02-user-flows.md
├── 03-system-architecture.md
├── 04-database-design.md
├── 05-api-specification.md
└── 06-development-workflow.md
```
