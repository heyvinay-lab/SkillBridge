security-and-privacy.md

This document should be treated as a mandatory engineering specification, not optional documentation.

SkillBridge will handle:

Personal information
Resumes
Certificates
Evidence documents
GitHub data
Job applications
Recruiter notes
Interview information

So security must be designed before implementation, especially for Firebase Authentication, Spring Boot authorization, PostgreSQL, GitHub OAuth, and Supabase Storage.

1. Security Objectives

SkillBridge must ensure:

Only authenticated users can access protected resources.
Users can access only resources they are authorized to access.
Students cannot access other students' private information.
Employers can access only candidate information they are permitted to see.
Employers can access only their own company's jobs/applications.
Admin access is restricted to administrative operations.
Sensitive files remain private unless intentionally made accessible.
Secrets are never exposed in frontend code or GitHub.
External integrations cannot compromise the application if they fail.
Important actions and changes remain traceable.
2. Security Architecture

The security model is:

                    USER
                      │
                      ▼
                 Next.js App
                      │
                      ▼
             Firebase Authentication
                      │
                      ▼
               Firebase ID Token
                      │
              Authorization Header
                      │
                      ▼
              Spring Boot Backend
                      │
        ┌─────────────┼──────────────┐
        ▼             ▼              ▼
 Authentication   Authorization   Validation
        │             │              │
        └─────────────┼──────────────┘
                      ▼
                 Business Logic
                      │
              ┌───────┴───────┐
              ▼               ▼
        PostgreSQL       Supabase Storage

The backend is the security boundary.

3. Authentication

SkillBridge will use:

Firebase Authentication

Supported authentication methods are controlled by the Firebase project configuration.

Initial expected methods may include:

Email / Password
Google Sign-In

Only methods actually enabled in the Firebase project should be considered supported.

4. Firebase ID Token

After authentication, the frontend receives a Firebase ID token.

Protected API requests use:

Authorization: Bearer <Firebase-ID-Token>

Example:

GET /api/v1/profile/me
Authorization: Bearer eyJhbGciOi...
5. Backend Token Verification

Spring Boot must verify the Firebase ID token.

It must not simply trust:

Authorization header

or a user ID supplied in JSON.

The verification flow is:

Firebase ID Token
        ↓
Spring Boot Firebase verification
        ↓
Valid?
   ┌────┴────┐
   │         │
  No        Yes
   │         │
 401         ▼
        Firebase UID

Only a successfully verified identity proceeds to protected business logic.

6. User Identity Mapping

After Firebase verification:

Firebase UID
     ↓
users.firebase_uid
     ↓
SkillBridge User

firebase_uid must be unique in PostgreSQL.

The application should use the authenticated Firebase UID to locate the SkillBridge user.

7. First-Time User Initialization

Authentication and application registration are separate concepts.

Example:

User signs in with Firebase
          ↓
Firebase identity exists
          ↓
SkillBridge user record checked
          ↓
Does user exist?
     ┌────┴────┐
     │         │
    No        Yes
     │         │
 Create      Continue
 User

The backend determines the application state.

8. Role Security

Each user has exactly one role:

STUDENT
EMPLOYER
ADMIN

The role is stored in the SkillBridge database.

Critical rule

The client cannot decide its own role.

Bad:

{
  "role": "ADMIN"
}

and then trusting it.

Correct:

Firebase UID
      ↓
Database user
      ↓
Database role
      ↓
Authorization
9. Authorization

Authentication answers:

Who are you?

Authorization answers:

What are you allowed to do?

Every protected operation must evaluate authorization.

Example:

Authenticated user
       ↓
Role = STUDENT
       ↓
Owns requested profile?
       ↓
Allowed
10. Role Permissions
STUDENT

Can:

Manage own profile
Manage own projects
Manage own skills
Manage own evidence
Manage own resumes
View permitted jobs
Apply to eligible jobs
View own applications
Manage own preferences

Cannot:

Modify another student's profile
Access another student's private files
Modify employer jobs
View private employer notes
Access administrative operations
EMPLOYER

Can:

Manage authorized company information
Create/manage company jobs
View applications to their own jobs
View permitted candidate information
Manage their hiring pipeline
Add employer notes
Manage interviews for their hiring workflow

Cannot:

Modify another company's jobs
Access unrelated employer data
Modify candidate-owned data
Access private candidate information beyond authorized visibility
ADMIN

Can perform only explicitly defined administrative operations.

Admin access should be:

Explicit
Auditable
Limited

Being an admin does not mean every internal API should automatically expose all data.

11. Resource Ownership

This is one of the most important security rules.

Suppose:

Profile A → User A
Profile B → User B

User A requests:

GET /api/v1/profiles/Profile-B

The backend must evaluate whether User A is authorized.

For user-owned resources:

Firebase UID
     ↓
User
     ↓
Profile ownership
     ↓
Resource ownership

If unauthorized:

403 Forbidden

or, where appropriate for privacy-preserving resource lookup, 404 Not Found.

The team should use one consistent strategy per resource category.

12. Never Trust Client-Supplied Ownership

Do not accept something like:

{
  "userId": "someone-else"
}

and use it to determine ownership.

The backend should derive ownership from the authenticated identity.

For example:

POST /api/v1/profile/me/projects

is preferable to allowing the client to arbitrarily specify a target user ID.

13. Employer Authorization

Employer access is more complex because employers belong to companies.

The authorization chain should be:

Firebase UID
     ↓
User
     ↓
Company Membership
     ↓
Company
     ↓
Job
     ↓
Application

An employer must only access applications belonging to jobs belonging to a company they are authorized to manage.

14. Company Membership

Company membership can contain:

OWNER
ADMIN
RECRUITER
HIRING_MANAGER

Each membership should have an appropriate permission model.

Do not assume every employee of a company automatically has unlimited recruiter access.

15. Candidate Privacy

Candidate information should be divided conceptually into:

Public
Employer-visible
Private

Example:

Information	Possible visibility
Name	Public/Employer
Skills	Public/Employer
Projects	Public/Employer
Resume	Controlled
Phone	Private/Controlled
Application data	Private
Employer evaluation	Employer only
Recruiter notes	Employer only
Private files	Owner/authorized party

The final product policy must define the exact defaults.

16. Private Profile Data

The database may contain information that does not appear in an API response.

For example:

PostgreSQL
   │
   ├── phone
   ├── application data
   ├── employer notes
   └── private evidence

The existence of a column does not mean the frontend can request it.

17. Resume Security

Resumes may contain:

Phone numbers
Email addresses
Education history
Employment history
Personal information

Therefore, resume access must be controlled.

A private resume should not be placed in a publicly accessible storage bucket.

18. Supabase Storage

SkillBridge will use:

Supabase Storage

for files such as:

Profile photos
Resume PDFs
Certificates
Evidence documents

The actual files are stored in Supabase Storage.

PostgreSQL stores the corresponding metadata.

19. Storage Security

Recommended architecture:

Private Supabase Bucket
          │
          ▼
Spring Boot
     ├── Authentication
     ├── Ownership check
     └── Authorization
          │
          ▼
Controlled file access

The browser must not be given unrestricted storage credentials.

20. Storage Credentials

Any privileged Supabase credentials must exist only on the backend/server environment.

Never place them in:

Next.js source
Public environment variables
GitHub repository
Frontend JavaScript bundle

Especially never expose privileged server keys to the browser.

21. File Metadata Security

The file_records table contains:

id
user_id
bucket_name
storage_path
original_filename
mime_type
size_bytes
purpose
visibility
created_at

The backend must verify:

authenticated user
        ↓
owns file?
        ↓
allowed to access?

before providing private access.

22. File Upload Validation

Never trust only the filename extension.

For uploaded files, validate:

File size
MIME type
Allowed file category
Extension
Actual content where practical
Owner
Purpose

Example:

A file named:

certificate.pdf

should not automatically be trusted just because it ends with .pdf.

23. File Size Limits

Every upload category should have a defined maximum size.

Example policy structure:

Profile photo → small limit
Resume PDF    → moderate limit
Certificate   → moderate limit
Evidence      → defined limit

The exact numerical limits should be fixed in the implementation configuration before production.

They should not be unlimited.

24. File Type Allowlist

Use allowlists rather than broad unrestricted uploads.

For example:

Resume:
application/pdf

Profile photo:
approved image MIME types

Certificate:
approved document/image types

Do not allow arbitrary executable formats.

25. Download Security

For private files:

User
 ↓
API request
 ↓
Firebase token verification
 ↓
Authorization
 ↓
Generate controlled access
 ↓
Supabase Storage

Avoid permanently exposing sensitive private-file URLs.

26. GitHub OAuth Security

GitHub integration may involve OAuth.

The backend must protect:

Client ID
Client secret
OAuth state
Access tokens
Refresh tokens, if applicable

The OAuth state mechanism should be used to protect against request-forgery issues in the OAuth flow.

27. GitHub Tokens

GitHub access tokens must never be:

Returned unnecessarily to frontend
Logged in plaintext
Stored in Git
Placed in URLs
Included in error messages

If long-lived tokens need to be stored, they must have appropriate protection and access restrictions.

The exact token storage mechanism should be chosen when GitHub OAuth implementation is finalized.

28. Minimum GitHub Permissions

SkillBridge should request only the permissions actually required.

Principle:

Least privilege

If public repository information is sufficient for a feature, don't request unnecessary private repository access.

If private repository access becomes a feature, that must be explicitly designed and consented to.

29. External API Failure

GitHub, AI providers, or other services can fail.

An external-service failure must not:

Delete user data
Corrupt candidate profile
Expose credentials
Break database integrity

Instead:

External service failure
       ↓
Controlled error
       ↓
Log technical details securely
       ↓
Show safe user message
30. AI Security

AI may receive sensitive information.

Before sending data to an external AI service, the application must define:

What data is sent?
Why is it sent?
Which provider receives it?
How long may it be retained?

Do not send unnecessary personal information.

For example, job matching may not require:

Phone number
Home address
Private application notes

Do not include them.

31. AI Must Not Become an Authorization Mechanism

Never do:

AI:
"This candidate appears trustworthy."

Backend:
"Give employer access."

AI output must never control security permissions.

Authorization remains deterministic backend logic.

32. AI Evidence Security

AI may analyze:

Resume
Project description
GitHub information
Evidence
Job description

But the AI cannot independently elevate a claim to:

VERIFIED

without an explicitly defined verification mechanism.

33. Input Validation

All externally supplied data should be treated as untrusted.

Validate:

Request body
Query parameters
Path variables
File uploads
URLs
Dates
Numbers
Enums
Text lengths

Validation must exist on the backend.

34. Injection Protection

The application must protect against:

SQL injection
XSS
malicious HTML
command injection
unsafe file processing
unsafe deserialization

Using parameterized queries/ORM mechanisms correctly is essential.

Do not construct SQL using raw string concatenation from user input.

35. XSS Protection

Candidate-entered text may include:

Summary
Project description
Experience
Achievements
Application answers
Notes

The frontend must safely render user-generated content.

Do not blindly interpret user text as HTML.

36. Password Security

SkillBridge itself should not store raw Firebase passwords.

Firebase Authentication manages authentication credentials.

SkillBridge should store only necessary application identity information.

37. Secrets Management

Never commit:

Firebase service account credentials
GitHub client secret
Supabase service key
Database password
AI API key
Storage credentials

to GitHub.

Use:

.env

locally and secure environment/secret management in hosted environments.

The repository may contain:

.env.example

with empty placeholders.

38. .gitignore

The repository must ignore sensitive/local files such as:

.env
.env.*
credentials
private keys
logs
IDE files
build output
node_modules

The exact .gitignore must be reviewed so it doesn't accidentally exclude required project files.

39. Secret Leak Response

If a secret is accidentally committed:

Do not simply delete the line and assume the secret is safe.

The credential must be:

Revoked/rotated
 ↓
Removed from active configuration
 ↓
Repository history assessed
 ↓
Replacement secret configured

This is an important team procedure.

40. HTTPS

Production communication must use HTTPS.

Browser
   │
 HTTPS
   ▼
SkillBridge API

Do not send authentication tokens over unencrypted HTTP in production.

41. CORS

The backend must define allowed origins explicitly.

Avoid:

Access-Control-Allow-Origin: *

for authenticated production APIs unless there is a deliberate reason and the security implications are understood.

Production should use the actual SkillBridge frontend origin(s).

42. CSRF

The exact CSRF strategy depends on how authentication credentials are transported.

Because the planned API uses a Firebase Bearer token rather than a traditional server session cookie, the CSRF model differs from cookie-based authentication.

The team must nevertheless ensure that any future cookie-based authentication/session mechanism introduces appropriate CSRF protection.

Don't add or remove CSRF controls blindly; align them with the actual authentication mechanism.

43. Rate Limiting

Rate limiting should protect expensive and sensitive operations.

Especially:

Login-related operations
GitHub sync
AI analysis
Job analysis
Job matching
Candidate search
File uploads

Exact limits should be configured based on real usage and infrastructure.

44. Abuse Prevention

The application should detect or limit:

Repeated application submissions
Excessive GitHub synchronization
Excessive AI requests
Large upload abuse
Automated scraping of candidate profiles
Repeated failed authentication attempts

The system should avoid creating unnecessary personal-data exposure through bulk APIs.

45. Pagination as a Security Control

Pagination is not only a performance feature.

It also helps prevent:

GET /candidates

from exposing thousands of records in one request.

Candidate APIs should use:

page
size

with sensible maximum page sizes.

46. API Error Security

Do not expose internal information.

Bad:

Database connection failed:
jdbc:postgresql://...
password...

Bad:

NullPointerException at com.skillbridge...

The user should receive:

{
  "success": false,
  "error": {
    "code": "INTERNAL_SERVER_ERROR",
    "message": "An unexpected error occurred."
  }
}

Technical details belong in secure server logs.

47. Logging

Logs should help developers diagnose problems without leaking sensitive data.

Never log:

Passwords
Firebase ID tokens
OAuth access tokens
OAuth secrets
Database passwords
Private resume contents
Private recruiter notes

Be especially careful with request-body logging.

48. Audit Logging

For sensitive business operations, we should eventually maintain an audit trail.

Examples:

Employer changed application status
Employer added note
Admin disabled account
User connected GitHub
User deleted evidence

A future:

audit_logs

table can capture:

actor
action
resource
timestamp
result

This is a recommended security enhancement and should be introduced when the corresponding functionality is implemented.

49. Application Security

An application must not be modified by an unauthorized employer.

Correct:

Employer
   ↓
Their Company
   ↓
Their Job
   ↓
Application
   ↓
Authorized operation

Incorrect:

Employer
   ↓
applicationId
   ↓
Modify anything

Every resource relationship must be validated server-side.

50. Employer Notes

Recruiter notes are private employer-side information.

They must never appear in:

Student profile API
Public candidate profile
Resume
Student application history
Public job API

unless the product explicitly introduces a feature to expose them.

51. Application Data

Application data can contain:

Resume used
Application answers
Status
Recruiter notes
Interview details

Therefore:

Student → own application
Employer → applications for authorized jobs
Admin → only where explicitly required
Public → none
52. Resume Version Privacy

When a student submits a resume:

Application
   ↓
Resume
   ↓
Specific Resume Version

The specific submitted version must remain identifiable.

Later editing a different resume version must not silently alter historical application records.

53. Data Retention

Not all data should be retained forever.

The team should eventually define policies for:

Inactive accounts
Deleted profiles
Rejected applications
Closed jobs
Old resumes
Unused GitHub synchronization data
AI processing artifacts
Audit records

Retention rules should balance:

User privacy
Legal requirements
Business needs
Project complexity

For the academic MVP, implement the core technical behavior and document retention decisions rather than inventing legal policies.

54. Data Deletion

Deletion must distinguish between:

Profile deletion
Account deactivation
File deletion
Application history
Employer records

Deleting a user's profile must not blindly cascade through historical business records if doing so would destroy necessary application history.

Deletion behavior must follow the database rules in 04-database-design.md.

55. Database Security

Database credentials must only be available to trusted backend services.

The frontend must never contain:

DATABASE_URL
Database password
PostgreSQL credentials
Privileged Supabase credentials

PostgreSQL access should be restricted to the backend/database environment.

56. Supabase Database Access

The team should clearly distinguish:

Supabase PostgreSQL

from:

Supabase client-side database access

For SkillBridge's planned architecture:

Frontend
   ↓
Spring Boot
   ↓
PostgreSQL

The frontend should not bypass the backend and directly manipulate application tables.

This keeps business authorization centralized.

57. Database Constraints Are Security

Security isn't only authentication.

Database constraints should also prevent bad states:

Unique Firebase UID
Unique skill normalization
Unique resume version per resume
Unique candidate/job application where required
Valid foreign keys
Valid status values

A secure system should remain protected even when a developer makes a mistake in application logic.

58. Transaction Safety

Operations that change several related records should use transactions where required.

Example:

Submit application
   ↓
Create application
   ↓
Create answers
   ↓
Create initial status history

These should not leave half-created data if one step fails.

Conceptually:

BEGIN
  create application
  create answers
  create status history
COMMIT

or rollback if necessary.

59. Concurrency

The team must consider two requests happening at the same time.

Example:

Student clicks Apply twice

or:

Two recruiters change an application status simultaneously

Database constraints and transactional logic should protect important business rules.

60. File and Database Consistency

File storage and database operations are separate systems.

For example:

Upload file
    ↓
Supabase Storage
    ↓
Save metadata
    ↓
PostgreSQL

If one step fails, the application needs cleanup/retry logic.

The team must not assume both systems change atomically.

61. Security Testing

The team must test not only:

"Does the feature work?"

but also:

"Can the wrong person use it?"

Examples:

Test 1

Student A attempts to access Student B's project.

Expected:

403 / 404 according to endpoint policy
Test 2

Employer A attempts to access Employer B's application.

Expected:

403 / 404 according to endpoint policy
Test 3

Student sends:

{
  "role": "ADMIN"
}

Expected:

Role is not elevated
Test 4

User requests another user's private file.

Expected:

Access denied
Test 5

Unauthenticated user requests:

GET /api/v1/profile/me

Expected:

401
62. Security Test Categories

At minimum:

Authentication tests
Authorization tests
Ownership tests
Input validation tests
File upload tests
API security tests
Database constraint tests
External integration tests

Later:

Penetration testing
Dependency scanning
Automated security scanning

can be added.

63. Dependency Security

The team should keep:

Spring Boot
Java
Next.js
npm packages
Firebase SDK
Supabase libraries

reasonably updated.

Avoid adding unnecessary dependencies.

Every dependency increases:

Attack surface
Maintenance
Upgrade complexity
64. Production vs Development

Development credentials and data should be separate from production.

Development
    ≠
Production

Use separate configurations for:

Firebase
Database
Storage
GitHub OAuth
AI providers

where practical.

65. Test Data

Do not put real personal information into public development environments.

Use:

Dummy students
Dummy companies
Dummy resumes
Dummy applications

for testing.

Never commit real certificates, phone numbers, resumes, tokens, or private candidate information to GitHub.

66. Security Incident Process

If a serious security problem is found:

Discover
   ↓
Contain
   ↓
Assess
   ↓
Fix
   ↓
Rotate/revoke credentials if needed
   ↓
Test
   ↓
Document
   ↓
Release

Examples:

Private data exposed
Token leaked
Unauthorized API access
Database corruption

These should take priority over ordinary feature work.

67. Security Priority Levels

Suggested classification:

P0 — Critical
Authentication bypass
Cross-user private-data access
Credential leak
Database destructive vulnerability
P1 — High
Broken authorization
Private file exposure
Major API security issue
P2 — Medium
Weak validation
Non-critical information exposure
P3 — Low
Minor hardening
Non-sensitive security improvements
68. Security Checklist Before MVP
Authentication
[ ] Firebase token verification
[ ] Firebase UID mapping
[ ] Role validation

Authorization
[ ] Student ownership checks
[ ] Employer company checks
[ ] Admin authorization
[ ] Candidate visibility checks

API
[ ] Input validation
[ ] Error handling
[ ] Rate limiting
[ ] Pagination
[ ] CORS

Database
[ ] Foreign keys
[ ] Unique constraints
[ ] Transaction handling
[ ] Secure credentials

Storage
[ ] Private buckets where appropriate
[ ] File size limits
[ ] MIME validation
[ ] Ownership checks
[ ] Controlled access

Integrations
[ ] GitHub OAuth security
[ ] Token protection
[ ] External failure handling
[ ] AI data minimization

Secrets
[ ] .env ignored
[ ] No keys in Git
[ ] Production secrets configured securely

Testing
[ ] Authentication tests
[ ] Authorization tests
[ ] Cross-user access tests
[ ] File access tests
[ ] Application ownership tests
69. Non-Negotiable Rules

These should be highlighted in your repository:

1. Never trust the frontend for authorization.

2. Never expose privileged Firebase, Supabase, GitHub, database, or AI secrets to the client.

3. Never allow a user to access another user's private resources without authorization.

4. Never treat AI output as authorization or verified truth.

5. Never expose private employer notes to candidates.

6. Never make private files publicly accessible by default.

7. Never delete important hiring history simply to remove a resource from the UI.

8. Never commit real credentials or private personal data to GitHub.
