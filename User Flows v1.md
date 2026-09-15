# SkillBridge — User Flows v1

**Rule:** Every major feature must have a clear Start → Actions → Result.

---

## Table of contents
1. [Student — First-time setup](#student--first-time-setup)  
2. [Student — Master Profile](#student--master-profile)  
3. [GitHub Integration](#github-integration)  
4. [Evidence Flow](#evidence-flow)  
5. [Resume Creation](#resume-creation)  
6. [Multiple Resume Flow](#multiple-resume-flow)  
7. [Resume Truth Checker](#resume-truth-checker)  
8. [Job Analysis](#job-analysis)  
9. [Job Matching](#job-matching)  
10. [Apply with SkillBridge](#apply-with-skillbridge)  
11. [Application Tracking](#application-tracking)  
12. [Employer — First-time setup](#employer--first-time-setup)  
13. [Employer — Create Job](#employer--create-job)  
14. [Employer — Find Candidates](#employer--find-candidates)  
15. [Employer — Hiring Pipeline](#employer--hiring-pipeline)  
16. [Admin Flow](#admin-flow)  
17. [The complete SkillBridge journey](#the-complete-skillbridge-journey)

---

## 1. Student — First-time setup
Flow:
Landing Page → Sign Up → Select "Student" → Create Account → Basic Profile → Education → Skills → Projects → Connect GitHub → Review Imported Data → Dashboard

Result:
- The student has a basic professional profile ready to use.

---

## 2. Student — Master Profile
Flow:
Dashboard → Profile → Edit Information → Save → Master Profile Updated

Notes:
- The Master Profile is the single source of truth. Other features should read from this profile.
- Master Profile example structure:
```
Master Profile
├── Education
├── Skills
├── Experience
├── Projects
├── Certifications
├── Achievements
└── External Profiles
```
- Everything else in the system can reference this Master Profile.

---

## 3. GitHub Integration
Flow:
Profile → Connect GitHub → GitHub Authorization → Fetch Repositories → Analyze Repository Data → Show Imported Projects → Student Reviews → Approve / Edit / Ignore → Save

Important:
- Do not automatically publish everything GitHub finds.
- The student must control what gets imported/published.

---

## 4. Evidence Flow
This is one of the most important flows.

Flow:
Profile / Project → Claim or Skill → Find Supporting Evidence → Evidence Analysis → Evidence Status → Student Review → Save Evidence

Example:
Skill: Java
Evidence:
```
├── GitHub Project ✓
├── Java source code ✓
├── Certification ✓
└── Internship experience ✓
```
- The system should show why each piece of evidence supports a claim (explain the rationale).

---

## 5. Resume Creation
Flow:
Dashboard → Resumes → Create Resume → Choose Template → Choose Profile Data → Generate Draft → Open Editor → Edit → Preview → Save Version → Export PDF

---

## 6. Multiple Resume Flow
Concept:
A student can maintain multiple resumes tailored to different roles. Each resume can be based on the same Master Profile.

Illustration:
```
                 Master Profile
                      │
         ┌────────────┼────────────┐
         ↓            ↓            ↓
     Backend        Frontend    Internship
      Resume         Resume       Resume
```

---

## 7. Resume Truth Checker
Flow:
Resume → Analyze → Find Claims → Check Supporting Evidence → Find Problems → Show Report

Example:
Claim: "Improved API performance by 40%"  
Status: ⚠ Evidence missing  
Suggestion: Add benchmark/report/measurement if available.

Important:
- The system should not invent or fabricate claims or metrics.

---

## 8. Job Analysis
Flow:
Job Page → Paste Job Description → Analyze → Extract Requirements → Display:
- Required Skills
- Preferred Skills
- Experience
- Education
- Responsibilities

---

## 9. Job Matching
Flow:
Job Description + Student Profile + Evidence → Match Engine → Match Analysis

Example match output:
- Java — Strong  
- Spring Boot — Strong  
- PostgreSQL — Strong  
- Docker — Limited evidence  
- AWS — Missing

Then produce:
- Match Summary
- Strengths
- Gaps
- Recommended Improvements

---

## 10. Apply with SkillBridge
Flow:
Job → Apply with SkillBridge → Choose Resume → Profile information loaded → Custom questions → Student reviews everything → Submit → Application created

Important:
- The student should always have a final review step before submission.

---

## 11. Application Tracking
After applying, an application can move through:
Applied → Shortlisted → Assessment → Interview → Offer → Hired  
Or: Rejected

- The student dashboard should clearly show where each application currently stands.

---

## 12. Employer — First-time setup
Flow:
Landing Page → Sign Up → Select "Employer" → Company Information → Company Profile → Employer Dashboard

---

## 13. Employer — Create Job
Flow:
Employer Dashboard → Create Job → Job Details → Requirements → Skills → Custom Questions → Preview → Publish

---

## 14. Employer — Find Candidates
Flow:
Employer Dashboard → Candidates → Search / Filters → Candidate List → Open Candidate → View:
- Skills
- Projects
- Experience
- Education
- Evidence
- Resume

---

## 15. Employer — Hiring Pipeline
Flow:
Applications → Screening → Shortlisted → Assessment → Interview → Selected → Hired

- Recruiters can move candidates between stages.

---

## 16. Admin Flow
Flow:
Admin Login → Admin Dashboard (Users, Employers, Companies, Reports, Platform Activity)

Notes:
- Admins should have elevated permissions but should not automatically access private candidate information unless required for legitimate administrative functions.

---

## 17. The complete SkillBridge journey
High-level flow:
```
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
```

This is the core SkillBridge product loop.

---

Notes on formatting changes
- Added a Table of Contents for quick navigation.
- Converted freeform lists into consistent "Flow" lines and "Notes/Important" blocks.
- Grouped examples and tree-like structures into code blocks for clarity.
- Standardized arrow notation (→) for flows and a compact ASCII diagram for larger flows.
