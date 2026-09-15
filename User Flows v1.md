SkillBridge — User Flows v1

The rule is:

Every major feature must have a clear Start → Actions → Result.

1. Student — First-time setup
Landing Page
     ↓
Sign Up
     ↓
Select "Student"
     ↓
Create Account
     ↓
Basic Profile
     ↓
Education
     ↓
Skills
     ↓
Projects
     ↓
Connect GitHub
     ↓
Review Imported Data
     ↓
Dashboard
Result

The student has a basic professional profile ready to use.

2. Student — Master Profile
Dashboard
    ↓
Profile
    ↓
Edit Information
    ↓
Save
    ↓
Master Profile Updated

The Master Profile is the single source of truth.

For example:

Master Profile
├── Education
├── Skills
├── Experience
├── Projects
├── Certifications
├── Achievements
└── External Profiles

Everything else can use this information.

3. GitHub Integration
Profile
   ↓
Connect GitHub
   ↓
GitHub Authorization
   ↓
Fetch Repositories
   ↓
Analyze Repository Data
   ↓
Show Imported Projects
   ↓
Student Reviews
   ↓
Approve / Edit / Ignore
   ↓
Save
Important

Don't automatically publish everything GitHub finds.

The student should have control.

4. Evidence Flow

This is one of the most important SkillBridge flows.

Profile / Project
       ↓
Claim or Skill
       ↓
Find Supporting Evidence
       ↓
Evidence Analysis
       ↓
Evidence Status
       ↓
Student Review
       ↓
Save Evidence

Example:

Skill: Java

Evidence:
├── GitHub Project ✓
├── Java source code ✓
├── Certification ✓
└── Internship experience ✓

The system should show why evidence supports a claim.

5. Resume Creation
Dashboard
    ↓
Resumes
    ↓
Create Resume
    ↓
Choose Template
    ↓
Choose Profile Data
    ↓
Generate Draft
    ↓
Open Editor
    ↓
Edit
    ↓
Preview
    ↓
Save Version
    ↓
Export PDF
6. Multiple Resume Flow

A student could have:

My Resumes
│
├── Java Backend Resume
├── Software Engineer Resume
├── Frontend Resume
└── Internship Resume

Each resume can be based on the same Master Profile.

                    Master Profile
                         │
            ┌────────────┼────────────┐
            ↓            ↓            ↓
        Backend        Frontend    Internship
         Resume         Resume       Resume
7. Resume Truth Checker
Resume
   ↓
Analyze
   ↓
Find Claims
   ↓
Check Supporting Evidence
   ↓
Find Problems
   ↓
Show Report

Example:

Claim:
"Improved API performance by 40%"

Status:
⚠ Evidence missing

Suggestion:
Add benchmark/report/measurement if available.

The system should not invent the 40% figure.

8. Job Analysis
Job Page
    ↓
Paste Job Description
    ↓
Analyze
    ↓
Extract Requirements
    ↓
Display:
├── Required Skills
├── Preferred Skills
├── Experience
├── Education
└── Responsibilities
9. Job Matching
Job Description
       +
Student Profile
       +
Evidence
       ↓
Match Engine
       ↓
Match Analysis

Example:

Java             Strong
Spring Boot      Strong
PostgreSQL       Strong
Docker           Limited evidence
AWS              Missing

Then:

Match Summary
      ↓
Strengths
      ↓
Gaps
      ↓
Recommended Improvements
10. Apply with SkillBridge

This is another important workflow.

Job
 ↓
Apply with SkillBridge
 ↓
Choose Resume
 ↓
Profile information loaded
 ↓
Custom questions
 ↓
Student reviews everything
 ↓
Submit
 ↓
Application created

The student should always have a final review step before submission.

11. Application Tracking

After applying:

Application
     ↓
Applied
     ↓
Shortlisted
     ↓
Assessment
     ↓
Interview
     ↓
Offer
     ↓
Hired

Or:

Rejected

The student dashboard should show where every application currently stands.

12. Employer — First-time setup
Landing Page
     ↓
Sign Up
     ↓
Select "Employer"
     ↓
Company Information
     ↓
Company Profile
     ↓
Employer Dashboard
13. Employer — Create Job
Employer Dashboard
       ↓
Create Job
       ↓
Job Details
       ↓
Requirements
       ↓
Skills
       ↓
Custom Questions
       ↓
Preview
       ↓
Publish
14. Employer — Find Candidates
Employer Dashboard
       ↓
Candidates
       ↓
Search / Filters
       ↓
Candidate List
       ↓
Open Candidate
       ↓
View:
├── Skills
├── Projects
├── Experience
├── Education
├── Evidence
└── Resume
15. Employer — Hiring Pipeline
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

The recruiter can move candidates between stages.

16. Admin Flow
Admin Login
    ↓
Admin Dashboard
    ├── Users
    ├── Employers
    ├── Companies
    ├── Reports
    └── Platform Activity

Admin should have elevated permissions, but shouldn't automatically have access to private candidate information unless required for a legitimate administrative function.

17. The complete SkillBridge journey

This is the most important flow for your team to understand:

                         STUDENT
                            │
                            ▼
                     Create Account
                            │
                            ▼
                      Master Profile
                            │
                ┌───────────┴───────────┐
                ▼                       ▼
             GitHub                  Projects
                │                       │
                └───────────┬───────────┘
                            ▼
                      Evidence Engine
                            │
                            ▼
                       Skills + Proof
                            │
                            ▼
                         Resume
                            │
                            ▼
                       Job Analysis
                            │
                            ▼
                       Job Matching
                            │
                            ▼
                  Apply with SkillBridge
                            │
                            ▼
                         EMPLOYER
                            │
                            ▼
                    Candidate Review
                            │
                            ▼
                        Shortlist
                            │
                            ▼
                        Interview
                            │
                            ▼
                          Hired

That's the core SkillBridge product loop.
