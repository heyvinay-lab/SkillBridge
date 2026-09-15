# SkillBridge — Database decisions

Last updated: (from original document)

---

## Contents

1. [High-level decisions](#high-level-decisions)  
2. [Core design principle](#core-design-principle)  
3. [User and authentication](#user-and-authentication)  
4. [Roles](#roles)  
5. [Student profile](#student-profile)  
6. [Education](#education)  
7. [Experience](#experience)  
8. [Projects](#projects)  
9. [Skills](#skills)  
10. [Profile ↔ Skill](#profile--skill)  
11. [Project ↔ Skill](#project--skill)  
12. [Evidence (redesigned)](#evidence---redesigned)  
13. [Evidence source details](#evidence-source-details)  
14. [Certifications](#certifications)  
15. [Achievements / Awards](#achievements--awards)  
16. [External accounts](#external-accounts)  
17. [GitHub repositories](#github-repositories)  
18. [Project ↔ GitHub repository](#project--github-repository)  
19. [Resume structure](#resume-structure)  
20. [Resume versions](#resume-versions)  
21. [Why JSONB for resume content?](#why-jsonb-for-resume-content)  
22. [Companies](#companies)  
23. [Employer memberships](#employer-memberships)  
24. [Jobs](#jobs)  
25. [Job skills](#job-skills)  
26. [Job custom questions](#job-custom-questions)  
27. [Applications](#applications)  
28. [Why store both Resume and Resume Version?](#why-store-both-resume-and-resume-version)  
29. [Application answers](#application-answers)  
30. [Application status history](#application-status-history)  
31. [Application notes](#application-notes)  
32. [Interviews](#interviews)  
33. [Candidate availability / preferences](#candidate-availability--preferences)  
34. [Important relationship map](#important-relationship-map)  
35. [Final core table list](#final-core-table-list)  
36. [Foreign-key rules](#foreign-key-rules)  
37. [Never delete important business history](#never-delete-important-business-history)  
38. [Indexes](#indexes)  
39. [Data validation rules](#data-validation-rules)  
40. [Privacy / visibility](#privacy--visibility)  
41. [The biggest architectural rule](#the-biggest-architectural-rule)  
42. [AI data rule](#ai-data-rule)  
43. [MVP vs Future tables](#mvp-vs-future-tables)  
44. [Schema versioning](#schema-versioning)  
45. [Development rule for the whole team](#development-rule-for-the-whole-team)

---

## 1. High-level decisions

| Decision | Choice |
|---|---|
| Database | PostgreSQL |
| Primary key | UUID |
| Authentication | Firebase Authentication |
| Firebase UID | Unique external identity, not DB PK |
| Timestamps | TIMESTAMPTZ |
| Soft deletion | Used where historical records matter |
| User roles | Separate role/membership model, not a single role column |
| Skills | Central normalized skill table |
| Many-to-many | Explicit junction tables |
| Applications | Separate current status + status history |
| Job questions | Separate questions + answers |
| Resumes | Master profile remains source of truth |
| Evidence | Explicit source types and evidence records |
| GitHub | External account + repository tables |
| Auditability | Important business changes recorded |

---

## 2. Core design principle

There are four distinct concepts that must not be mixed:

- Firebase → Identity
- User → Platform account
- Profile → Professional information
- Resume → Presentation of profile information

Important: The resume is NOT the candidate's master database record.

Flow:

User  
→ Profile  
  ├── Education  
  ├── Experience  
  ├── Projects  
  ├── Skills  
  ├── Certifications  
  └── Evidence  
     ↓  
   Resume

---

## 3. User and authentication

Table: `users`

```text
users
--------------------------------
id            UUID PK
firebase_uid  VARCHAR UNIQUE NOT NULL
email         VARCHAR
status        ENUM
created_at    TIMESTAMPTZ
updated_at    TIMESTAMPTZ
deleted_at    TIMESTAMPTZ NULL
```

Rules:

- `firebase_uid` must be unique.
- Do not make `email` the primary key (emails can change; Firebase owns authentication).
- The Firebase UID is the stable external identity.

---

## 4. Roles

Avoid a single `User.role` column. Use normalized roles and a junction table.

Table: `roles`

```text
roles
----------------
id    UUID PK
code  VARCHAR UNIQUE
name  VARCHAR
```

Initial values: `STUDENT`, `EMPLOYER`, `ADMIN`

Table: `user_roles`

```text
user_roles
-------------------------
user_id     UUID FK
role_id     UUID FK
created_at  TIMESTAMPTZ

PK(user_id, role_id)
```

Now one account can have multiple roles.

---

## 5. Student profile

Table: `profiles`

```text
profiles
--------------------------------
id                 UUID PK
user_id            UUID UNIQUE FK
first_name         VARCHAR
last_name          VARCHAR
headline           VARCHAR
summary            TEXT
phone              VARCHAR NULL
location_text      VARCHAR NULL
portfolio_url      TEXT NULL
profile_photo_url  TEXT NULL
visibility         ENUM
created_at         TIMESTAMPTZ
updated_at         TIMESTAMPTZ
```

Relationship: `users` 1 ── 1 `profiles`  
`user_id UNIQUE` ensures one profile per user.

---

## 6. Education

Table: `educations`

```text
educations
--------------------------------
id               UUID PK
profile_id       UUID FK
institution_name VARCHAR
degree           VARCHAR
field_of_study   VARCHAR
start_date       DATE NULL
end_date         DATE NULL
grade_value      VARCHAR NULL
grade_type       VARCHAR NULL
description      TEXT NULL
created_at       TIMESTAMPTZ
updated_at       TIMESTAMPTZ
```

Relationship: `profile` 1 ── N `educations`

---

## 7. Experience

Table: `experiences`

```text
experiences
--------------------------------
id                 UUID PK
profile_id         UUID FK
organization_name  VARCHAR
job_title          VARCHAR
employment_type    VARCHAR
location           VARCHAR NULL
start_date         DATE
end_date           DATE NULL
is_current         BOOLEAN
description        TEXT
created_at         TIMESTAMPTZ
updated_at         TIMESTAMPTZ
deleted_at         TIMESTAMPTZ NULL
```

Constraints / rules:
- If `is_current = true`, `end_date` should normally be NULL.
- Reject records where `end_date < start_date`.

---

## 8. Projects

Table: `projects`

```text
projects
--------------------------------
id                 UUID PK
profile_id         UUID FK
name               VARCHAR NOT NULL
description        TEXT
role_description   TEXT NULL
github_url         TEXT NULL
live_url           TEXT NULL
start_date         DATE NULL
end_date           DATE NULL
visibility         ENUM
created_at         TIMESTAMPTZ
updated_at         TIMESTAMPTZ
deleted_at         TIMESTAMPTZ NULL
```

---

## 9. Skills

Avoid duplicates (different casing or phrasing). Normalize skill names.

Table: `skills`

```text
skills
--------------------------------
id               UUID PK
name             VARCHAR UNIQUE
normalized_name  VARCHAR UNIQUE
category         VARCHAR
created_at       TIMESTAMPTZ
```

Example:
- `name = Spring Boot`
- `normalized_name = spring_boot`
- `category = FRAMEWORK`

---

## 10. Profile ↔ Skill

Many-to-many between profiles and skills.

Table: `profile_skills`

```text
profile_skills
--------------------------------
profile_id         UUID FK
skill_id           UUID FK
proficiency_level  VARCHAR NULL
years_experience   NUMERIC NULL
source_type        VARCHAR
created_at         TIMESTAMPTZ

PK(profile_id, skill_id)
```

Prevents duplicate profile+skill rows.

---

## 11. Project ↔ Skill

Table: `project_skills`

```text
project_skills
--------------------------------
project_id    UUID FK
skill_id      UUID FK
created_at    TIMESTAMPTZ

PK(project_id, skill_id)
```

Example skills for a project: Java, Spring Boot, PostgreSQL, React

---

## 12. Evidence — redesigned properly

Evidence must describe what is being supported and where it came from.

Table: `evidence`

```text
evidence
--------------------------------
id               UUID PK
profile_id       UUID FK
skill_id         UUID FK NULL
project_id       UUID FK NULL
type             VARCHAR
title            VARCHAR
description      TEXT
status           ENUM
confidence_score NUMERIC NULL
created_at       TIMESTAMPTZ
updated_at       TIMESTAMPTZ
```

Possible `type` values:
- GITHUB_REPOSITORY, PROJECT, CERTIFICATION, EXPERIENCE, CODING_PROFILE, PORTFOLIO, DOCUMENT, ASSESSMENT, SELF_REPORTED

Possible `status` values:
- SELF_REPORTED, SUPPORTED, VERIFIED, UNVERIFIED, REJECTED

Important: `VERIFIED` should only be used when SkillBridge has a clear verification mechanism (AI alone does not verify).

---

## 13. Evidence source details

Instead of putting every possible source field into `evidence`, use separate source tables for structured data:

- `github_repository` (linked from `evidence`)
- `certification`
- `assessment_result`

This prevents a single bloated table with many mostly-NULL columns.

---

## 14. Certifications

Table: `certifications`

```text
certifications
--------------------------------
id                    UUID PK
profile_id            UUID FK
name                  VARCHAR
issuing_organization  VARCHAR
credential_id         VARCHAR NULL
credential_url        TEXT NULL
issued_date           DATE NULL
expiry_date           DATE NULL
description           TEXT NULL
created_at            TIMESTAMPTZ
updated_at            TIMESTAMPTZ
```

---

## 15. Achievements / awards

Table: `achievements`

```text
achievements
--------------------------------
id               UUID PK
profile_id       UUID FK
title            VARCHAR
organization     VARCHAR NULL
description      TEXT
achievement_date DATE NULL
url              TEXT NULL
created_at       TIMESTAMPTZ
updated_at       TIMESTAMPTZ
```

Don't force achievements into projects.

---

## 16. External accounts

Table: `external_accounts`

```text
external_accounts
--------------------------------
id                 UUID PK
user_id            UUID FK
provider           VARCHAR
provider_user_id   VARCHAR NULL
username           VARCHAR NULL
profile_url        TEXT
status             VARCHAR
connected_at       TIMESTAMPTZ
updated_at         TIMESTAMPTZ
```

Examples of `provider`: GITHUB, LEETCODE, CODECHEF, CODEFORCES, KAGGLE

Constraint: avoid connecting the same provider twice; typically enforce uniqueness on `(user_id, provider)` if one account per provider is allowed.

---

## 17. GitHub repositories

Table: `github_repositories`

```text
github_repositories
--------------------------------
id                    UUID PK
external_account_id   UUID FK
github_repository_id  BIGINT
name                  VARCHAR
full_name             VARCHAR
description           TEXT NULL
html_url              TEXT
default_branch         VARCHAR NULL
stars_count           INTEGER
forks_count           INTEGER
language_summary      JSONB NULL
readme_content        TEXT NULL
last_pushed_at        TIMESTAMPTZ NULL
github_created_at     TIMESTAMPTZ NULL
github_updated_at     TIMESTAMPTZ NULL
synced_at             TIMESTAMPTZ
```

Important: `github_repository_id` should be unique (per GitHub installation/account). Do not rely on repository `name` as identity.

---

## 18. Project ↔ GitHub Repository

Use a junction table rather than storing only a URL on `project`.

Table: `project_repositories`

```text
project_repositories
--------------------------------
project_id            UUID FK
github_repository_id  UUID FK
is_primary            BOOLEAN
created_at            TIMESTAMPTZ

PK(project_id, github_repository_id)
```

A project can reference multiple repos (frontend, backend, mobile).

---

## 19. Resume structure

Table: `resumes`

```text
resumes
--------------------------------
id            UUID PK
profile_id    UUID FK
name          VARCHAR
target_role   VARCHAR NULL
template_key  VARCHAR
status        VARCHAR
created_at    TIMESTAMPTZ
updated_at    TIMESTAMPTZ
deleted_at    TIMESTAMPTZ NULL
```

Examples: "Java Backend Resume", "SDE Resume", "Internship Resume"

---

## 20. Resume versions

Table: `resume_versions`

```text
resume_versions
--------------------------------
id              UUID PK
resume_id       UUID FK
version_number  INTEGER
content         JSONB
created_at      TIMESTAMPTZ
created_by      UUID FK

UNIQUE(resume_id, version_number)
```

This prevents duplicate version numbers per resume.

---

## 21. Why JSONB for resume content?

- Resumes have variable structure (Summary, Education, Projects, Experience, Achievements, etc.).
- Different templates require different layouts.
- Keep canonical profile relationally modeled; store a renderable resume snapshot as JSONB (presentation model).
- Important: Resume JSONB is a snapshot, not the authoritative source.

---

## 22. Companies

Table: `companies`

```text
companies
--------------------------------
id            UUID PK
name          VARCHAR
legal_name    VARCHAR NULL
description   TEXT NULL
website_url   TEXT NULL
industry      VARCHAR NULL
location      VARCHAR NULL
logo_url      VARCHAR NULL
status        VARCHAR
created_at    TIMESTAMPTZ
updated_at    TIMESTAMPTZ
```

---

## 23. Employer memberships

Table: `company_memberships`

```text
company_memberships
--------------------------------
id               UUID PK
company_id       UUID FK
user_id          UUID FK
membership_role  VARCHAR
status           VARCHAR
created_at       TIMESTAMPTZ
updated_at       TIMESTAMPTZ
```

Possible roles: `OWNER`, `ADMIN`, `RECRUITER`, `HIRING_MANAGER`

Now:
User → Company Membership → Company

This supports granular authorization.

---

## 24. Jobs

Table: `jobs`

```text
jobs
--------------------------------
id                   UUID PK
company_id           UUID FK
created_by_user_id   UUID FK
title                VARCHAR
description          TEXT
employment_type      VARCHAR
work_mode            VARCHAR
location             VARCHAR NULL
salary_min           NUMERIC NULL
salary_max           NUMERIC NULL
salary_currency      CHAR(3) NULL
experience_min       NUMERIC NULL
experience_max       NUMERIC NULL
openings             INTEGER
application_deadline DATE NULL
status               VARCHAR
created_at           TIMESTAMPTZ
updated_at           TIMESTAMPTZ
published_at         TIMESTAMPTZ NULL
closed_at            TIMESTAMPTZ NULL
```

---

## 25. Job skills

Table: `job_skills`

```text
job_skills
--------------------------------
job_id           UUID FK
skill_id         UUID FK
requirement_type ENUM
created_at       TIMESTAMPTZ

PK(job_id, skill_id)
```

`requirement_type`: `REQUIRED`, `PREFERRED`

---

## 26. Job custom questions

Table: `job_questions`

```text
job_questions
--------------------------------
id              UUID PK
job_id          UUID FK
question_text   TEXT
question_type   VARCHAR
is_required     BOOLEAN
display_order   INTEGER
created_at      TIMESTAMPTZ
updated_at      TIMESTAMPTZ
```

Question types: `TEXT`, `LONG_TEXT`, `YES_NO`, `NUMBER`, `URL`, etc.

---

## 27. Applications

Table: `applications`

```text
applications
--------------------------------
id                 UUID PK
job_id             UUID FK
profile_id         UUID FK
resume_id          UUID FK NULL
resume_version_id  UUID FK NULL
status             VARCHAR
applied_at         TIMESTAMPTZ
updated_at         TIMESTAMPTZ
withdrawn_at       TIMESTAMPTZ NULL
```

Critical constraint (MVP): `UNIQUE(job_id, profile_id)` to prevent accidental duplicates unless reapplication is explicitly allowed.

---

## 28. Why store both Resume and Resume Version?

When a candidate applies using a particular resume version, we must know what was submitted even if they edit the resume later. Storing both `resume_id` and `resume_version_id` provides traceability.

---

## 29. Application answers

Table: `application_answers`

```text
application_answers
--------------------------------
id               UUID PK
application_id   UUID FK
job_question_id  UUID FK
answer_text      TEXT
created_at       TIMESTAMPTZ
updated_at       TIMESTAMPTZ

UNIQUE(application_id, job_question_id)
```

---

## 30. Application status history

Table: `application_status_history`

```text
application_status_history
--------------------------------
id                 UUID PK
application_id     UUID FK
from_status        VARCHAR NULL
to_status          VARCHAR
changed_by_user_id UUID FK NULL
note               TEXT NULL
created_at         TIMESTAMPTZ
```

This preserves the ATS workflow and timestamps for every state change.

---

## 31. Application notes

Recruiters may need private notes.

Table: `application_notes`

```text
application_notes
--------------------------------
id                 UUID PK
application_id     UUID FK
created_by_user_id UUID FK
note               TEXT
created_at         TIMESTAMPTZ
updated_at         TIMESTAMPTZ
```

Notes must obey employer/company authorization rules.

---

## 32. Interviews

Table: `interviews`

```text
interviews
--------------------------------
id                 UUID PK
application_id     UUID FK
scheduled_at       TIMESTAMPTZ NULL
interview_type     VARCHAR
status             VARCHAR
location_or_link   TEXT NULL
notes              TEXT NULL
created_at         TIMESTAMPTZ
updated_at         TIMESTAMPTZ
```

Potential types: `HR`, `TECHNICAL`, `PROJECT`, `MANAGERIAL`, `CODING`

---

## 33. Candidate availability / preferences

Table: `candidate_preferences`

```text
candidate_preferences
--------------------------------
id                 UUID PK
profile_id         UUID UNIQUE FK
preferred_work_mode VARCHAR NULL
preferred_location  VARCHAR NULL
remote_preference   BOOLEAN
job_search_status   VARCHAR NULL
expected_salary     NUMERIC NULL
notice_period_days  INTEGER NULL
updated_at          TIMESTAMPTZ
```

This supports richer job matching.

---

## 34. Important relationship map

Core model:

```
Firebase
   │
   ▼
 users
   │
   ├── user_roles
   │
   └── profiles
         │
    ┌────┼─────┬─────────┐
    ▼    ▼     ▼         ▼
  education experience projects skills
                      │        │
                      └───┬────┘
                          ▼
                       evidence
                          ▲
                          │
                   GitHub repository
                          ▲
                          │
                  external_accounts
```

Hiring:

```
companies
   │
company_memberships
   │
  jobs
  ├── job_skills
  ├── job_questions
  └── applications
       ├── answers
       ├── status history
       └── interviews
```

Resumes:

```
profiles
  │
 resumes
  │
 resume_versions
```

---

## 35. Final core table list (MVP)

1. users  
2. roles  
3. user_roles  
4. profiles  
5. educations  
6. experiences  
7. projects  
8. skills  
9. profile_skills  
10. project_skills  
11. certifications  
12. achievements  
13. evidence  
14. external_accounts  
15. github_repositories  
16. project_repositories  
17. resumes  
18. resume_versions  
19. companies  
20. company_memberships  
21. jobs  
22. job_skills  
23. job_questions  
24. applications  
25. application_answers  
26. application_status_history  
27. application_notes  
28. interviews  
29. candidate_preferences

This is a substantial but practical MVP schema.

---

## 36. Foreign-key rules

- Establish FK rules explicitly before coding.
- User deletion: don't cascade-delete everything when Firebase/user deletion occurs. Use `ON DELETE RESTRICT` or controlled soft deletion for historical records.
- Profile-owned data: use controlled cascading where appropriate for things like `Profile → Education/Experience/Projects`.
- Applications: avoid cascading deletion of `Job → Applications`. Prefer `status = CLOSED` instead of deleting jobs.

---

## 37. Never delete important business history

Historical records (jobs, applications, application_status_history, etc.) must be retained. Prefer status changes over deletion.

---

## 38. Indexes

Create indexes for frequent lookups. Minimum recommended indexes:

- `users(firebase_uid)`
- `profiles(user_id)`
- `profile_skills(profile_id)`, `profile_skills(skill_id)`
- `project_skills(project_id)`, `project_skills(skill_id)`
- `external_accounts(user_id, provider)`
- `github_repositories(external_account_id)`
- `companies(name)`
- `company_memberships(company_id)`, `company_memberships(user_id)`
- `jobs(company_id)`, `jobs(status)`
- `job_skills(job_id)`, `job_skills(skill_id)`
- `applications(job_id)`, `applications(profile_id)`, `applications(status)`
- `application_status_history(application_id)`
- `application_answers(application_id)`
- `interviews(application_id)`

Don't index everything — base indexes on query patterns.

---

## 39. Data validation rules

Enforce critical invariants in DB (and also validate in application code):

- `experience.start_date <= experience.end_date`
- `job.experience_min <= job.experience_max`
- `job.salary_min <= job.salary_max`
- `job.openings > 0`
- `application.status` must be valid
- `skill.normalized_name` must be unique
- `resume.version_number` unique per resume

Put critical invariants in the database (constraints/triggers) rather than relying only on frontend validation.

---

## 40. Privacy / visibility

Profile fields should have visibility controls (at minimum: `PUBLIC`, `EMPLOYERS`, `PRIVATE`) and backend enforcement. Example items to control:

- GitHub repositories
- Projects
- Skills
- Resumes
- Experience
- Contact information

---

## 41. The biggest architectural rule

- Firebase = Identity  
- PostgreSQL = SkillBridge data  
- Spring Boot = Business rules + authorization  
- Frontend = User interface

Never rely on frontend to grant sensitive access (e.g., "this user is admin, so show admin data"). The backend must decide authorization.

---

## 42. AI data rule

Do not treat AI output as authoritative. AI analysis can create evidence candidates, but verification must follow evidence rules. Example flow:

AI analysis → Evidence candidate → Evidence rules → (Supported / Unverified / Verified)

AI alone should not mark data as `VERIFIED`.

---

## 43. MVP vs Future tables

MVP (do first): the full list from Section 35.

Future (add later):
- colleges, college_memberships
- hackathons
- events
- rankings
- assessments
- interview_sessions, interview_answers
- project_recommendations
- notifications
- reports
- audit_logs

Do not add tables prematurely to the MVP schema.

---

## 44. Schema versioning

Use a migration system (e.g., Flyway for Spring Boot):

- V1__initial_schema.sql
- V2__add_github_tables.sql
- V3__add_application_history.sql
- V4__add_resume_versions.sql

This ensures ordered, auditable DB changes.

---

## 45. Development rule for the whole team

Before creating a new table:

1. Check if the entity already exists.
2. Can an existing relationship solve the need?
3. If not, discuss the schema change (update the design doc).
4. Create a migration.
5. Implement code changes.

This prevents schema sprawl and inconsistent models.

---

### Final note

This formatted document preserves the original content and intent but makes it easier to read, reference, and convert to migration scripts or an ER diagram. Use this as the canonical design doc to drive migrations and application code.
