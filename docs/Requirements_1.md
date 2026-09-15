# SkillBridge — Software Requirements

## Table of contents
1. [Users & Roles](#users--roles)  
2. [Student Requirements](#student-requirements)  
3. [External Profile Integration](#external-profile-integration)  
4. [GitHub Integration](#github-integration)  
5. [Evidence Engine](#evidence-engine)  
6. [Resume Management](#resume-management)  
7. [Job-Specific Resume](#job-specific-resume)  
8. [Resume Truth Checker](#resume-truth-checker)  
9. [Job Intelligence](#job-intelligence)  
10. [Job Matching](#job-matching)  
11. [Career Gap Analysis](#career-gap-analysis)  
12. [Project Recommendation / Generation](#project-recommendation--generation)  
13. [Interview Intelligence](#interview-intelligence)  
14. [Interview Simulation](#interview-simulation)  
15. [Application Management](#application-management)  
16. [Apply with SkillBridge](#apply-with-skillbridge)  
17. [Employer Requirements](#employer-requirements)  
18. [Candidate Search](#candidate-search)  
19. [Candidate Profile](#candidate-profile)  
20. [Employer Hiring Pipeline](#employer-hiring-pipeline)  
21. [Admin Requirements](#admin-requirements)  
22. [Security Requirements](#security-requirements)  
23. [Privacy Requirements](#privacy-requirements)  
24. [AI Requirements](#ai-requirements)  
25. [Non-Functional Requirements](#non-functional-requirements)  
26. [MVP — Scope for First Delivery](#mvp--scope-for-first-delivery)  
27. [Core Product Loop](#core-product-loop)

---

## 1. Users & Roles
SkillBridge will have three primary roles:

- Student / Candidate
- Employer / Recruiter
- Admin

### A. Student / Candidate
Capabilities:
- Create and manage a professional (master) profile
- Manage skills, education, projects, experience, certifications
- Connect GitHub and other supported platforms
- Build multiple resumes, analyze them
- Analyze job descriptions and check compatibility
- Apply to jobs and track applications
- Prepare for interviews

### B. Employer / Recruiter
Capabilities:
- Create company profiles and job openings
- Define required/preferred skills
- Search and evaluate candidates
- View candidate profiles and evidence
- Shortlist candidates and manage hiring pipeline

### C. Admin
Responsibilities:
- User and employer management
- Platform moderation and handling reported content
- System-level configuration and activity monitoring

---

## 2. Student Requirements

### 2.1 Authentication
The system shall allow students to:
- Register, Login, Logout
- Reset password
- Manage account
- Delete/deactivate account

### 2.2 Professional Profile
A student shall be able to maintain a Master Profile containing:

- Personal
  - Name, Profile photo, Contact information, Location, Portfolio URL
- Education
  - Institution, Degree, Department, Graduation year, CGPA/percentage
- Skills
  - Programming languages, Frameworks, Libraries, Databases, Tools, Soft skills
- Experience
  - Internships, Jobs, Organizations, Role, Duration, Responsibilities
- Projects
  - Project name, Description, Technologies, GitHub URL, Live URL, Role, Dates
- Additional
  - Certifications, Achievements, Hackathons, Awards, Publications, Languages, Coding profiles

---

## 3. External Profile Integration
Support for connecting external platforms where APIs/access policies permit.

Initial priority:
- GitHub

Potential future integrations:
- GitLab, Bitbucket, LeetCode, CodeChef, Codeforces, HackerRank, Kaggle, LinkedIn

Important: Do not assume an API exists for a platform before implementing its integration.

---

## 4. GitHub Integration
Students should be able to connect GitHub and import permitted repository information such as:
- Repository name, Description, URL, Languages, README, Repository metadata, Activity

Use this data to identify:
- Projects, Skills, Technologies, Evidence

Students must be able to review imported information before it becomes part of their professional profile.

---

## 5. Evidence Engine
Core requirement: connect professional claims with supporting evidence.

Example flow:
Student claims: Java + Spring Boot
Possible evidence:
- GitHub project -> Java code -> Spring Boot dependencies -> Project description

Evidence states (use clear states):
- Verified, Supported, Self-Reported, Unverified

Important: The platform must never fabricate evidence or claims. Example: "Improved performance by 40%" remains unverified unless supporting information is provided.

---

## 6. Resume Management
Students can create multiple resumes derived from their Master Profile:
- Examples: Software Engineer, Java Backend, Frontend, Data Analyst, Internship, Company-specific

### 6.1 Resume Editor
The system should provide:
- Live editing, Add/remove/reorder sections, Formatting, Templates, Preview
- Autosave, Resume versions, PDF generation

---

## 7. Job-Specific Resume
Students can provide a job description; SkillBridge should:
- Extract required/preferred skills, experience, responsibilities, and education
- Recommend relevant profile information to include in a tailored resume

Important: SkillBridge may tailor wording but must not invent experience, skills, projects, achievements, or metrics.

---

## 8. Resume Truth Checker
The system should identify:
- Unsupported claims, Vague statements, Missing evidence, Contradictory information
- Inconsistent dates, Excessive buzzwords, Missing measurable results

Example interaction:
- Claim: "Developed a highly scalable application."
- Follow-up: "What makes it scalable? Which technologies? Any measurable results?"

---

## 9. Job Intelligence
From a submitted Job Description, SkillBridge should extract:
- Job Title, Required Skills, Preferred Skills, Experience, Education, Responsibilities, Location, Work Mode, Other Requirements

It should also surface likely areas the candidate needs to prepare for.

---

## 10. Job Matching
Compare candidate profile vs job to produce a Match Analysis that explains reasons for each match level.

Example:
- Java ✓ Strong  
- Spring Boot ✓ Strong  
- PostgreSQL ✓  
- Docker △ Limited evidence  
- AWS ✗ Missing

Avoid only presenting a single percentage; explain the match details.

---

## 11. Career Gap Analysis
Identify gaps between current skills and the target job.

Example:
Target: Java Backend Developer
- Strong: Java, Spring Boot, PostgreSQL
- Gaps: Docker (limited), AWS (missing), Microservices (missing)

---

## 12. Project Recommendation / Generation
Recommend projects to address skill gaps (recommendation system only).

Example:
- Missing: Microservices  
- Recommended project: Build an e-commerce microservices backend  
- Skills gained: Java, Spring Boot, Docker, REST APIs, Microservices, PostgreSQL

Do not automatically claim the student possesses those skills; treat as suggested learning activities.

---

## 13. Interview Intelligence
Generate interview preparation based on:
- Candidate Profile + Resume + Job Description + Projects

Possible categories:
- HR questions, Technical questions, Project questions, Role-specific questions, Resume questions

---

## 14. Interview Simulation
Future/advanced functionality can provide simulated Q&A:
- Question → Candidate Answer → Analysis → Feedback

Feedback may include:
- Technical correctness, Relevance, Communication, Missing points, Weak areas

---

## 15. Application Management
Students should have an application tracker lifecycle:
Saved → Applied → Shortlisted → Assessment → Interview → Offer → Hired

Each application stores:
- Company, Job, Date applied, Resume used, Job description, Application status, Notes, Interview information

---

## 16. Apply with SkillBridge
For participating employers, students should be able to:
- Click Apply → Use SkillBridge Profile → Select Resume → Answer Custom Questions → Review → Submit

Goal: Avoid re-entering the same information repeatedly.

---

## 17. Employer Requirements
Employers should be able to create:
- Company Profile (name, description, website, industry, location)
- Jobs (title, description, required/preferred skills, experience, education, location, work mode, salary, number of openings, deadline, custom questions)

---

## 18. Candidate Search
Employers should be able to search/filter candidates using:
- Skills, Experience, Projects, Education, Certifications, Graduation year, Location, Availability, Evidence

---

## 19. Candidate Profile
An employer view should include:
- Profile summary, Skills, Projects, Experience, Education, Certifications, Evidence, Resume, External profiles

Privacy settings must control visibility.

---

## 20. Employer Hiring Pipeline
Basic ATS workflow:
Applications → Screening → Shortlisted → Assessment → Interview → Selected → Hired

Recruiter actions:
- Shortlist, Reject, Add notes, Move candidates between stages, View application details

---

## 21. Admin Requirements
Admins should be able to:
- Manage users, employers, companies
- Review reported content and moderate
- Monitor system activity
- Manage roles and permissions

---

## 22. Security Requirements
SkillBridge must ensure:
- Secure authentication, Role-based access control, Protected APIs
- Password security, Input validation, Secure environment variables
- No API keys committed to Git, Proper DB authorization, Secure file access
- User-controlled profile visibility

---

## 23. Privacy Requirements
Users control visibility of sensitive information:
- GitHub connections and private repositories must never be auto-exposed
- Personal contact details, Resumes, Employment/application info should not be public by default

---

## 24. AI Requirements
Use AI where it adds value:
- Job description analysis, Skill extraction, Resume analysis, Claim analysis, Evidence interpretation, Semantic job matching, Interview Q/A generation, Interview feedback

Use deterministic logic for:
- Authentication, Permissions, DB operations, Application status, Resume versions, Employer workflows

AI should assist — not replace — core system logic.

---

## 25. Non-Functional Requirements
The platform should be:
- Reliable: core workflows should not depend entirely on AI availability
- Maintainable: modular, documented code
- Scalable: allow future extensions (colleges, hackathons, internships, events)
- Responsive: web app works on desktop, tablet, mobile
- Explainable: matching and evidence outputs should explain why they were produced

---

## 26. MVP — Scope for First Delivery
Focus on a manageable MVP; do not build the entire vision at once.

MVP Components:
- AUTH (Student, Employer)
- STUDENT:
  - Master Profile, Projects, Skills, GitHub Integration, Evidence Engine
  - Resume Builder, Resume Truth Checker, Job Description Analysis, Job Matching
  - Apply with SkillBridge, Application Tracking
- EMPLOYER:
  - Company Profile, Job Creation, Candidate Search, Candidate Profile, Shortlisting, Basic ATS

Later phases may include:
- College Dashboard, Rankings, Interview Simulation, Gap → Project Generator, Hackathons, Internships, Scholarships, Career Events, Sponsored Programs, Advanced analytics

---

## 27. The complete core loop
Visual flow of how the product should work:

STUDENT
→ Master Profile
  ├─ GitHub
  └─ Projects
→ Evidence Engine
→ Skills / Evidence
→ Resume
→ Job Analysis
→ Job Matching
→ Apply with SkillBridge
→ EMPLOYER
→ Candidate Review → Shortlist → Interview → Hired

---
