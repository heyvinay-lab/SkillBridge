SkillBridge — Software Requirements
1. Users

SkillBridge will have 3 primary roles:

A. Student / Candidate

Uses SkillBridge to:

Create a professional profile
Manage skills, education, projects, experience, certifications, etc.
Connect GitHub and other supported platforms
Build multiple resumes
Analyze resumes
Analyze job descriptions
Check job compatibility
Apply to jobs
Track applications
Prepare for interviews
B. Employer / Recruiter

Uses SkillBridge to:

Create company profile
Create job openings
Define required/preferred skills
Search candidates
View candidate profiles
Evaluate evidence
Shortlist candidates
Manage applications
Manage hiring pipeline
C. Admin

Responsible for:

User management
Employer/company management
Platform moderation
Managing reported content
Managing system-level configuration
Monitoring platform activity
2. Student Requirements
2.1 Authentication

The system shall allow students to:

Register
Login
Logout
Reset password
Manage account
Delete/deactivate account
2.2 Professional Profile

A student shall be able to maintain a Master Profile containing:

Personal
Name
Profile photo
Contact information
Location
Portfolio URL
Education
Institution
Degree
Department
Graduation year
CGPA/percentage
Skills
Programming languages
Frameworks
Libraries
Databases
Tools
Soft skills
Experience
Internships
Jobs
Organizations
Role
Duration
Responsibilities
Projects
Project name
Description
Technologies
GitHub URL
Live URL
Project role
Project dates
Additional
Certifications
Achievements
Hackathons
Awards
Publications
Languages
Coding profiles
3. External Profile Integration

SkillBridge should support connecting external platforms where APIs/access policies permit.

Initial priority:

GitHub

Potential future integrations:

GitLab
Bitbucket
LeetCode
CodeChef
Codeforces
HackerRank
Kaggle
LinkedIn

The system must not assume an API exists for a platform before implementing its integration.

4. GitHub Integration

The student should be able to connect GitHub.

SkillBridge should retrieve permitted repository information such as:

Repository name
Description
URL
Languages
README
Repository metadata
Activity information
Relevant project information

The system should use this information to help identify:

Projects
Skills
Technologies
Evidence

The student should be able to review imported information before it becomes part of their professional profile.

5. Evidence Engine

This is one of SkillBridge's core requirements.

The system should connect professional claims with supporting evidence.

Example:

Student claims: Java + Spring Boot

SkillBridge may find:

GitHub project
      ↓
Java code
      ↓
Spring Boot dependencies
      ↓
Project description

The system can then show supporting evidence.

Evidence status

Use clear states such as:

Verified
Supported
Self-Reported
Unverified

The platform should never fabricate evidence.

For example:

"Improved performance by 40%"

should remain unverified unless the student provides supporting information.

6. Resume Management

Students should be able to create multiple resumes from their Master Profile.

Examples:

Software Engineer Resume
Java Backend Resume
Frontend Resume
Data Analyst Resume
Internship Resume
Company-specific Resume
6.1 Resume Editor

The system should support:

Live editing
Add/remove sections
Reorder sections
Edit content
Formatting
Templates
Preview
Autosave
Resume versions
PDF generation
7. Job-Specific Resume

The student should be able to provide a job description.

SkillBridge should analyze:

Job Description
      ↓
Required Skills
Preferred Skills
Experience
Responsibilities
Education

Then recommend relevant information from the student's existing profile.

Important rule:

SkillBridge may tailor existing information, but must not invent experience, skills, projects, achievements, or metrics.

8. Resume Truth Checker

The system should identify:

Unsupported claims
Vague statements
Missing evidence
Contradictory information
Inconsistent dates
Excessive buzzwords
Missing measurable results

Example:

"Developed a highly scalable application."

SkillBridge can ask:

What makes it scalable?
What technology did you use?
Do you have measurable results?

9. Job Intelligence

A student should be able to submit a Job Description.

SkillBridge should extract:

Job Title
Required Skills
Preferred Skills
Experience
Education
Responsibilities
Location
Work Mode
Other Requirements

It should also identify likely areas the candidate may need to prepare for.

10. Job Matching

SkillBridge should compare:

Candidate
      +
Job
      ↓
Match Analysis

The result should explain why the candidate matches.

For example:

Java                 ✓ Strong
Spring Boot          ✓ Strong
PostgreSQL            ✓
Docker                △ Limited evidence
AWS                   ✗ Missing

Instead of only saying:

78% match

the system should explain the match.

11. Career Gap Analysis

The system should identify gaps between:

Current Skills
      ↓
Target Job

Example:

Target: Java Backend Developer

Strong:
✓ Java
✓ Spring Boot
✓ PostgreSQL

Gaps:
△ Docker
✗ AWS
✗ Microservices
12. Project Recommendation / Generation

Based on skill gaps, SkillBridge can recommend projects that help the student gain practical experience.

Example:

Missing:
Microservices

Recommended project:
Build an e-commerce microservices backend

Skills gained:
Java
Spring Boot
Docker
REST APIs
Microservices
PostgreSQL

This should initially be treated as a recommendation system, not an automatic claim that the student possesses those skills.

13. Interview Intelligence

SkillBridge should generate interview preparation based on:

Candidate Profile
+
Resume
+
Job Description
+
Projects

Possible categories:

HR questions
Technical questions
Project questions
Role-specific questions
Resume questions
14. Interview Simulation

Future/advanced functionality can provide:

Question
   ↓
Candidate Answer
   ↓
Analysis
   ↓
Feedback

Feedback can include:

Technical correctness
Relevance
Communication
Missing points
Weak areas
15. Application Management

Students should have an application tracker:

Saved
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

Each application can store:

Company
Job
Date applied
Resume used
Job description
Application status
Notes
Interview information
16. Apply with SkillBridge

For participating employers, students should be able to:

Click Apply
      ↓
Use SkillBridge Profile
      ↓
Select Resume
      ↓
Answer Custom Questions
      ↓
Review
      ↓
Submit

The goal is to avoid repeatedly entering the same information.

17. Employer Requirements

Employers should be able to create:

Company Profile
Company name
Description
Website
Industry
Location
Company information
Job
Job title
Description
Required skills
Preferred skills
Experience
Education
Location
Remote/hybrid/on-site
Salary, where applicable
Number of openings
Deadline
Custom application questions
18. Candidate Search

Employers should be able to search/filter candidates based on permitted profile information such as:

Skills
Experience
Projects
Education
Certifications
Graduation year
Location
Availability
Evidence
19. Candidate Profile

An employer should be able to view an appropriate candidate profile containing:

Profile
Skills
Projects
Experience
Education
Certifications
Evidence
Resume
External profiles

Privacy settings must control what is visible.

20. Employer Hiring Pipeline

Employers should have a basic ATS workflow:

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

Recruiters should be able to:

Shortlist
Reject
Add notes
Move candidates between stages
View application details
21. Admin Requirements

Admin should be able to:

Manage users
Manage employers
Review reported content
Manage companies
Monitor system activity
Handle platform-level moderation
Manage roles/permissions
22. Security Requirements

SkillBridge must have:

Secure authentication
Role-based access control
Protected APIs
Password security
Input validation
Secure environment variables
No API keys committed to Git
Proper database authorization
Secure file access
User-controlled profile visibility
23. Privacy Requirements

The user should control the visibility of sensitive information.

For example:

GitHub → Connected
GitHub → Private repositories → Never automatically exposed

Similarly:

Personal contact details
Resumes
Employment information
Application information

should not automatically become public.

24. AI Requirements

AI should be used where it adds value:

✓ Job description analysis
✓ Skill extraction
✓ Resume analysis
✓ Claim analysis
✓ Evidence interpretation
✓ Semantic job matching
✓ Interview question generation
✓ Interview feedback

Traditional software logic should control:

✓ Authentication
✓ Permissions
✓ Database operations
✓ Application status
✓ Resume versions
✓ Employer workflow
✓ Deterministic validation

AI should assist the system, not become the system.

25. Non-Functional Requirements

SkillBridge should be:

Reliable

Core workflows should not depend entirely on an AI API being available.

Maintainable

Code should be modular and documented.

Scalable

The architecture should allow future:

Colleges
Hackathons
Internships
Career events
Sponsored programs
Responsive

The web application should work properly on:

Desktop
Tablet
Mobile
Explainable

Important outputs such as matching and evidence should explain why they were produced.

26. MVP — What we actually build first

This is important.

Don't build the entire vision for your college project.

MVP
AUTH
 │
 ├── Student
 └── Employer

STUDENT
 │
 ├── Master Profile
 ├── Projects
 ├── Skills
 ├── GitHub Integration
 ├── Evidence Engine
 ├── Resume Builder
 ├── Resume Truth Checker
 ├── Job Description Analysis
 ├── Job Matching
 ├── Apply with SkillBridge
 └── Application Tracking

EMPLOYER
 │
 ├── Company Profile
 ├── Job Creation
 ├── Candidate Search
 ├── Candidate Profile
 ├── Shortlisting
 └── Basic ATS
Later
College Dashboard
Rankings
Interview Simulation
Career Gap → Project Generator
Hackathons
Internships
Scholarships
Career Events
Sponsored Skill Programs
Advanced analytics
27. The complete core loop

Ultimately, SkillBridge should demonstrate this:

              STUDENT
                 │
                 ▼
          Master Profile
                 │
        ┌────────┴────────┐
        ▼                 ▼
     GitHub            Projects
        │                 │
        └────────┬────────┘
                 ▼
           Evidence Engine
                 │
                 ▼
            Skills/Evidence
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
