1. Roadmap objective

SkillBridge is large enough that trying to build everything simultaneously is risky.

The development strategy is:

Foundation
   ↓
Core Student Platform
   ↓
Evidence + GitHub
   ↓
Resume System
   ↓
Job Intelligence
   ↓
Applications
   ↓
Employer + ATS
   ↓
Integration
   ↓
Testing
   ↓
Deployment

The priority is to get the complete core loop working before adding advanced features.

2. Development phases
Phase 0 — Project Setup
Goal

Create the technical foundation that everyone will use.

Tasks
Repository
Git workflow
Project documentation
Frontend setup
Backend setup
Database setup
Firebase setup
Environment configuration
Basic CI/testing setup
Deliverable
Developer
   ↓
Clone repository
   ↓
Run frontend
   ↓
Run backend
   ↓
Connect database
   ↓
Authenticate with Firebase

At the end of this phase, every team member should be able to run the project locally.

3. Phase 1 — Authentication & User Foundation
Features
Firebase Authentication
Student registration
Employer registration
Login
Logout
Password reset
Firebase UID mapping
SkillBridge User creation
Role assignment
Roles
STUDENT
EMPLOYER
ADMIN

One account has exactly one role, as already decided.

Deliverable
Firebase Login
      ↓
Firebase UID
      ↓
Spring Boot
      ↓
SkillBridge User
      ↓
Role
4. Phase 2 — Student Profile

Now build the Master Profile.

Features
Profile
Education
Experience
Projects
Skills
Certifications
Achievements
Preferences
Goal

The student should be able to create a useful professional profile without any AI.

This is important because later systems depend on it.

Profile
   ↓
Resume
   ↓
Job Matching
   ↓
Applications
5. Phase 3 — Skills & Evidence Foundation

Now implement the foundation of SkillBridge's differentiator.

Features
Skill management
Project ↔ Skill relationships
Evidence records
Evidence statuses
Evidence source types
Student evidence review

Example:

Java
 ↓
Evidence
 ├── Project
 ├── GitHub
 └── Certification
Goal

A skill should be able to have supporting evidence, rather than existing only as a typed claim.

6. Phase 4 — GitHub Integration

Now connect GitHub.

Workflow
Student
   ↓
Connect GitHub
   ↓
Authorize
   ↓
Fetch repositories
   ↓
Normalize data
   ↓
Store repository information
   ↓
Student reviews imported projects/evidence
MVP GitHub data

Start with:

Repository name
Description
URL
Languages
README
Basic metadata
Timestamps/activity signals

Do not attempt to build every possible GitHub analysis feature immediately.

7. Phase 5 — Evidence Engine

Now combine the profile + projects + GitHub.

Profile
   +
Projects
   +
GitHub
   +
Certifications
   ↓
Evidence Engine
Initial responsibilities
Identify candidate evidence
Link evidence to skills/projects
Analyze evidence strength
Explain evidence
Allow candidate confirmation
Maintain evidence status
Important product rule

The engine should distinguish:

Self-reported
Supported
Verified
Unverified
Rejected

It must not turn an AI guess into verified fact automatically.

8. Phase 6 — Resume System

Now build the resume ecosystem.

Features
Create resume
Templates
Resume editor
Resume preview
Sections
Reordering
Multiple resumes
Resume versions
PDF export

Architecture:

Master Profile
      ↓
Resume
      ↓
Resume Version
9. Phase 7 — Resume Intelligence

Once the basic resume works, add:

Resume Truth Checker
Claim extraction
Evidence comparison
Missing evidence
Contradiction detection
Vague claim detection

Example:

Resume claim
     ↓
Evidence Engine
     ↓
Supported?
     │
 ┌───┴────┐
Yes       No
 │         │
Keep     Flag

Again, the system should not fabricate metrics.

10. Phase 8 — Job Platform

Now start building the employer/job side.

Employer foundation
Employer account
Company profile
Company membership
Job creation
Job editing
Job publishing
Job closing
Job requirements
Title
Description
Required skills
Preferred skills
Experience
Education
Location
Work mode
Employment type
Openings
Deadline
Custom questions
11. Phase 9 — Job Intelligence

Now analyze job descriptions.

Job Description
      ↓
Analysis
      ↓
Required Skills
Preferred Skills
Experience
Education
Responsibilities

This should be implemented as a separate backend intelligence service/API.

12. Phase 10 — Job Matching

Now connect the student to jobs.

Candidate
      +
Job
      +
Evidence
      ↓
Matching Engine

The output should explain:

Strong matches
Weak matches
Missing skills
Supporting evidence
Potential gaps

A numerical score can be included, but the explanation is more important than the number.

13. Phase 11 — Application System

Now implement:

Apply with SkillBridge

Flow:

Job
 ↓
Apply
 ↓
Choose Resume Version
 ↓
Load application information
 ↓
Answer custom questions
 ↓
Review
 ↓
Submit

Then application tracking:

Applied
 ↓
Screening
 ↓
Shortlisted
 ↓
Assessment
 ↓
Interview
 ↓
Selected
 ↓
Hired
14. Phase 12 — Employer Candidate Management

Now the recruiter can see candidates.

Employer
   ↓
Candidates
   ↓
Search
   ↓
Filters
   ↓
Candidate Profile

Candidate information shown must respect:

candidate visibility settings
employer authorization
privacy rules
15. Phase 13 — ATS

Implement the employer hiring pipeline.

Applications
      ↓
Screening
      ↓
Shortlisted
      ↓
Assessment
      ↓
Interview
      ↓
Selected
      ↓
Hired

Features:

status changes
notes
application history
interviews
candidate review
16. Phase 14 — Complete Core Loop

This is the most important milestone.

The team must be able to demonstrate:

Student
   ↓
Create Profile
   ↓
Connect GitHub
   ↓
Collect Evidence
   ↓
Create Resume
   ↓
Analyze Job
   ↓
See Match
   ↓
Apply
   ↓
Employer receives Application
   ↓
Employer Reviews Candidate
   ↓
Shortlists Candidate
   ↓
Moves Candidate through ATS

Once this works, you have a real SkillBridge MVP.

17. Phase 15 — Hardening

Now stop adding major features for a moment.

Focus on:

Security
Validation
Authorization
Error handling
Database constraints
Performance
Logging
Testing
Accessibility
Responsive UI

This phase often makes the difference between:

"It works on my laptop."

and:

"We have a functioning software product."

18. Phase 16 — Deployment

Deploy:

Frontend
Backend
Database

Set up:

Production environment
Environment variables
Firebase production configuration
GitHub OAuth configuration
HTTPS
Error monitoring/logging
Backups/retention strategy

Do not expose secrets in GitHub.

19. Phase 17 — Documentation & Demo

Prepare:

README
Setup guide
Architecture diagram
Database diagram
API documentation
User manual
Test report
Project report
Demo flow

For a college project, this matters because your implementation and evaluation need to be explainable.

20. MVP Boundary

This is something you should put directly into the repository.

MVP
Authentication
Student Profile
Education
Experience
Projects
Skills
Certifications
Achievements
GitHub Integration
Evidence Engine
Resume Builder
Resume Versions
Resume Truth Checker
Job Posting
Job Analysis
Job Matching
Apply with SkillBridge
Application Tracking
Employer Candidate Search
Candidate Profile
Shortlisting
ATS
Post-MVP
Interview Simulation
Career Gap → Project Generator
College Dashboard
Rankings
Hackathons
Career Events
Internships Marketplace
Scholarships
Sponsored Skill Programs
Advanced analytics

Do not allow post-MVP features to silently enter the current sprint.

21. Team Parallelization

You don't have to wait for every phase to finish before anyone works.

Once the contracts are defined:

                 Foundation
                     ↓
           ┌─────────┼─────────┐
           ↓         ↓         ↓
       Frontend    Backend   Database
           │         │         │
           └─────────┼─────────┘
                     ↓
                Integration

Later:

Student side                    Employer side
     │                               │
     ├── Profile                     ├── Company
     ├── Resume                      ├── Job
     └── Matching                    ├── Candidates
                                     └── ATS

Then both meet at:

Job
 ↓
Application
22. Five-Member Work Allocation
Member 1 — Frontend

Primary:

Student UI
Profile UI
Resume UI
Job UI
Application UI
Member 2 — Backend & Security

Primary:

Spring Boot
Firebase verification
Authorization
Profile APIs
Resume APIs
Application APIs
Member 3 — Database & Integrations

Primary:

PostgreSQL
Migrations
GitHub integration
External account system
Member 4 — Intelligence

Primary:

Evidence Engine
Job analysis
Matching
Resume analysis
Future interview intelligence
Member 5 — Employer & Quality

Primary:

Company
Employer
Jobs
Candidate search
ATS
Testing
Deployment support
23. Dependency Map

Your team should understand the dependencies:

Firebase
   ↓
User
   ↓
Profile
   ↓
Projects / Skills / Experience
   ↓
Evidence
   ↓
Resume
   ↓
Job Matching
   ↓
Application
   ↓
Employer
   ↓
ATS

So don't start ATS development before the core application model exists.

Similarly:

Evidence Engine
   ↓
depends on
Projects + Skills + External Data

Therefore you can't build the final Evidence Engine before those foundations exist.

24. Suggested Sprint Structure

A practical structure is one-week sprints, but the exact number of sprints depends on your available development time.

Sprint 0
Repository
Documentation
Architecture
Database foundation
Frontend/backend setup
Firebase
Sprint 1
Authentication
User
Role
Profile
Sprint 2
Education
Experience
Projects
Skills
Certifications
Achievements
Sprint 3
External Accounts
GitHub OAuth
GitHub synchronization
Sprint 4
Evidence Engine foundation
Evidence UI
Skill ↔ Evidence
Sprint 5
Resume Builder
Resume versions
PDF
Sprint 6
Resume Truth Checker
Job Description Analysis
Sprint 7
Employer
Company
Job creation
Job publishing
Sprint 8
Job Matching
Candidate search
Sprint 9
Apply with SkillBridge
Application tracking
Sprint 10
ATS
Shortlisting
Interviews
Sprint 11
End-to-end integration
Security
Bug fixing
Sprint 12
Testing
Deployment
Documentation
Demo preparation

These are planning units, not promises. Your actual dates should be set after you tell me your team's available hours and college deadline.

25. Milestones

You should track a few major milestones rather than 100 tiny goals.

M1 — Foundation Ready
All developers can run SkillBridge.
M2 — Student Platform Ready
Authentication + Master Profile working.
M3 — Evidence Ready
GitHub + Evidence Engine working.
M4 — Resume Ready
Multiple resumes + versioning + PDF working.
M5 — Job Intelligence Ready
Job analysis + matching working.
M6 — Hiring Ready
Apply + Employer + ATS working.
M7 — MVP Complete
Complete student → employer hiring loop.
M8 — Release Candidate
Testing + security + deployment + documentation complete.
26. MVP Freeze

This is a very important team-lead rule.

At some point, declare:

MVP FROZEN

After that:

New idea
   ↓
GitHub Issue
   ↓
Future Roadmap

not:

New idea
   ↓
Everyone immediately starts building it

This is how projects avoid scope explosion.

27. The Team Lead Dashboard

You should always be able to answer these six questions:

1. What are we building now?
2. Who owns each task?
3. What's blocked?
4. What is waiting for review?
5. What has been completed?
6. What is next?

If you can answer these from GitHub, you're managing the project correctly.

28. Recommended GitHub Milestone Structure

You can create GitHub milestones such as:

M1 Foundation
M2 Student Profile
M3 Evidence
M4 Resume
M5 Job Intelligence
M6 Applications
M7 Employer & ATS
M8 MVP Release

Then issues belong to milestones.

Example:

Issue #42
"Implement GitHub repository sync"

Milestone:
M3 Evidence

Assigned:
Member 3

Labels:
integration
backend
github
P1
