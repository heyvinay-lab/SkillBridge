# SkillBridge — UI/UX Specification

**Version:** 1.0  
**Status:** Design Foundation  
**Product:** SkillBridge  
**Repository:** `heyvinay-lab/SkillBridge`

---

# 1. Purpose

This document defines the user experience, information architecture, navigation, screen structure, interaction patterns, design system principles, responsive behavior, accessibility expectations, and UI states for SkillBridge.

The purpose is to ensure that:

- Student and employer experiences are clearly separated.
- All major workflows have a defined interface.
- The frontend team designs from the same specification.
- Backend/API requirements can be mapped to screens.
- New screens are not created arbitrarily.
- UI decisions remain consistent across the product.

This document is the UI/UX contract for the frontend team.

---

# 2. Product UX Vision

SkillBridge should not feel like:

> "Another resume builder."

It should feel like a **career workspace** where professional information continuously moves through:

Build → Prove → Present → Discover → Apply → Interview → Improve

The core UX loop is:

                Student
                   ↓
            Professional Profile
                   ↓
          Skills + Projects + Evidence
                   ↓
                Resume
                   ↓
             Job Discovery
                   ↓
              Job Matching
                   ↓
              Application
                   ↓
              Employer Review
                   ↓
                Interview
                   ↓
                Hiring

The interface should make this journey easy to understand.

---

# 3. Primary UX Goals

SkillBridge UI should optimize for:

## 3.1 Clarity

Users should immediately understand:

- Where they are
- What they can do
- What happened
- What to do next

## 3.2 Trust

Because SkillBridge evaluates evidence and candidate information, the interface must clearly distinguish:

- Self-reported
- Supported
- Verified
- Unverified
- Missing

## 3.3 Efficiency

Repeated career tasks should require minimal unnecessary effort.

Examples:

- Reusing profile information in resumes
- Reusing resumes for applications
- Reusing application information
- Connecting GitHub instead of manually entering every project

## 3.4 User Control

Users must always understand:

- What data is being imported
- What data is visible
- What is being submitted
- What the system is analyzing

## 3.5 Explainability

AI-generated recommendations must show useful reasoning.

---

# 4. UX Principles

## Principle 1 — One Source of Truth

The Master Profile is the user's authoritative professional information.

The resume is generated/presented from that information.

UI relationship:

                Master Profile
                       ↓
          ┌────────────┼────────────┐
          ↓            ↓            ↓
       Resume A     Resume B     Resume C

## Principle 2 — Never Hide Important Consequences

Before an important action, show its effect.

Example:

Before application submission:

- Resume being submitted
- Questions being answered
- Information being shared

## Principle 3 — Evidence Must Be Understandable

Do not show an unexplained:

> Evidence Score: 82

Instead show:

Java

Supported

✓ GitHub project  
✓ Java source code  
✓ Certification

## Principle 4 — AI Is Assistive

AI recommendations should visually communicate that they are recommendations/analysis rather than unquestionable facts.

## Principle 5 — Progressive Disclosure

Do not show every available feature simultaneously.

Simple information first.

Advanced information when requested.

---

# 5. User Types

SkillBridge has three primary user experiences.

## Student

Primary objective:

> Build a credible professional identity and find opportunities.

## Employer

Primary objective:

> Find, evaluate, and hire suitable candidates.

## Admin

Primary objective:

> Operate and moderate the platform.

Each role has its own workspace.

---

# 6. Information Architecture

## Public

    SkillBridge
    ├── Landing
    ├── Jobs
    ├── Job Details
    ├── Login
    └── Registration

## Student

    Student Workspace
    ├── Dashboard
    ├── Profile
    ├── Evidence
    ├── Resumes
    ├── Jobs
    ├── Applications
    ├── Interview
    └── Settings

## Employer

    Employer Workspace
    ├── Dashboard
    ├── Jobs
    ├── Candidates
    ├── Applications
    ├── Interviews
    ├── Company
    └── Settings

## Admin

    Admin Workspace
    ├── Dashboard
    ├── Users
    ├── Employers
    ├── Companies
    ├── Reports
    └── Settings

---

# 7. Global Application Structure

Authenticated application screens should generally have:

    ┌───────────────────────────────────────────────┐
    │ Logo                         Notifications     │
    ├──────────────┬────────────────────────────────┤
    │              │                                │
    │ Navigation   │           Content              │
    │              │                                │
    │              │                                │
    │              │                                │
    └──────────────┴────────────────────────────────┘

Desktop:
- Persistent sidebar
- Top bar
- Main content

Mobile:
- Collapsed navigation
- Drawer/menu or appropriate mobile navigation
- Full-width content

The exact mobile navigation pattern will be finalized during wireframing.

---

# 8. Global Navigation Rules

Navigation should clearly indicate:

- Current location
- Available destinations
- Context

The active navigation item must have a visually distinct state.

Avoid deep navigation where possible.

Important actions should not be hidden behind multiple menus without reason.

---

# 9. Student Navigation

Recommended primary navigation:

    Dashboard
    Profile
    Evidence
    Resumes
    Jobs
    Applications
    Interview

Secondary:

    Settings

Potential future features should not appear in the MVP navigation until implemented.

---

# 10. Employer Navigation

Recommended:

    Dashboard
    Jobs
    Candidates
    Applications
    Interviews
    Company

Secondary:

    Settings

---

# 11. Admin Navigation

Recommended:

    Dashboard
    Users
    Employers
    Companies
    Reports

Secondary:

    Settings

---

# 12. Student Dashboard

## Goal

The dashboard should answer:

1. How complete is my professional profile?
2. How strong is my supporting evidence?
3. What opportunities are relevant?
4. What should I do next?

Suggested structure:

    Welcome back

    ┌─────────────────────┐
    │ Profile Completion  │
    │ 82%                 │
    └─────────────────────┘

    ┌─────────────────────┐
    │ Evidence            │
    │ 12 Supported        │
    └─────────────────────┘

    Recommended Opportunities

    ┌────────────────────────────────────────────┐
    │ Java Backend Developer                     │
    │ Strong Match                               │
    │ Java ✓ Spring Boot ✓ Docker △              │
    │                                            │
    │ [View Job]                                 │
    └────────────────────────────────────────────┘

    Recommended Next Steps

    - Add evidence for Spring Boot
    - Improve Docker
    - Update backend resume

---

# 13. Dashboard Priority

The dashboard must prioritize:

1. Actionable career information
2. Relevant opportunities
3. Important alerts
4. Progress
5. Secondary statistics

Avoid turning the dashboard into a wall of charts.

---

# 14. Profile Screen

The Profile screen represents the Master Profile.

Recommended sections:

    Profile Header
    About
    Education
    Experience
    Skills
    Projects
    Certifications
    Achievements
    External Profiles
    Preferences

Every section should provide appropriate:

    View
    Add
    Edit
    Delete
    Visibility

actions.

---

# 15. Profile Header

Should show:

- Name
- Professional headline
- Profile photo
- Location where applicable
- Portfolio
- Profile visibility/status

Primary action:

    Edit Profile

Secondary actions may include:

    Preview Profile

---

# 16. Education UI

Each education record should appear as a structured item.

Example:

    B.Tech — Computer Science & Engineering
    Budge Budge Institute of Technology
    2023 — 2027
    CGPA: ...

    [Edit]

The interface should support adding multiple education records.

---

# 17. Experience UI

Example:

    Software Engineering Intern
    Company Name
    Jun 2026 — Aug 2026

    Description...

    [Edit]

Current roles must clearly show that they are ongoing.

---

# 18. Projects UI

Projects are a major SkillBridge entity.

Example:

    SkillBridge
    Evidence-based career platform

    Technologies
    Java  Spring Boot  PostgreSQL  React

    GitHub
    Live Demo

    Evidence
    4 supporting items

    [View] [Edit]

Projects should surface evidence connections where useful.

---

# 19. Skills UI

Skills should be easy to scan.

Example:

    Skills

    Programming
    [Java] [Python] [JavaScript]

    Frameworks
    [Spring Boot] [React]

    Databases
    [PostgreSQL]

Selecting a skill should allow the user to inspect supporting evidence.

---

# 20. Skill Detail

Example:

    Java

    Support Level
    ████████░░

    Supporting Evidence

    ✓ SkillBridge Project
    ✓ GitHub Repository
    ✓ Certification

    Self Reported
    ✓ 2 years

    [View Evidence]

The interface must clearly distinguish **support** from **verification**.

---

# 21. Evidence Center

The Evidence screen is a first-class SkillBridge feature.

Suggested structure:

    Evidence Center

    Overview

    Supported        14
    Verified          4
    Unverified        5
    Needs Review      3

    Needs Your Attention

    ⚠ Performance improvement claim
      Supporting evidence unavailable

      [Review]

    Recent Evidence

    ✓ Java
    ✓ Spring Boot
    ✓ PostgreSQL

---

# 22. Evidence Detail

Example:

    Evidence

    Skill
    Spring Boot

    Source
    GitHub Repository

    Repository
    SkillBridge Backend

    Supporting information
    ...

    Status
    Supported

    Why?
    Relevant Spring Boot project information was found.

    [Accept]
    [Reject]

The interface must never imply certainty that does not exist.

---

# 23. Evidence Status Design

Use both:

- Text
- Visual indicator

Possible statuses:

    SELF-REPORTED
    SUPPORTED
    VERIFIED
    UNVERIFIED
    REJECTED

Do not communicate status through color alone.

---

# 24. GitHub Connection

Initial disconnected state:

    Connect GitHub

    Connect your GitHub account to help SkillBridge
    identify projects, technologies and supporting evidence.

    [Connect GitHub]

    Privacy information

---

Connected state:

    GitHub Connected

    Last Synced
    Today, 10:42 AM

    18 repositories

    [Review Repositories]
    [Sync Now]
    [Disconnect]

---

# 25. Repository Review

Imported repositories should not automatically become profile projects.

Example:

    GitHub Repositories

    ☑ SkillBridge
    ☑ E-Commerce API
    ☐ Old College Project
    ☐ Test Repository

    [Import Selected]

The user must be able to review and reject imported information.

---

# 26. Resume Center

The Resume screen should be a workspace.

    Resumes

    [+ Create Resume]

    ┌─────────────────────────────┐
    │ Java Backend Resume         │
    │ Version 4                   │
    │ Updated 2 hours ago        │
    │                             │
    │ [Edit] [Preview] [...]      │
    └─────────────────────────────┘

    ┌─────────────────────────────┐
    │ SDE Resume                  │
    │ Version 2                   │
    │ Updated yesterday            │
    │                             │
    │ [Edit] [Preview] [...]      │
    └─────────────────────────────┘

---

# 27. Create Resume

Flow:

    Create Resume
          ↓
    Resume Name
          ↓
    Target Role
          ↓
    Template
          ↓
    Choose Profile Information
          ↓
    Create Draft
          ↓
    Resume Editor

---

# 28. Resume Editor

The editor should prioritize content editing and preview.

Desktop concept:

    ┌───────────────┬─────────────────────────────┐
    │ Sections      │ Resume Preview              │
    │               │                             │
    │ ☰ Summary     │       VINAY KUMAR           │
    │ ☰ Skills      │       Software Engineer    │
    │ ☰ Experience  │                             │
    │ ☰ Projects    │       SUMMARY               │
    │ ☰ Education   │       ...                   │
    │               │                             │
    │ [+ Add]       │       EXPERIENCE            │
    │               │       ...                   │
    └───────────────┴─────────────────────────────┘

Primary actions:

    Save
    Preview
    Download
    Version History

---

# 29. Resume Editing Rules

The editor should make clear whether content is:

- From Master Profile
- Manually edited
- Recommended by SkillBridge
- Missing evidence

AI-generated suggestions should not overwrite verified/user-confirmed information without explicit action.

---

# 30. Resume Version History

Example:

    Version History

    v4   Current
         Sep 15, 2026

    v3
         Sep 10, 2026

    v2
         Sep 05, 2026

    v1
         Sep 01, 2026

Actions:

    Preview
    Compare
    Restore
    Duplicate

Restoring should create an appropriate new version rather than silently destroying historical versions.

---

# 31. Resume Truth Checker

The user should see a structured report.

    Resume Analysis

    Claims Reviewed
    24

    Supported
    18

    Needs Evidence
     4

    Potential Issues
     2

    ──────────────────────────────

    ⚠ "Improved performance by 40%"

    Evidence not found.

    [Review Claim]

The interface should never generate a replacement number without user-provided evidence.

---

# 32. Job Discovery

Students should have a dedicated Jobs workspace.

Suggested structure:

    Jobs

    Search jobs...

    Filters
    Location
    Work Mode
    Experience
    Skills
    Employment Type

    Recommended
    All Jobs
    Saved Jobs

---

# 33. Job Card

Example:

    Java Backend Developer
    Company Name

    Kolkata · Hybrid

    Strong Match

    Java ✓
    Spring Boot ✓
    PostgreSQL ✓
    Docker △

    [View Job]
    [Save]

The card should prioritize information needed for decision-making.

---

# 34. Job Details

Structure:

    Job Title
    Company
    Location
    Work Mode
    Employment Type

    About the Role

    Requirements

    Required Skills
    Preferred Skills

    Responsibilities

    Your Match

    Strengths
    Gaps
    Evidence

    [Apply]

The application CTA should remain visible at appropriate points.

---

# 35. Job Intelligence

Job analysis should be understandable to non-technical users.

Example:

    Job Intelligence

    Role
    Java Backend Developer

    Required Skills

    ✓ Java
    ✓ Spring Boot
    ✓ PostgreSQL

    Preferred

    ○ Docker
    ○ AWS

    Experience
    1–3 years

---

# 36. Job Match

The matching screen should show:

    Your Match

    Strong Areas
    ✓ Java
    ✓ Spring Boot

    Partial
    △ Docker

    Missing
    ✕ AWS

    Supporting Evidence
    ✓ Project
    ✓ GitHub
    ✓ Certification

    Improvement Suggestions
    ...

A score can be displayed, but it must never replace the explanation.

---

# 37. Application Flow

The application flow should be sequential.

    Job
      ↓
    Apply
      ↓
    Select Resume
      ↓
    Questions
      ↓
    Review
      ↓
    Submit

The user should always know their current step.

---

# 38. Resume Selection During Application

Example:

    Select Resume

    ○ Java Backend Resume
      Version 4

    ○ SDE Resume
      Version 2

    ○ Internship Resume
      Version 3

    [Continue]

The user must explicitly select the resume.

---

# 39. Application Review

Before submission:

    Review Application

    Job
    Java Backend Developer

    Resume
    Java Backend Resume — Version 4

    Information

    ✓ Profile
    ✓ Education
    ✓ Experience
    ✓ Projects

    Questions

    ✓ All required questions answered

    [Back]
    [Submit Application]

This is a mandatory confirmation step.

---

# 40. Application Tracking

Possible views:

### List view

    Java Developer
    Company A
    Applied Sep 15
    Status: Shortlisted

### Pipeline view

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

The final design will determine whether list, timeline, or Kanban should be primary.

---

# 41. Application Details

Should include:

    Job
    Company
    Resume Used
    Application Date
    Current Status
    Submitted Information
    Interview Information
    Status History

The student's view must not expose employer-private notes.

---

# 42. Employer Dashboard

The employer dashboard should answer:

1. How many active jobs exist?
2. How many new applications arrived?
3. How many candidates are shortlisted?
4. What interviews require attention?

Example:

    Good morning

    Active Jobs       4
    New Applications 28
    Shortlisted       7
    Interviews        3

    Recent Job Activity

    Java Backend Developer
    14 applications

---

# 43. Company Screen

Employer should have:

    Company name
    Logo
    Description
    Website
    Industry
    Location

    Company Members

The interface should clearly show membership/permission where relevant.

---

# 44. Create Job

Use a multi-step flow.

    Create Job

    1. Basic Information
    2. Requirements
    3. Skills
    4. Questions
    5. Preview
    6. Publish

This is preferable to exposing every field at once.

---

# 45. Job Creation — Basic Information

Fields may include:

    Job Title
    Description
    Employment Type
    Work Mode
    Location
    Experience
    Salary
    Openings
    Application Deadline

Required fields must be visibly marked.

---

# 46. Job Creation — Skills

Separate:

    Required Skills

and:

    Preferred Skills

Example:

    Required
    [Java]
    [Spring Boot]
    [PostgreSQL]

    Preferred
    [Docker]
    [AWS]

The interface should prevent accidental duplication.

---

# 47. Job Creation — Custom Questions

Employer can add:

    Question text
    Question type
    Required / Optional
    Display order

Example:

    Do you have experience with Spring Boot?

    Type: Yes / No
    Required: Yes

---

# 48. Job Preview

Before publishing:

    Java Backend Developer
    Company Name

    Location
    Work Mode
    Experience

    Required Skills
    ...

    Preferred Skills
    ...

    Questions
    ...

    [Back]
    [Publish Job]

Publishing is a meaningful action and should be explicit.

---

# 49. Candidate Search

Candidate search should prioritize:

    Search

    Filters

    Candidate Cards

Possible filters:

    Skills
    Experience
    Graduation Year
    Location
    Certifications
    Projects
    Evidence
    Availability

---

# 50. Candidate Card

Example:

    Vinay Kumar
    Java Backend Developer

    Java · Spring Boot · PostgreSQL

    Evidence
    Strong

    Relevant Projects
    3

    Relevant Experience
    1

    [View Profile]
    [Shortlist]

The UI should avoid making unsupported judgments such as:

> "Excellent candidate."

Instead show evidence and relevant facts.

---

# 51. Employer Candidate Profile

Structure:

    Candidate Header

    Professional Summary

    Skills

    Evidence

    Projects

    Experience

    Education

    Certifications

    Resume

    Actions

    [Shortlist]
    [Reject]

Only employer-visible information should be shown.

---

# 52. ATS

Employer applications should have a clear pipeline:

    Screening
    Shortlisted
    Assessment
    Interview
    Selected
    Hired

A Kanban layout is a strong candidate for this workspace:

    ┌────────────┬──────────────┬────────────┬─────────────┐
    │ Screening  │ Shortlisted  │ Interview  │ Selected    │
    │            │              │            │             │
    │ Candidate  │ Candidate    │ Candidate  │ Candidate   │
    │ Candidate  │ Candidate    │            │             │
    └────────────┴──────────────┴────────────┴─────────────┘

The backend remains responsible for validating status transitions.

---

# 53. Interview Screen

Employer:

    Candidate
    Interview Type
    Date/Time
    Meeting Link
    Status

Student:

    Company
    Job
    Interview Type
    Date/Time
    Meeting Information

Privacy must be respected for each side.

---

# 54. Admin Dashboard

Admin should focus on platform operations.

Example:

    Users
    12,453

    Students
    11,900

    Employers
    553

    Active Jobs
    426

    Reports
    7

Exact statistics depend on available backend data.

---

# 55. UI State Requirements

Every major screen must define at least:

    Loading
    Success
    Empty
    Error
    Disabled
    Permission denied

Do not design only the "perfect data" state.

---

# 56. Loading State

Use skeletons or appropriate progress indicators.

Avoid displaying:

> "Loading..."

for long periods without context.

Examples:

    Resume list
    [████████████]

    Candidate list
    [████████████]

The exact loading pattern should depend on component behavior.

---

# 57. Empty States

Every list needs an intentional empty state.

Example:

    No Resumes Yet

    Create your first resume from your professional profile.

    [+ Create Resume]

Bad empty state:

    No data.

---

# 58. Error States

Errors should explain:

- What happened
- Whether the data was saved
- What the user can do next

Example:

    GitHub synchronization failed.

    Your existing data is safe.

    [Try Again]

Do not show raw backend stack traces.

---

# 59. Form Validation

Validation should appear close to the relevant field.

Example:

    Email
    [vinay@]

    Invalid email format.

Validation should exist in both frontend and backend.

The UI should never imply that frontend validation alone guarantees acceptance.

---

# 60. Destructive Actions

Actions such as:

    Delete Resume
    Disconnect GitHub
    Delete Evidence
    Deactivate Account

should have appropriate confirmation.

Confirmation should explain the consequence.

Example:

    Disconnect GitHub?

    Your previously imported data will remain,
    but future synchronization will stop.

    [Cancel] [Disconnect]

---

# 61. Notifications

Notifications may include:

- Application status changes
- Interview schedules
- GitHub sync completion/failure
- Evidence review requests
- Important employer actions

Notification UI should distinguish:

    Unread
    Read

Do not overwhelm users with notifications for every trivial event.

---

# 62. Toasts

Use toasts for short-lived feedback such as:

    Profile saved
    Resume saved
    GitHub synchronized

Do not put critical information only in a toast.

---

# 63. Modals

Use modals for focused actions.

Good:

    Delete confirmation
    Quick edit
    Short action

Avoid putting large, complex workflows inside modals.

Large workflows should use dedicated screens or drawers.

---

# 64. Search

Search should:

- Show what is being searched
- Support clear results
- Offer appropriate filtering
- Handle zero results

Example:

    Search candidates...

    No candidates found.

    Try:
    - Another skill
    - Broader experience range
    - Fewer filters

---

# 65. Tables

Tables should be used where users need to compare structured records.

Examples:

    Employer applications
    Admin users
    Company members

Avoid huge tables on mobile.

---

# 66. Cards

Cards are suitable for:

    Jobs
    Candidates
    Resumes
    Projects
    Evidence

Cards should remain compact and scannable.

---

# 67. Typography

The final design system should define:

    Display
    H1
    H2
    H3
    Body
    Small
    Caption

Typography should create clear information hierarchy.

Avoid excessive font sizes or inconsistent typography between modules.

---

# 68. Color System

The final color palette must define:

    Primary
    Secondary
    Background
    Surface
    Border
    Text
    Success
    Warning
    Error
    Informational

Status should not rely entirely on color.

For example:

    ✓ Supported
    △ Limited
    ✕ Missing

rather than green/yellow/red alone.

---

# 69. Spacing

The team should use a consistent spacing scale rather than arbitrary values.

All major components should follow the same spacing rhythm.

---

# 70. Component System

Core reusable components should include:

    Button
    Input
    Textarea
    Select
    Checkbox
    Radio
    Switch
    Card
    Badge
    Modal
    Drawer
    Tabs
    Table
    Dropdown
    Toast
    Tooltip
    Progress
    Skeleton
    Pagination
    Empty State
    Error State

Build reusable components instead of recreating slightly different versions for each page.

---

# 71. Button Hierarchy

The visual design should distinguish:

    Primary
    Secondary
    Tertiary
    Destructive

For example:

Primary:
    Apply

Secondary:
    Save

Tertiary:
    Cancel

Destructive:
    Delete

Do not make every button visually dominant.

---

# 72. Forms

Forms should:

- Group related information
- Use meaningful labels
- Indicate required fields
- Explain errors
- Preserve user input after validation errors where possible
- Prevent accidental loss of entered information

Long forms should be broken into logical sections.

---

# 73. Responsive Design

Required targets:

    Mobile
    Tablet
    Desktop

The design should adapt rather than simply shrink.

Examples:

Desktop:

    Sidebar + content

Mobile:

    Collapsed navigation
    Full-width content

Tables may become:

    Cards
    Horizontal scrolling
    Or simplified layouts

depending on the content.

---

# 74. Accessibility

The UI should support:

- Keyboard navigation
- Visible focus states
- Meaningful labels
- Accessible form errors
- Adequate contrast
- Screen-reader-friendly controls
- Semantic HTML where applicable

Interactive elements must have understandable accessible names.

---

# 75. Keyboard Behavior

Users should be able to complete important workflows without requiring a mouse.

Especially:

    Login
    Profile editing
    Resume editing
    Job search
    Application submission

---

# 76. Focus Management

After actions such as:

    Open modal
    Submit form
    Display error
    Navigate to next step

focus should move appropriately.

Do not leave keyboard users in an unexpected location.

---

# 77. Resume Editor Accessibility

The resume editor requires special consideration.

Controls should have:

- Keyboard-accessible drag/reorder alternatives
- Clear focus state
- Accessible section controls
- Readable preview

Drag-and-drop must not be the only way to reorder sections.

---

# 78. AI Interaction UX

Whenever AI is involved, display:

    Analyzing...
    Reviewing...
    Generating recommendations...

After completion, show results with explanations.

If analysis fails:

    Analysis unavailable

    Your existing information is safe.

    [Try Again]

AI-generated content should be visually distinguishable from user-confirmed information where necessary.

---

# 79. AI Confidence

Avoid presenting AI confidence as scientific certainty.

For example:

Bad:

    97.8% certainty

unless the underlying methodology genuinely justifies that interpretation.

Prefer understandable descriptions such as:

    Strong supporting evidence
    Limited evidence
    Evidence unavailable

when appropriate.

---

# 80. Privacy UX

Whenever information becomes visible to employers, the UI should make the visibility state understandable.

Example:

    Profile Visibility

    Employers can view selected professional information.

    [Manage Visibility]

For connected services:

    GitHub Connected

    Private repositories are not automatically exposed.

---

# 81. Application Privacy UX

Before applying:

    Information being shared

    ✓ Name
    ✓ Education
    ✓ Selected resume
    ✓ Relevant profile data

This creates user trust.

---

# 82. Employer Privacy UX

Employer-only information should be visually separated.

Example:

    Internal Notes

    Visible only to your hiring team.

Students must never see this area.

---

# 83. Security-Related UX

Security errors should be generic.

Example:

    You don't have permission to view this resource.

Do not disclose unnecessary information such as:

    "This resource belongs to another user."

when that disclosure itself could reveal private information.

---

# 84. Navigation Depth

Important tasks should generally be reachable without excessive navigation.

Target principle:

    Dashboard
      ↓
    Feature
      ↓
    Action

Avoid unnecessary chains such as:

    Dashboard
      ↓
    Menu
      ↓
    Submenu
      ↓
    Secondary menu
      ↓
    Action

---

# 85. User Journey — Student

Primary journey:

    Sign Up
       ↓
    Profile
       ↓
    Add Education
       ↓
    Add Skills
       ↓
    Add Projects
       ↓
    Connect GitHub
       ↓
    Review Evidence
       ↓
    Build Resume
       ↓
    Discover Job
       ↓
    Analyze Match
       ↓
    Apply
       ↓
    Track Application

This is the primary UX journey to optimize.

---

# 86. User Journey — Employer

Primary journey:

    Sign Up
       ↓
    Create Company
       ↓
    Create Job
       ↓
    Publish
       ↓
    Receive Applications
       ↓
    Search/View Candidates
       ↓
    Shortlist
       ↓
    Interview
       ↓
    Select
       ↓
    Hire

---

# 87. User Journey — Admin

    Login
      ↓
    Admin Dashboard
      ↓
    Monitor
      ↓
    Review
      ↓
    Moderate
      ↓
    Resolve

Admin functionality should remain separate from student/employer product workflows.

---

# 88. Screen Inventory — MVP

The exact count will change during wireframing, but the initial inventory is:

## Public

    1. Landing
    2. Jobs
    3. Job Details
    4. Login
    5. Registration
    6. Password Reset

## Student

    7. Dashboard
    8. Profile
    9. Education Management
    10. Experience Management
    11. Project Management
    12. Skills
    13. Certifications
    14. Achievements
    15. External Accounts
    16. GitHub Review
    17. Evidence Center
    18. Evidence Detail
    19. Resume Center
    20. Create Resume
    21. Resume Editor
    22. Resume Version History
    23. Resume Analysis
    24. Jobs
    25. Job Details
    26. Job Match
    27. Application Review
    28. Applications
    29. Application Details
    30. Interview
    31. Settings

## Employer

    32. Dashboard
    33. Company
    34. Jobs
    35. Create Job
    36. Edit Job
    37. Job Preview
    38. Candidates
    39. Candidate Profile
    40. Applications
    41. Application Details
    42. ATS
    43. Interviews
    44. Settings

## Admin

    45. Dashboard
    46. Users
    47. User Details
    48. Employers
    49. Companies
    50. Reports
    51. Settings

Some of these can be implemented as reusable states/components instead of completely separate routes.

---

# 89. Screen Prioritization

Not all screens have equal importance.

## P0 — Core MVP

    Login
    Student Dashboard
    Profile
    Projects
    Skills
    GitHub
    Evidence
    Resume
    Jobs
    Job Match
    Apply
    Applications
    Employer Dashboard
    Create Job
    Candidates
    Candidate Profile
    ATS

## P1

    Certifications
    Achievements
    Interviews
    Advanced resume analysis
    Admin operations

## P2

    Future platform modules

---

# 90. Design File Structure

The design team should organize design files by:

    00 — Foundations
    01 — Public
    02 — Student
    03 — Employer
    04 — Admin
    05 — Components
    06 — Prototypes
    07 — Archived

The exact design tool/file structure depends on the tool selected by the team.

---

# 91. Design Handoff

A screen is ready for development when it has:

    Final layout
    Component states
    Responsive behavior
    Interaction notes
    Empty state
    Loading state
    Error state
    Validation behavior
    Relevant API/data requirements
    Accessibility considerations

A screenshot alone is not a sufficient developer handoff.

---

# 92. UI/UX → Frontend Handoff

The handoff flow is:

    Requirements
        ↓
    User Flow
        ↓
    Wireframe
        ↓
    High-Fidelity Design
        ↓
    Prototype
        ↓
    UX Review
        ↓
    Developer Handoff
        ↓
    Frontend Implementation
