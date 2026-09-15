1. Purpose of the API

The SkillBridge REST API provides controlled access to:

User accounts
Student profiles
Education
Experience
Skills
Projects
Certifications
Achievements
External accounts
GitHub data
Evidence
Resumes
Job analysis
Job matching
Applications
Companies
Employer accounts
Jobs
Candidate search
ATS workflow
Interviews
Administrative operations

The frontend must not directly access PostgreSQL.

Frontend
   │
   │ HTTPS
   ▼
Spring Boot REST API
   │
   ├── Authorization
   ├── Validation
   ├── Business Logic
   └── Data Access
          │
          ▼
      PostgreSQL
2. Base URL

Development:

http://localhost:8080/api/v1

Production:

https://api.<skillbridge-domain>/api/v1

The actual production domain will be decided later.

Every endpoint must be versioned under:

/api/v1

Example:

GET /api/v1/profile/me
3. Authentication

Firebase Authentication is responsible for authenticating users.

The frontend obtains a Firebase ID token after login.

Every protected API request sends:

Authorization: Bearer <FIREBASE_ID_TOKEN>

Example:

GET /api/v1/profile/me
Authorization: Bearer eyJhbGciOi...
4. Authentication Flow
                 User
                  │
                  ▼
            SkillBridge UI
                  │
                  ▼
          Firebase Authentication
                  │
                  ▼
          Firebase ID Token
                  │
                  ▼
        Authorization: Bearer ...
                  │
                  ▼
           Spring Boot API
                  │
          Verify Firebase token
                  │
                  ▼
            Extract Firebase UID
                  │
                  ▼
          Find SkillBridge User
                  │
                  ▼
          Check user's ONE role
                  │
                  ▼
              API logic
5. Critical Authentication Rule

The frontend must never decide authentication or authorization.

Bad:

Frontend says:
role = ADMIN

Backend:
"Okay, you're admin."

Correct:

Firebase token
      ↓
Spring Boot verifies token
      ↓
Firebase UID
      ↓
Database user
      ↓
Database role
      ↓
Authorization decision
6. One User = One Role

This is now locked.

A SkillBridge user has exactly one application role:

STUDENT
EMPLOYER
ADMIN

Database rule:

users
   ↓
one role

There will be no:

STUDENT + EMPLOYER

for the same account in the MVP.

If someone needs two identities, they should use separate accounts unless we later deliberately change this product rule.

7. Public vs Protected APIs

Every endpoint must explicitly define whether authentication is required.

Public

Examples:

GET /api/v1/jobs
GET /api/v1/jobs/{jobId}

Only if we decide public job discovery is enabled.

Protected

Examples:

GET /api/v1/profile/me
POST /api/v1/projects
POST /api/v1/applications
Role-protected

Example:

POST /api/v1/employer/jobs

Only:

EMPLOYER

can access it.

8. Standard HTTP Headers

Typical request:

Content-Type: application/json
Authorization: Bearer <firebase-id-token>

For requests containing files:

Content-Type: multipart/form-data
Authorization: Bearer <firebase-id-token>

The backend must validate content types rather than assuming the client is trustworthy.

9. Standard Success Response

We have locked:

{
  "success": true,
  "data": {},
  "message": "Profile retrieved successfully"
}
10. Standard Error Response
{
  "success": false,
  "error": {
    "code": "PROFILE_NOT_FOUND",
    "message": "Profile not found"
  }
}

For validation:

{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "One or more fields are invalid",
    "details": {
      "name": "Name is required",
      "email": "Invalid email format"
    }
  }
}

The exact shape of details must remain consistent across the API.

11. HTTP Status Codes

Use standard meanings.

Status	Meaning
200	Successful request
201	Resource created
204	Successful request with no response body
400	Invalid request
401	Missing/invalid authentication
403	Authenticated but not permitted
404	Resource not found
409	Conflict
422	Semantically invalid input, if needed
429	Rate limit exceeded
500	Unexpected server error
502/503	Upstream/external service unavailable

Don't use 200 for everything.

12. Resource Ownership

This is critical for security.

A student must only modify their own resources.

For example:

PUT /api/v1/projects/{projectId}

Spring Boot must verify:

Project
   ↓
Profile
   ↓
User
   ↓
Authenticated Firebase UID

If the project belongs to another user:

403 FORBIDDEN

not successful.

13. Authentication APIs

Even though Firebase handles authentication, SkillBridge still needs application-level user initialization.

POST /api/v1/users/me

Purpose:

Create/synchronize the SkillBridge user record after Firebase authentication.

Authentication:

Required

Role:

Authenticated user

Possible request:

{
  "role": "STUDENT"
}

But the backend must validate/lock the allowed initial role and should not allow an existing user to arbitrarily change it.

Response
{
  "success": true,
  "data": {
    "id": "uuid",
    "firebaseUid": "firebase-uid",
    "email": "student@example.com",
    "role": "STUDENT"
  },
  "message": "User initialized successfully"
}
14. Current User
GET /api/v1/users/me

Returns the authenticated user's SkillBridge account.

GET /api/v1/users/me

Response:

{
  "success": true,
  "data": {
    "id": "uuid",
    "firebaseUid": "firebase-uid",
    "email": "student@example.com",
    "role": "STUDENT",
    "status": "ACTIVE"
  },
  "message": "User retrieved successfully"
}

The API should not expose unnecessary Firebase internals.

15. Profile APIs
Get current profile
GET /api/v1/profile/me

Role:

STUDENT

Response contains the appropriate profile information.

Create profile
POST /api/v1/profile

Request:

{
  "firstName": "Vinay",
  "lastName": "Kumar",
  "headline": "Computer Science Student",
  "summary": "..."
}

Validation:

firstName required
reasonable length constraints
reject malformed URLs
sanitize text where appropriate

Response:

201 Created
Update profile
PUT /api/v1/profile/me

Only the authenticated student's profile may be updated.

Delete/deactivate profile

We should prefer account deactivation / soft deletion over destructive deletion where historical applications need to remain.

DELETE /api/v1/profile/me

The implementation must follow the platform's retention/privacy policy.

16. Education APIs
GET    /api/v1/profile/me/educations
POST   /api/v1/profile/me/educations
GET    /api/v1/profile/me/educations/{educationId}
PUT    /api/v1/profile/me/educations/{educationId}
DELETE /api/v1/profile/me/educations/{educationId}

Ownership is always verified.

Example:

{
  "institutionName": "Budge Budge Institute of Technology",
  "degree": "B.Tech",
  "fieldOfStudy": "Computer Science and Engineering",
  "startDate": "2023-08-01",
  "endDate": "2027-06-30",
  "gradeType": "CGPA",
  "gradeValue": "8.2"
}
17. Experience APIs
GET    /api/v1/profile/me/experiences
POST   /api/v1/profile/me/experiences
GET    /api/v1/profile/me/experiences/{experienceId}
PUT    /api/v1/profile/me/experiences/{experienceId}
DELETE /api/v1/profile/me/experiences/{experienceId}

Validation:

startDate <= endDate

when an end date exists.

For current experience:

isCurrent = true
endDate = null
18. Project APIs
GET    /api/v1/profile/me/projects
POST   /api/v1/profile/me/projects
GET    /api/v1/profile/me/projects/{projectId}
PUT    /api/v1/profile/me/projects/{projectId}
DELETE /api/v1/profile/me/projects/{projectId}

Example:

{
  "name": "SkillBridge",
  "description": "Evidence-based career intelligence platform",
  "liveUrl": "https://...",
  "githubUrl": "https://github.com/..."
}

Skills attached to a project should be managed through a controlled skills API rather than accepting arbitrary duplicated skill names.

19. Skill APIs
Search skills
GET /api/v1/skills?query=java

Possible response:

{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Java",
      "category": "PROGRAMMING_LANGUAGE"
    }
  ],
  "message": "Skills retrieved successfully"
}
Add skill to profile
POST /api/v1/profile/me/skills
{
  "skillId": "uuid",
  "proficiencyLevel": "INTERMEDIATE",
  "yearsExperience": 2
}

The backend should reference an existing normalized skill rather than blindly creating a duplicate.

20. Certifications
GET    /api/v1/profile/me/certifications
POST   /api/v1/profile/me/certifications
GET    /api/v1/profile/me/certifications/{id}
PUT    /api/v1/profile/me/certifications/{id}
DELETE /api/v1/profile/me/certifications/{id}
21. Achievements
GET    /api/v1/profile/me/achievements
POST   /api/v1/profile/me/achievements
GET    /api/v1/profile/me/achievements/{id}
PUT    /api/v1/profile/me/achievements/{id}
DELETE /api/v1/profile/me/achievements/{id}
22. External Account APIs
List connections
GET /api/v1/external-accounts
Connect provider
POST /api/v1/external-accounts/{provider}/connect

Example:

POST /api/v1/external-accounts/GITHUB/connect

The exact OAuth implementation will depend on the provider.

23. GitHub APIs

These need special treatment because GitHub is an external service.

Start GitHub connection
GET /api/v1/integrations/github/connect

The backend can return or redirect to the appropriate authorization flow.

GitHub callback

Conceptually:

GET /api/v1/integrations/github/callback

This endpoint should validate the OAuth state and establish the external account connection.

Sync GitHub
POST /api/v1/integrations/github/sync

Response:

{
  "success": true,
  "data": {
    "repositoriesFound": 18,
    "repositoriesUpdated": 15,
    "repositoriesAdded": 3
  },
  "message": "GitHub synchronization completed"
}
24. GitHub Repository APIs
GET /api/v1/github/repositories
GET /api/v1/github/repositories/{repositoryId}

The repository ID in our API is our database UUID, not the raw GitHub numeric repository ID.

The backend internally maintains the external ID.

25. Evidence APIs
List candidate evidence
GET /api/v1/evidence

Filter example:

GET /api/v1/evidence?skillId=<uuid>
Create manual evidence
POST /api/v1/evidence

Example:

{
  "skillId": "uuid",
  "projectId": "uuid",
  "type": "PROJECT",
  "title": "E-commerce Backend",
  "description": "Built using Spring Boot and PostgreSQL"
}
26. Evidence status

Possible statuses:

SELF_REPORTED
SUPPORTED
VERIFIED
UNVERIFIED
REJECTED

The API must not allow a client to simply submit:

{
  "status": "VERIFIED"
}

and become verified.

Verification state must be controlled by backend business rules.

This is extremely important.

27. Evidence analysis
POST /api/v1/evidence/{evidenceId}/analyze

The backend can:

Evidence
   ↓
Rules + AI analysis
   ↓
Assessment

Example:

{
  "success": true,
  "data": {
    "status": "SUPPORTED",
    "confidenceScore": 0.87,
    "explanation": "The linked project contains evidence relevant to Spring Boot."
  },
  "message": "Evidence analyzed successfully"
}

The AI result should be treated as analysis, not unquestionable truth.

28. Resume APIs
List resumes
GET /api/v1/resumes
Create resume
POST /api/v1/resumes

Example:

{
  "name": "Java Backend Resume",
  "targetRole": "Java Backend Developer",
  "templateKey": "modern"
}
Get resume
GET /api/v1/resumes/{resumeId}
Update resume metadata
PUT /api/v1/resumes/{resumeId}
Delete/archive resume
DELETE /api/v1/resumes/{resumeId}

Deletion behavior must preserve application history where necessary.

29. Resume Versions
GET  /api/v1/resumes/{resumeId}/versions
POST /api/v1/resumes/{resumeId}/versions
GET  /api/v1/resumes/{resumeId}/versions/{versionId}

A version response:

{
  "id": "uuid",
  "versionNumber": 4,
  "createdAt": "2026-09-15T10:00:00Z",
  "content": {}
}

A submitted application can reference the exact resume version used.

30. Resume Truth Checker
POST /api/v1/resumes/{resumeId}/analyze

Response:

{
  "success": true,
  "data": {
    "claims": [
      {
        "text": "Improved API performance by 40%",
        "status": "UNSUPPORTED",
        "reason": "No supporting evidence was found."
      }
    ]
  },
  "message": "Resume analysis completed"
}

Again:

The system must flag unsupported claims, not invent replacement metrics.

31. Job APIs — Student side
Browse jobs
GET /api/v1/jobs

Supported query parameters may include:

page
size
sort
location
workMode
employmentType
skillId
search

Example:

GET /api/v1/jobs?page=0&size=20&workMode=REMOTE
32. Job details
GET /api/v1/jobs/{jobId}

The response may include:

Job
Company
Requirements
Skills
Questions
Deadline

Public/private information must be controlled according to job visibility.

33. Job Description Analysis

This should be a separate intelligence API.

POST /api/v1/job-analysis

Request:

{
  "jobDescription": "We are looking for a Java developer..."
}

Response:

{
  "success": true,
  "data": {
    "jobTitle": "Java Developer",
    "requiredSkills": [
      "Java",
      "Spring Boot",
      "PostgreSQL"
    ],
    "preferredSkills": [
      "Docker"
    ],
    "experience": {
      "minimumYears": 1
    }
  },
  "message": "Job description analyzed successfully"
}
34. Job Matching
POST /api/v1/jobs/{jobId}/match

or:

POST /api/v1/matching/jobs/{jobId}

I prefer the second form because matching is an intelligence operation rather than a job resource CRUD operation.

Response:

{
  "success": true,
  "data": {
    "summary": "Strong match with gaps in AWS experience.",
    "strengths": [],
    "gaps": [],
    "evidence": [],
    "matchScore": 82
  },
  "message": "Job match calculated successfully"
}

The exact scoring methodology should be maintained in the matching specification rather than hidden inside the API.

35. Career Gap Analysis
POST /api/v1/career-gap-analysis

Request:

{
  "jobId": "uuid"
}

Response might contain:

Strengths
Gaps
Recommended skills
Recommended projects
36. Application APIs
Apply
POST /api/v1/jobs/{jobId}/applications

Request:

{
  "resumeVersionId": "uuid",
  "answers": [
    {
      "questionId": "uuid",
      "answer": "Yes"
    }
  ]
}

The backend must verify:

user is authenticated
user has STUDENT role
job exists
job is accepting applications
deadline has not passed, if applicable
resume belongs to the applicant
resume version belongs to that resume
required questions are answered
duplicate application is not allowed under MVP rules
37. Application Response
{
  "success": true,
  "data": {
    "id": "uuid",
    "jobId": "uuid",
    "status": "APPLIED",
    "appliedAt": "2026-09-15T10:20:00Z"
  },
  "message": "Application submitted successfully"
}
38. Student Applications
GET /api/v1/applications

Filters:

status
page
size
sort
Application details
GET /api/v1/applications/{applicationId}

Student can see their own application.

Employer can see applications for their own jobs, subject to permissions.

39. Withdraw application
POST /api/v1/applications/{applicationId}/withdraw

This should change the application state appropriately rather than deleting the application.

40. Application Status History
GET /api/v1/applications/{applicationId}/history

Response:

{
  "success": true,
  "data": [
    {
      "fromStatus": "APPLIED",
      "toStatus": "SCREENING",
      "changedAt": "2026-09-16T09:00:00Z"
    },
    {
      "fromStatus": "SCREENING",
      "toStatus": "SHORTLISTED",
      "changedAt": "2026-09-17T12:00:00Z"
    }
  ],
  "message": "Application history retrieved successfully"
}
41. Employer APIs

Employer authentication uses the same Firebase system.

Their application role must be:

EMPLOYER
42. Company APIs
Create company
POST /api/v1/employer/company
Get company
GET /api/v1/employer/company
Update company
PUT /api/v1/employer/company
43. Employer Job Creation
POST /api/v1/employer/jobs

Request:

{
  "title": "Java Backend Developer",
  "description": "...",
  "employmentType": "FULL_TIME",
  "workMode": "HYBRID",
  "location": "Kolkata",
  "experienceMin": 1,
  "experienceMax": 3,
  "openings": 2,
  "applicationDeadline": "2026-10-15",
  "requiredSkills": [
    "skill-uuid-1"
  ],
  "preferredSkills": [
    "skill-uuid-2"
  ],
  "questions": [
    {
      "questionText": "Do you have experience with Spring Boot?",
      "questionType": "YES_NO",
      "isRequired": true
    }
  ]
}

The backend must validate company ownership/membership before creating the job.

44. Employer Job Management
GET    /api/v1/employer/jobs
GET    /api/v1/employer/jobs/{jobId}
PUT    /api/v1/employer/jobs/{jobId}
DELETE /api/v1/employer/jobs/{jobId}
POST   /api/v1/employer/jobs/{jobId}/publish
POST   /api/v1/employer/jobs/{jobId}/close

For historical reasons, DELETE should generally be implemented as archive/deactivation rather than physically destroying a job with applications.

45. Candidate Search
GET /api/v1/employer/candidates

Possible filters:

skill
experience
graduationYear
location
project
certification
availability

Example:

GET /api/v1/employer/candidates?skill=java&graduationYear=2027

Do not expose private fields merely because they exist in PostgreSQL.

The API response should contain only recruiter-authorized fields.

46. Candidate Profile for Employer
GET /api/v1/employer/candidates/{profileId}

Before returning data, verify:

Authenticated user
       ↓
EMPLOYER
       ↓
Allowed employer access
       ↓
Candidate visibility rules
47. Employer Application APIs
GET /api/v1/employer/jobs/{jobId}/applications
GET /api/v1/employer/applications/{applicationId}

An employer must only access applications associated with its own jobs.

48. ATS Status Change
POST /api/v1/employer/applications/{applicationId}/status

Request:

{
  "status": "SHORTLISTED",
  "note": "Strong Java and Spring Boot evidence."
}

The backend should validate legal/allowed status transitions.

Don't allow arbitrary jumps if the product workflow doesn't permit them.

For example, whether:

APPLIED → HIRED

is directly allowed should be a business rule.

49. Employer Notes
GET  /api/v1/employer/applications/{applicationId}/notes
POST /api/v1/employer/applications/{applicationId}/notes
PUT  /api/v1/employer/applications/{applicationId}/notes/{noteId}
DELETE /api/v1/employer/applications/{applicationId}/notes/{noteId}

Notes are private employer-side data and must never appear in the student's profile/API.

50. Interview APIs
GET  /api/v1/applications/{applicationId}/interviews
POST /api/v1/applications/{applicationId}/interviews
PUT  /api/v1/interviews/{interviewId}
DELETE /api/v1/interviews/{interviewId}

Employer authorization must be checked.

51. Interview Intelligence

Future intelligence endpoint:

POST /api/v1/interview-analysis

Input may include:

Resume
Job
Project
Interview type

Output:

Questions
Topics
Weak areas
Feedback

This can remain a later MVP/V1 feature depending on scope.

52. Admin APIs

Admin must have:

ADMIN

role.

Potential endpoints:

GET /api/v1/admin/users
GET /api/v1/admin/employers
GET /api/v1/admin/companies
GET /api/v1/admin/reports
PATCH /api/v1/admin/users/{id}/status

These should be implemented only as needed.

Don't build a giant admin API merely for architecture completeness.

53. Pagination

Any endpoint that may return many records should support pagination.

Recommended:

page = 0-based
size = 20

Example:

GET /api/v1/jobs?page=0&size=20

Response:

{
  "success": true,
  "data": [],
  "pagination": {
    "page": 0,
    "size": 20,
    "totalElements": 125,
    "totalPages": 7,
    "hasNext": true,
    "hasPrevious": false
  },
  "message": "Jobs retrieved successfully"
}

Do not return thousands of candidates in one response.

54. Sorting

Where appropriate:

sort=createdAt,desc

or:

sort=title,asc

The backend should whitelist sortable fields rather than accepting arbitrary SQL-like values.

55. Filtering

Use query parameters.

Example:

GET /api/v1/jobs
    ?page=0
    &size=20
    &workMode=REMOTE
    &employmentType=FULL_TIME

Don't create a separate endpoint for every possible filter.

56. Validation

There are two levels:

Frontend validation
        +
Backend validation

Frontend helps the user.

Backend protects the system.

Example:

Frontend:
"Email looks invalid."

Backend:
"I will verify it again."

Never trust client-side validation alone.

57. Error Codes

Use stable machine-readable codes.

Examples:

UNAUTHENTICATED
FORBIDDEN
USER_NOT_FOUND
PROFILE_NOT_FOUND
PROJECT_NOT_FOUND
INVALID_REQUEST
VALIDATION_ERROR
DUPLICATE_APPLICATION
JOB_NOT_FOUND
JOB_CLOSED
APPLICATION_DEADLINE_PASSED
RESUME_NOT_FOUND
RESUME_VERSION_NOT_FOUND
GITHUB_CONNECTION_FAILED
GITHUB_RATE_LIMITED
EXTERNAL_SERVICE_ERROR

Frontend developers should use error.code, not parse the human-readable message.

58. Rate Limiting

Rate limits are especially important for:

GitHub sync
AI analysis
Job analysis
Job matching
Authentication-related APIs
Candidate search

Example conceptual rule:

Normal CRUD → moderate limit
AI operations → stricter limit
GitHub sync → strict limit

The exact numerical limits should be decided during deployment/testing rather than hardcoded into this requirements document prematurely.

59. Idempotency

Some operations may be retried.

For example:

POST /applications

The client might accidentally send the request twice.

The backend must use database constraints/business logic to prevent duplicate applications.

This is why we already defined:

UNIQUE(job_id, profile_id)

for the MVP.

60. External API failures

Suppose GitHub is unavailable.

SkillBridge shouldn't crash.

Instead:

SkillBridge
     │
     ▼
GitHub API
     │
     X
 unavailable
     │
     ▼
Clear error

Example:

{
  "success": false,
  "error": {
    "code": "GITHUB_UNAVAILABLE",
    "message": "GitHub could not be reached. Please try again later."
  }
}

The existing candidate data should remain intact.

61. AI failures

The same principle applies to AI.

If AI fails:

AI unavailable
      ↓
Don't destroy user data
      ↓
Return controlled error

Example:

{
  "success": false,
  "error": {
    "code": "AI_SERVICE_UNAVAILABLE",
    "message": "Analysis is temporarily unavailable."
  }
}

Core CRUD functions must continue working independently of AI wherever possible.

62. API Security Rules

These are non-negotiable:

Never trust:
- userId from frontend
- role from frontend
- profile ownership from frontend
- company ownership from frontend
- verification status from frontend

The backend derives these from authenticated identity + database state.

63. Don't expose database entities directly

Don't simply do:

return userRepository.findById(id);

and serialize the database entity directly.

Prefer:

Entity
  ↓
Service
  ↓
DTO
  ↓
API Response

Why?

Because database entities may contain fields that should never be exposed.

64. DTO Principle

Example:

Database:

User
├── id
├── firebaseUid
├── email
├── status
├── deletedAt
└── internal fields

API response:

{
  "id": "...",
  "email": "...",
  "role": "STUDENT"
}

The API exposes only what the client actually needs.

65. Suggested Spring Boot structure

This API design maps cleanly to:

backend/
└── src/main/java/com/skillbridge/
    │
    ├── config/
    ├── security/
    │
    ├── user/
    ├── profile/
    ├── education/
    ├── experience/
    ├── project/
    ├── skill/
    ├── certification/
    ├── achievement/
    ├── evidence/
    ├── external/
    ├── github/
    ├── resume/
    ├── job/
    ├── matching/
    ├── application/
    ├── company/
    ├── employer/
    ├── interview/
    ├── admin/
    │
    └── common/

Each feature can contain:

Controller
Service
Repository
Entity
DTO
Mapper

where appropriate.

66. API ownership by team

This is useful for your team management.

Module	Primary Owner
Auth/Security	Member 2
Profile	Member 2
Education/Experience	Member 2
Projects/Skills	Member 2 + 3
GitHub	Member 3
Evidence	Member 4
Resume	Member 1 + 2
Job Intelligence	Member 4
Matching	Member 4
Applications	Member 2
Employer/Company	Member 5
ATS	Member 5
Testing	Member 5
Database	Member 3

This doesn't mean only one person can touch a module. It means there is a clear owner.

67. API development workflow

Now connect everything we've learned about GitHub teamwork:

Requirement
    ↓
User Flow
    ↓
Database Design
    ↓
API Specification
    ↓
GitHub Issue
    ↓
Developer creates branch
    ↓
Implementation
    ↓
Unit tests
    ↓
Pull Request
    ↓
API review
    ↓
Integration testing
    ↓
Merge

This is why we're creating these documents before development.

68. Example: Build Student Profile

Your team lead task:

Issue #12
Implement Student Profile API

Developer creates:

git switch -c feature/student-profile-api

Implements:

POST /api/v1/profile
GET  /api/v1/profile/me
PUT  /api/v1/profile/me

Adds:

Controller
Service
Repository
DTO
Validation
Tests

Then:

git add .
git commit -m "Implement student profile API"
git push

Then:

PR
 ↓
Review
 ↓
Testing
 ↓
Merge
69. Our API contract rule

This should go into your team documentation:

Frontend and backend developers must implement against the API specification.

If a developer wants to change:

POST /api/v1/profile

to:

POST /api/v2/profile

they don't casually change it.

They first discuss the change, update the API specification, then implement the change.

70. One issue I deliberately have NOT finalized

There is one thing we should not guess yet:

File storage

SkillBridge will eventually need files such as:

Resume PDFs
Profile photos
Certificates
Evidence documents

We previously discussed Supabase Storage, but we haven't explicitly locked the storage architecture alongside Firebase.

I don't want to silently make that decision in the API contract.

So before we finalize the file-upload endpoints, we need to choose:

A. Supabase Storage

Firebase Auth
+
Supabase PostgreSQL
+
Supabase Storage
