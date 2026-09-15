1. Database decisions — locked
Decision	Choice
Database	PostgreSQL
Primary key	UUID
Authentication	Firebase Authentication
Firebase UID	Unique external identity, not the database PK
Timestamps	TIMESTAMPTZ
Soft deletion	Used where historical records matter
User roles	Separate role/membership model, not one fragile role column
Skills	Central normalized skill table
Many-to-many	Explicit junction tables
Applications	Separate current status + status history
Job questions	Separate questions + answers
Resumes	Master profile remains source of truth
Evidence	Explicit source types and evidence records
GitHub	External account + repository tables
Auditability	Important business changes recorded
2. Core design principle

There are four different concepts that must not be mixed:

Firebase
   ↓
Identity

User
   ↓
Platform account

Profile
   ↓
Professional information

Resume
   ↓
Presentation of profile information

Therefore:

The resume is NOT the candidate's master database record.

The flow is:

User
  ↓
Profile
  ├── Education
  ├── Experience
  ├── Projects
  ├── Skills
  ├── Certifications
  └── Evidence
        ↓
      Resume
3. User and authentication
users
users
--------------------------------
id                  UUID PK
firebase_uid        VARCHAR UNIQUE NOT NULL
email               VARCHAR
status              ENUM
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
deleted_at          TIMESTAMPTZ NULL
Rules

firebase_uid must be unique.

Do not make email the primary key.

Why?

email can change
Firebase owns authentication
UID is the stable external identity
4. Roles

Earlier we had:

User.role

That's too restrictive.

A better design:

roles
roles
----------------
id          UUID PK
code        VARCHAR UNIQUE
name        VARCHAR

Initial values:

STUDENT
EMPLOYER
ADMIN
user_roles
user_roles
-------------------------
user_id       UUID FK
role_id       UUID FK
created_at    TIMESTAMPTZ

PK(user_id, role_id)

Now one account can theoretically have multiple roles without changing the database structure.

5. Student profile
profiles
profiles
--------------------------------
id                  UUID PK
user_id             UUID UNIQUE FK
first_name          VARCHAR
last_name           VARCHAR
headline            VARCHAR
summary             TEXT
phone               VARCHAR NULL
location_text       VARCHAR NULL
portfolio_url       TEXT NULL
profile_photo_url   TEXT NULL
visibility          ENUM
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Relationship:

users 1 ───── 1 profiles
Important

user_id UNIQUE ensures one profile per user.

6. Education
educations
educations
--------------------------------
id                  UUID PK
profile_id          UUID FK
institution_name    VARCHAR
degree              VARCHAR
field_of_study      VARCHAR
start_date          DATE NULL
end_date            DATE NULL
grade_value         VARCHAR NULL
grade_type          VARCHAR NULL
description         TEXT NULL
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Relationship:

profile 1 ───── N education
7. Experience
experiences
experiences
--------------------------------
id                  UUID PK
profile_id          UUID FK
organization_name  VARCHAR
job_title           VARCHAR
employment_type     VARCHAR
location            VARCHAR NULL
start_date          DATE
end_date            DATE NULL
is_current          BOOLEAN
description         TEXT
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
deleted_at          TIMESTAMPTZ NULL
Constraints

If:

is_current = true

then end_date should normally be NULL.

If:

end_date < start_date

reject the record.

8. Projects
projects
projects
--------------------------------
id                  UUID PK
profile_id          UUID FK
name                VARCHAR NOT NULL
description         TEXT
role_description    TEXT NULL
github_url          TEXT NULL
live_url            TEXT NULL
start_date          DATE NULL
end_date            DATE NULL
visibility          ENUM
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
deleted_at          TIMESTAMPTZ NULL
9. Skills

This is an area where the previous design needed improvement.

We don't want:

Java
java
JAVA
Java Programming

to become four skills.

skills
skills
--------------------------------
id                  UUID PK
name                VARCHAR UNIQUE
normalized_name     VARCHAR UNIQUE
category            VARCHAR
created_at          TIMESTAMPTZ

Example:

name = Spring Boot
normalized_name = spring_boot
category = FRAMEWORK
10. Profile ↔ Skill

A candidate can have many skills.

A skill belongs to many candidates.

Therefore:

profile_skills
profile_skills
--------------------------------
profile_id          UUID FK
skill_id            UUID FK
proficiency_level   VARCHAR NULL
years_experience    NUMERIC NULL
source_type         VARCHAR
created_at          TIMESTAMPTZ

PK(profile_id, skill_id)

This prevents duplicate:

Vinay + Java
Vinay + Java
Vinay + Java
11. Project ↔ Skill
project_skills
project_skills
--------------------------------
project_id          UUID FK
skill_id            UUID FK
created_at          TIMESTAMPTZ

PK(project_id, skill_id)

Example:

SkillBridge
 ├── Java
 ├── Spring Boot
 ├── PostgreSQL
 └── React
12. Evidence — redesigned properly

This was one of the biggest weaknesses in the earlier design.

A generic:

Evidence
source_url
source

isn't enough.

We need to distinguish what is being supported and where the evidence comes from.

evidence
evidence
--------------------------------
id                  UUID PK
profile_id          UUID FK
skill_id            UUID FK NULL
project_id          UUID FK NULL
type                VARCHAR
title               VARCHAR
description         TEXT
status              ENUM
confidence_score    NUMERIC NULL
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Possible type:

GITHUB_REPOSITORY
PROJECT
CERTIFICATION
EXPERIENCE
CODING_PROFILE
PORTFOLIO
DOCUMENT
ASSESSMENT
SELF_REPORTED

Possible status:

SELF_REPORTED
SUPPORTED
VERIFIED
UNVERIFIED
REJECTED
Important

VERIFIED should only be used when SkillBridge has a clearly defined verification mechanism.

AI saying:

“This looks real”

does not automatically mean verified.

13. Evidence source details

Rather than putting every possible source field directly into evidence, use separate source tables where structured data matters.

For GitHub:

evidence
   ↓
github_repository

For certificates:

evidence
   ↓
certification

For assessments:

evidence
   ↓
assessment_result

This prevents one giant table full of:

github_url
certificate_url
assessment_id
coding_url
portfolio_url
...

with most fields NULL.

14. Certifications
certifications
certifications
--------------------------------
id                  UUID PK
profile_id          UUID FK
name                VARCHAR
issuing_organization VARCHAR
credential_id       VARCHAR NULL
credential_url      TEXT NULL
issued_date         DATE NULL
expiry_date         DATE NULL
description         TEXT NULL
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
15. Achievements / awards

Don't force achievements into projects.

achievements
achievements
--------------------------------
id                  UUID PK
profile_id          UUID FK
title               VARCHAR
organization        VARCHAR NULL
description         TEXT
achievement_date    DATE NULL
url                 TEXT NULL
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
16. External accounts
external_accounts
external_accounts
--------------------------------
id                  UUID PK
user_id             UUID FK
provider            VARCHAR
provider_user_id    VARCHAR NULL
username            VARCHAR NULL
profile_url         TEXT
status              VARCHAR
connected_at        TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Examples:

GITHUB
LEETCODE
CODECHEF
CODEFORCES
KAGGLE
Constraint

A user's same provider account should not accidentally be connected twice.

Use an appropriate uniqueness constraint, typically around:

(user_id, provider)

when one account per provider is allowed.

17. GitHub repositories
github_repositories
github_repositories
--------------------------------
id                      UUID PK
external_account_id     UUID FK
github_repository_id    BIGINT
name                    VARCHAR
full_name               VARCHAR
description             TEXT NULL
html_url                TEXT
default_branch          VARCHAR NULL
stars_count             INTEGER
forks_count             INTEGER
language_summary        JSONB NULL
readme_content          TEXT NULL
last_pushed_at          TIMESTAMPTZ NULL
github_created_at       TIMESTAMPTZ NULL
github_updated_at       TIMESTAMPTZ NULL
synced_at               TIMESTAMPTZ
Important

github_repository_id should be unique for the relevant GitHub installation/account model.

Don't rely on repository name as identity.

Two repositories can have the same name under different users/organizations.

18. Project ↔ GitHub Repository

A project may be linked to a GitHub repository.

Do not store only:

project.github_url

for everything.

We can support structured linkage:

project_repositories
project_repositories
--------------------------------
project_id              UUID FK
github_repository_id    UUID FK
is_primary              BOOLEAN
created_at              TIMESTAMPTZ

PK(project_id, github_repository_id)

This allows a project to use:

Frontend repo
Backend repo
Mobile repo

if needed.

19. Resume structure
resumes
resumes
--------------------------------
id                  UUID PK
profile_id          UUID FK
name                VARCHAR
target_role         VARCHAR NULL
template_key        VARCHAR
status              VARCHAR
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
deleted_at          TIMESTAMPTZ NULL

Examples:

Java Backend Resume
SDE Resume
Internship Resume
20. Resume versions
resume_versions
resume_versions
--------------------------------
id                  UUID PK
resume_id           UUID FK
version_number      INTEGER
content             JSONB
created_at          TIMESTAMPTZ
created_by          UUID FK

Constraint:

UNIQUE(resume_id, version_number)

This prevents:

Resume 1
Version 2
Version 2
21. Why JSONB for resume content?

The resume has variable structure:

Summary
Education
Projects
Experience
Achievements
...

Different templates may have different layout requirements.

Keeping the canonical profile relationally structured while storing a renderable resume snapshot as JSONB provides flexibility.

Important:

Resume JSONB is a snapshot/presentation model, not the authoritative source of candidate data.

22. Companies
companies
companies
--------------------------------
id                  UUID PK
name                VARCHAR
legal_name          VARCHAR NULL
description         TEXT NULL
website_url         TEXT NULL
industry             VARCHAR NULL
location             VARCHAR NULL
logo_url             TEXT NULL
status               VARCHAR
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
23. Employer memberships

Earlier we had:

Company 1 → N Employer

but it's cleaner to represent the relationship explicitly.

company_memberships
company_memberships
--------------------------------
id                  UUID PK
company_id          UUID FK
user_id             UUID FK
membership_role     VARCHAR
status              VARCHAR
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Possible membership roles:

OWNER
ADMIN
RECRUITER
HIRING_MANAGER

Now:

User
   ↓
Company Membership
   ↓
Company

This is much better for authorization.

24. Jobs
jobs
jobs
--------------------------------
id                  UUID PK
company_id          UUID FK
created_by_user_id  UUID FK
title               VARCHAR
description         TEXT
employment_type     VARCHAR
work_mode           VARCHAR
location            VARCHAR NULL
salary_min          NUMERIC NULL
salary_max          NUMERIC NULL
salary_currency     CHAR(3) NULL
experience_min      NUMERIC NULL
experience_max      NUMERIC NULL
openings            INTEGER
application_deadline DATE NULL
status              VARCHAR
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
published_at        TIMESTAMPTZ NULL
closed_at           TIMESTAMPTZ NULL
25. Job skills
job_skills
job_skills
--------------------------------
job_id              UUID FK
skill_id            UUID FK
requirement_type    ENUM
created_at          TIMESTAMPTZ

PK(job_id, skill_id)

requirement_type:

REQUIRED
PREFERRED
26. Job custom questions

This is missing from the earlier simplified database.

job_questions
job_questions
--------------------------------
id                  UUID PK
job_id              UUID FK
question_text       TEXT
question_type       VARCHAR
is_required         BOOLEAN
display_order       INTEGER
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Question types could include:

TEXT
LONG_TEXT
YES_NO
NUMBER
URL
27. Applications
applications
applications
--------------------------------
id                  UUID PK
job_id              UUID FK
profile_id          UUID FK
resume_id           UUID FK NULL
resume_version_id   UUID FK NULL
status              VARCHAR
applied_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ
withdrawn_at        TIMESTAMPTZ NULL
Critical constraint

A candidate should not accidentally create unlimited duplicate applications for the same job unless the product explicitly allows reapplication.

For the MVP:

UNIQUE(job_id, profile_id)

is a sensible starting rule.

28. Why store both Resume and Resume Version?

Suppose:

Student applies to Company A

using:

Java Backend Resume
Version 4

Later they edit the resume.

We still need to know what was submitted.

Therefore storing:

resume_id
resume_version_id

gives us historical traceability.

29. Application answers
application_answers
application_answers
--------------------------------
id                  UUID PK
application_id      UUID FK
job_question_id     UUID FK
answer_text         TEXT
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Constraint:

UNIQUE(application_id, job_question_id)
30. Application status history

This is extremely important for the ATS.

Don't only store:

applications.status

because then you lose the history.

application_status_history
application_status_history
--------------------------------
id                  UUID PK
application_id      UUID FK
from_status         VARCHAR NULL
to_status           VARCHAR
changed_by_user_id  UUID FK NULL
note                TEXT NULL
created_at          TIMESTAMPTZ

Now we can know:

APPLIED
   ↓
SCREENING
   ↓
SHORTLISTED
   ↓
INTERVIEW
   ↓
SELECTED

and exactly when each transition occurred.

31. Application notes

Recruiters may need private notes.

application_notes
application_notes
--------------------------------
id                  UUID PK
application_id      UUID FK
created_by_user_id  UUID FK
note                TEXT
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

These notes must obey employer/company authorization.

32. Interviews
interviews
interviews
--------------------------------
id                  UUID PK
application_id      UUID FK
scheduled_at        TIMESTAMPTZ NULL
interview_type      VARCHAR
status              VARCHAR
location_or_link    TEXT NULL
notes               TEXT NULL
created_at          TIMESTAMPTZ
updated_at          TIMESTAMPTZ

Potential types:

HR
TECHNICAL
PROJECT
MANAGERIAL
CODING
33. Candidate availability/preferences

This wasn't clearly modeled earlier.

A candidate may have preferences.

candidate_preferences
candidate_preferences
--------------------------------
id                  UUID PK
profile_id          UUID UNIQUE FK
preferred_work_mode VARCHAR NULL
preferred_location  VARCHAR NULL
remote_preference   BOOLEAN
job_search_status   VARCHAR NULL
expected_salary     NUMERIC NULL
notice_period_days  INTEGER NULL
updated_at          TIMESTAMPTZ

This gives job matching additional structured information.

34. Important relationship map

The core model becomes:

Firebase
   │
   ▼
users
   │
   ├──────── user_roles
   │
   └──────── profiles
                │
       ┌────────┼─────────┬──────────┐
       ▼        ▼         ▼          ▼
  education  experience projects   skills
                         │           │
                         └────┬──────┘
                              ▼
                           evidence
                              ▲
                              │
                       GitHub repository
                              ▲
                              │
                     external accounts

And hiring:

companies
   │
   ├── company_memberships
   │
   └── jobs
         │
         ├── job_skills
         ├── job_questions
         │
         └── applications
                 │
       ┌─────────┼───────────┐
       ▼         ▼           ▼
 answers      status      interviews
              history
35. Final core table list

For SkillBridge MVP, I recommend these tables:

01  users
02  roles
03  user_roles
04  profiles
05  educations
06  experiences
07  projects
08  skills
09  profile_skills
10  project_skills
11  certifications
12  achievements
13  evidence
14  external_accounts
15  github_repositories
16  project_repositories
17  resumes
18  resume_versions

19  companies
20  company_memberships
21  jobs
22  job_skills
23  job_questions

24  applications
25  application_answers
26  application_status_history
27  application_notes
28  interviews

29  candidate_preferences

This is much more complete than the earlier schema.

36. Foreign-key rules

We should establish these before coding.

User deletion

Don't casually cascade-delete everything when Firebase/user deletion occurs.

For important historical records:

ON DELETE RESTRICT

or controlled soft deletion.

Profile-owned data

For things like:

Profile → Education
Profile → Experience
Profile → Projects

we can use controlled cascading where appropriate.

Applications

Avoid cascading deletion of:

Job
 ↓
Applications

because applications are historical business records.

Instead:

Job → CLOSED

rather than deleting the job.

This is a major rule.

37. Never delete important business history

For example:

Company
   ↓
Job
   ↓
Application

Suppose the recruiter closes the job.

Do not delete the Job row.

Set:

status = CLOSED

Otherwise you destroy application history.

Similarly, don't delete:

application_status_history

just because someone closes an account.

38. Indexes

Your backend developer should create indexes for frequent lookups.

At minimum:

users(firebase_uid)
profiles(user_id)

profile_skills(profile_id)
profile_skills(skill_id)

project_skills(project_id)
project_skills(skill_id)

external_accounts(user_id, provider)

github_repositories(external_account_id)

companies(name)

company_memberships(company_id)
company_memberships(user_id)

jobs(company_id)
jobs(status)

job_skills(job_id)
job_skills(skill_id)

applications(job_id)
applications(profile_id)
applications(status)

application_status_history(application_id)
application_answers(application_id)

interviews(application_id)

Don't blindly index every column. Index according to actual query patterns and constraints.

39. Data validation rules

The database should protect itself as much as reasonably possible.

Examples:

experience.start_date <= experience.end_date
job.experience_min <= job.experience_max
job.salary_min <= job.salary_max
job.openings > 0
application status must be valid
skill normalized_name must be unique
resume version_number must be unique per resume

Some validation belongs in Spring Boot, some belongs in PostgreSQL.

Critical invariants should not depend only on frontend validation.

40. Privacy / visibility

Certain profile information should have visibility controls.

At minimum:

PUBLIC
EMPLOYERS
PRIVATE

But don't automatically make everything employer-visible.

Examples:

GitHub repositories
Projects
Skills
Resume
Experience
Contact information

can each have their own business rules.

Privacy should be enforced by the backend.

41. The biggest architectural rule

Your team should remember this:

Firebase
    = Identity

PostgreSQL
    = SkillBridge data

Spring Boot
    = Business rules + authorization

Frontend
    = User interface

Never let the frontend decide:

"This user is an admin, so I will give them admin data."

The backend must decide that.

42. AI data rule

Don't store AI output as unquestioned truth.

For example:

AI says:
Candidate demonstrates Spring Boot.

That shouldn't automatically become:

profile_skills → Spring Boot → VERIFIED

Instead:

AI analysis
    ↓
Evidence candidate
    ↓
Evidence rules
    ↓
Supported / Unverified / Verified

AI is an input to the system—not the authority over the database.

43. MVP vs Future tables

Don't build everything immediately.

MVP
users
roles
user_roles
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

This is already substantial.

Future

Later we can add:

colleges
college_memberships
hackathons
events
rankings
assessments
interview_sessions
interview_answers
project_recommendations
notifications
reports
audit_logs

We should not pollute the MVP schema with tables we won't use.

44. One more thing: schema versioning

Your team should never manually change the production database randomly.

Use a migration system.

For Spring Boot, use something like:

Flyway

Then:

V1__initial_schema.sql
V2__add_github_tables.sql
V3__add_application_history.sql
V4__add_resume_versions.sql

Now the entire team knows exactly which database changes happened and in what order.

45. Development rule for the whole team

Once we freeze this document:

Nobody creates a new table simply because they need one for their feature.

They first check:

Does the entity already exist?
       ↓
Can an existing relationship solve it?
       ↓
If not → discuss schema change
       ↓
Update database-design document
       ↓
Create migration
       ↓
Implement

This single rule will prevent a lot of database chaos.

Final SkillBridge database architecture
                         FIREBASE
                            │
                            ▼
                          users
                            │
                    ┌───────┴────────┐
                    ▼                ▼
                user_roles        profiles
                                      │
          ┌────────────┬──────────────┼─────────────┐
          ▼            ▼              ▼             ▼
      education    experience      projects       skills
                                      │              │
                                      ├──────────────┤
                                      ▼
                                  evidence
                                      ▲
                                      │
                              GitHub / Certificates
                                      │
                               external_accounts


                         companies
                            │
                     company_memberships
                            │
                            ▼
                           jobs
                     ┌──────┼──────┐
                     ▼      ▼      ▼
                job_skills questions
                            │
                            ▼
                      applications
                     ┌──────┼─────────┐
                     ▼      ▼         ▼
                  answers history  interviews


                         profiles
                            │
                            ▼
                         resumes
                            │
                            ▼
                    resume_versions
