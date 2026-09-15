# SkillBridge

> An evidence-based career intelligence, resume management, and intelligent talent matching platform.

SkillBridge connects students, colleges, and employers through professional profiles, evidence-backed skills, dynamic resumes, job matching, simplified applications, interview preparation, and hiring workflows.

---

## Team Development Guide

This repository is developed by a 5-person team. **Read this before starting any task.**

### Golden Rule

**Never work directly on `main`.**

Every feature or task must be developed on a separate branch and merged through a Pull Request.

```text
main
  │
  ├── feature/your-task
  │       │
  │       ├── code
  │       ├── test
  │       └── commit
  │
  └── Pull Request
          ↓
       Review
          ↓
       Merge
          ↓
         main
```

---

# 1. One-Time Setup

## Install Git

Make sure Git is installed:

```bash
git --version
```

## Configure Git

Use your own GitHub identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

---

# 2. Clone the Repository

```bash
git clone https://github.com/heyvinay-lab/SkillBridge.git
cd SkillBridge
git remote -v
```

---

# 3. Always Start From the Latest Main

Before creating a new branch:

```bash
git checkout main
git pull origin main
```

Always do this before starting a new task.

---

# 4. Create Your Branch

Create a branch specifically for your assigned GitHub Issue.

Recommended naming:

```text
feature/<short-task-name>
```

Examples:

```bash
git checkout -b feature/landing-brand-hero
git checkout -b feature/landing-product-story
git checkout -b feature/landing-employer-ecosystem
git checkout -b feature/landing-motion-qa
```

For bugs:

```bash
git checkout -b fix/mobile-navbar-overflow
```

---

# 5. Check Your Branch

```bash
git branch
```

The branch with `*` is your current branch.

```text
  main
* feature/landing-brand-hero
```

If `main` has `*`, stop and switch to your feature branch before changing code.

---

# 6. Understand Your GitHub Issue Before Coding

Every task should have a GitHub Issue.

Before writing code:

1. Open your assigned Issue.
2. Read the objective.
3. Read the acceptance criteria.
4. Check the files/folder you own.
5. Check dependencies.
6. Check whether another teammate must finish something first.
7. Ask the project lead if anything is unclear.

Do not expand the scope without discussing it with the project lead.

---

# 7. Feature Folder Separation — IMPORTANT

**Each major feature must have its own folder.**

Do not put every feature's code into one shared folder.

The purpose is to allow multiple teammates and AI tools to work independently with fewer conflicts.

### Example

```text
frontend/
├── landing/
│   ├── branding/
│   ├── hero/
│   ├── product-story/
│   ├── employer/
│   ├── ecosystem/
│   └── motion/
│
├── student/
│   ├── dashboard/
│   ├── profile/
│   ├── evidence/
│   ├── resumes/
│   ├── jobs/
│   └── applications/
│
├── employer/
│   ├── dashboard/
│   ├── jobs/
│   ├── candidates/
│   ├── ats/
│   └── interviews/
│
└── shared/
    ├── components/
    ├── hooks/
    ├── utils/
    └── types/
```

The exact structure may evolve as the application grows, but the principle remains:

> **One feature = one clear folder/module.**

### Before creating a new folder

Check whether the feature already has an appropriate folder.

Do not create duplicates such as:

```text
resume/
resumes/
resume-builder/
resume-feature/
```

Choose one agreed location and keep it consistent.

### Shared code

If something is genuinely reusable across multiple features, put it in the agreed shared location.

Example:

```text
shared/components/Button
shared/components/Modal
shared/hooks/useAuth
shared/utils/formatDate
```

Do not put feature-specific code into `shared/` just because it is convenient.

---

# 8. Feature Ownership

Every feature has an owner.

The owner is responsible for:

- Implementation
- Understanding the code
- Testing
- AI-generated code review
- Documentation where required
- Pull Request
- Fixing review comments

For the landing page:

```text
Person 1
  frontend/landing/branding/
  frontend/landing/hero/

Person 2
  frontend/landing/product-story/

Person 3
  frontend/landing/employer/
  frontend/landing/ecosystem/

Person 4
  frontend/landing/motion/
  final landing-page QA
```

The project lead owns final integration and review.

---

# 9. Use AI Correctly

You may use ChatGPT, GitHub Copilot, Claude, Codex, or other AI tools.

AI is an **implementation assistant**, not the owner of the task.

Before asking AI to code, give it:

- The GitHub Issue
- Your exact task
- Existing project structure
- Your feature folder
- Files you are allowed to modify
- SkillBridge design/system rules

### Recommended prompt

```text
You are helping me implement my assigned SkillBridge GitHub Issue.

Read the issue carefully and work only within my assigned feature folder.
Use the existing SkillBridge project structure and design system.
Do not modify another teammate's feature folder.
Do not invent product requirements.
Do not add unnecessary dependencies.
Create clean, reusable, maintainable code.
After implementation, explain what changed and what I should test.
```

### AI responsibility rule

The person assigned to the Issue is responsible for all AI-generated code.

Before opening a PR:

- Understand the code.
- Run the application.
- Test the feature.
- Check console errors.
- Check responsive behavior where relevant.
- Remove unused code.
- Remove unnecessary dependencies.
- Confirm the code follows the Issue.

**Never blindly accept AI-generated code.**

---

# 10. Work Only in Your Scope

Do not edit another teammate's feature folder unnecessarily.

If another feature needs a change:

1. Discuss it with the project lead.
2. Explain why it is needed.
3. Keep the change minimal.
4. Mention it clearly in the Pull Request.

This rule is especially important when multiple people are using AI coding tools.

---

# 11. Save Your Work

Check changed files:

```bash
git status
```

Review changes:

```bash
git diff
```

Stage:

```bash
git add .
```

Commit:

```bash
git commit -m "feat(landing): build hero section"
```

Recommended prefixes:

```text
feat:     new feature
fix:      bug fix
docs:     documentation
style:    styling only
refactor: code restructuring
test:     tests
chore:    maintenance
```

---

# 12. Push Your Branch

First push:

```bash
git push -u origin feature/your-task-name
```

Later:

```bash
git push
```

---

# 13. Create a Pull Request

On GitHub:

**Pull requests → New pull request**

Use:

```text
base: main
compare: feature/your-task-name
```

### PR title

```text
feat(landing): build hero section
```

### PR description

```md
## What changed

- Added SkillBridge hero section
- Added responsive layout
- Added primary and secondary CTAs

## Related Issue

Closes #ISSUE_NUMBER

## Testing

- [x] Desktop tested
- [x] Mobile tested
- [x] No console errors
- [x] Build passes

## AI usage

AI was used for implementation assistance. The generated code was reviewed,
understood, and tested manually.
```

---

# 14. Code Review

Do not merge your own PR without the project review process.

The project lead checks:

- Correct scope
- Feature-folder separation
- Code quality
- Design consistency
- Security where relevant
- Responsive behavior
- Tests
- Unnecessary changes
- AI-generated code quality

If changes are requested:

```bash
git add .
git commit -m "fix: address review feedback"
git push
```

The existing PR will update automatically.

---

# 15. After Your PR Is Merged

```bash
git checkout main
git pull origin main
```

Then you may delete your old local branch:

```bash
git branch -d feature/your-task-name
```

Only delete a branch after confirming the PR was merged.

---

# 16. Starting Your Next Task

```bash
git checkout main
git pull origin main
git checkout -b feature/next-task
```

Start every new task from the latest `main`.

---

# 17. If You Work on the Wrong Branch

Check:

```bash
git status
git branch
```

If you accidentally committed to `main`, **do not force-push or rewrite shared history**.
Tell the project lead so it can be fixed safely.

---

# 18. If `main` Changes While You Work

Before opening a PR, make sure your branch is compatible with the latest `main`.

```bash
git checkout main
git pull origin main
git checkout feature/your-task-name
```

If you are not comfortable resolving conflicts, ask the project lead before doing so.

Never force-push a shared branch without approval.

---

# 19. Merge Conflicts

A conflict means two branches changed the same part of a file.

Do not randomly delete code to remove the conflict.

If unsure:

1. Stop.
2. Do not force-push.
3. Tell the project lead.
4. Resolve the conflict carefully.
5. Test the affected feature.

---

# 20. Landing Page Team Workflow

The current landing page is divided into four Issues:

### Issue 1 — Brand Identity + Navbar + Hero

Branch:

```text
feature/landing-brand-hero
```

Folders:

```text
frontend/landing/branding/
frontend/landing/hero/
```

### Issue 2 — Product Story + Student Experience

Branch:

```text
feature/landing-product-story
```

Folder:

```text
frontend/landing/product-story/
```

### Issue 3 — Employer + College + Ecosystem

Branch:

```text
feature/landing-employer-ecosystem
```

Folders:

```text
frontend/landing/employer/
frontend/landing/ecosystem/
```

### Issue 4 — Motion + Responsive + QA

Branch:

```text
feature/landing-motion-qa
```

Folder:

```text
frontend/landing/motion/
```

Issue 4 should do the major integrated QA after Issues 1–3 have usable implementations.

---

# 21. Team Rules

### DO

- Create an Issue before coding.
- Create a separate branch.
- Pull the latest `main` before starting.
- Work inside your assigned feature folder.
- Keep commits focused.
- Test your changes.
- Create a Pull Request.
- Ask for review.
- Use AI responsibly.
- Communicate when blocked.

### DON'T

- Do not push directly to `main`.
- Do not work directly on another person's branch.
- Do not modify another feature folder unnecessarily.
- Do not blindly accept AI-generated code.
- Do not add unnecessary libraries.
- Do not change architecture without approval.
- Do not commit secrets or API keys.
- Do not commit `.env` files containing secrets.
- Do not force-push shared branches.
- Do not close an Issue before its acceptance criteria are satisfied.

---

# 22. Quick Command Cheat Sheet

```bash
# Clone
git clone https://github.com/heyvinay-lab/SkillBridge.git
cd SkillBridge

# Latest main
git checkout main
git pull origin main

# New feature branch
git checkout -b feature/your-task

# Check status
git status

# Review changes
git diff

# Stage
git add .

# Commit
git commit -m "feat: describe your change"

# Push
git push -u origin feature/your-task

# After PR merge
git checkout main
git pull origin main
```

---

# Development Philosophy

SkillBridge is a team project. The goal is not simply to produce code quickly.

We want code that is:

- Understandable
- Maintainable
- Secure
- Tested
- Consistent
- Reusable
- Easy for another teammate to continue

**Build your assigned feature in its own folder, communicate with the team, and integrate through Pull Requests.**
