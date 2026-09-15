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

If Git is installed, you will see a version number.

## Configure Git

Use your own GitHub identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

Use the email associated with your GitHub account where appropriate.

---

# 2. Clone the Repository

Clone SkillBridge to your computer:

```bash
git clone https://github.com/heyvinay-lab/SkillBridge.git
```

Move into the project:

```bash
cd SkillBridge
```

Check the remote:

```bash
git remote -v
```

You should see the SkillBridge GitHub repository.

---

# 3. Always Start From the Latest Main

Before creating a new branch:

```bash
git checkout main
git pull origin main
```

This makes sure your local `main` contains the latest merged work.

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

For a bug:

```text
fix/<short-bug-name>
```

Example:

```bash
git checkout -b fix/mobile-navbar-overflow
```

---

# 5. Check Your Branch

Run:

```bash
git branch
```

The branch with `*` is your current branch.

Example:

```text
  main
* feature/landing-brand-hero
```

If you see `main` with `*`, stop and switch to your feature branch before changing code.

---

# 6. Understand Your GitHub Issue Before Coding

Every task should have a GitHub Issue.

Before writing code:

1. Open your assigned Issue.
2. Read the objective.
3. Read the acceptance criteria.
4. Check the files/components you own.
5. Check dependencies.
6. Check whether another teammate must finish something first.
7. Comment on the Issue if something is unclear.

Do not expand the scope without discussing it with the project lead.

---

# 7. Use AI Correctly

You may use ChatGPT, GitHub Copilot, Claude, Codex, or other AI tools.

AI is an **implementation assistant**, not the owner of the task.

### Before asking AI to code

Give it:

- The GitHub Issue
- Your exact task
- Existing project structure
- Files you are allowed to modify
- SkillBridge design/system rules

### Recommended prompt

```text
You are helping me implement my assigned SkillBridge GitHub Issue.

Read the task requirements carefully and work only within the assigned scope.
Use the existing project structure and design system.
Do not modify another teammate's feature or files unless absolutely required.
Do not invent product requirements.
Do not add unnecessary dependencies.
Create clean, reusable, maintainable code.
After implementation, explain what changed and what I should test.
```

### You are responsible for AI-generated code

Before opening a PR:

- Understand the code.
- Run the application.
- Test your feature.
- Check console errors.
- Check responsive behavior where relevant.
- Remove unused code.
- Remove unnecessary dependencies.
- Make sure the implementation matches the Issue.

**Do not blindly copy AI-generated code.**

---

# 8. Work Only in Your Scope

SkillBridge is being developed by multiple people simultaneously.

Avoid editing another person's files unnecessarily.

For the landing page, the current ownership is:

```text
Person 1
  branding/
  components/brand/
  components/hero/

Person 2
  components/product-story/

Person 3
  components/employer/
  components/ecosystem/

Person 4
  motion/ and final QA changes
```

If you need to change another person's area:

1. Discuss it with the project lead.
2. Explain why the change is required.
3. Keep the change as small as possible.
4. Mention it clearly in the Pull Request.

---

# 9. Save Your Work

Check changed files:

```bash
git status
```

Review the changes:

```bash
git diff
```

Add your changes:

```bash
git add .
```

Commit with a clear message:

```bash
git commit -m "feat(landing): build hero section"
```

Recommended commit prefixes:

```text
feat:     new feature
fix:      bug fix
docs:     documentation
style:    styling only
refactor: code restructuring
test:     tests
chore:    maintenance
```

Examples:

```bash
git commit -m "feat(landing): add SkillBridge hero"
git commit -m "feat(landing): add evidence section"
git commit -m "fix(landing): fix mobile navigation"
```

---

# 10. Push Your Branch

First push:

```bash
git push -u origin feature/your-task-name
```

Later pushes:

```bash
git push
```

Your branch will then be available on GitHub.

---

# 11. Create a Pull Request

Open the SkillBridge repository on GitHub.

Go to:

**Pull requests → New pull request**

Select:

```text
base: main
compare: feature/your-task-name
```

Create the PR.

### PR title format

```text
feat(landing): build hero section
```

### PR description

Use:

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

AI was used for implementation assistance and the generated code was reviewed and tested manually.
```

---

# 12. Code Review

Do not merge your own PR without the required project review process.

The project lead will check:

- Correct scope
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

The existing PR will automatically update.

---

# 13. After Your PR Is Merged

After the PR is merged:

```bash
git checkout main
git pull origin main
```

You can then delete your old local branch:

```bash
git branch -d feature/your-task-name
```

Delete the remote branch if it has not already been deleted through GitHub:

```bash
git push origin --delete feature/your-task-name
```

Only delete a branch after confirming the PR was merged.

---

# 14. Starting Your Next Task

Always update `main` first:

```bash
git checkout main
git pull origin main
```

Then create a new branch:

```bash
git checkout -b feature/next-task
```

Do not continue unrelated work on an old feature branch.

---

# 15. If You Made Changes on the Wrong Branch

Do not panic and do not immediately push.

Check:

```bash
git status
git branch
```

If the changes are uncommitted, contact the project lead before moving them.

If you accidentally committed to `main`, **do not force-push or rewrite shared history**. Tell the project lead so the team can fix it safely.

---

# 16. If `main` Changed While You Were Working

Before opening a PR, update your branch if necessary.

First:

```bash
git checkout main
git pull origin main
```

Then return to your branch:

```bash
git checkout feature/your-task-name
```

If the team lead asks you to update your branch, use the agreed team method. Do not force-push without approval.

For beginners, the safest approach is to ask the project lead before resolving a complicated merge conflict.

---

# 17. Merge Conflict

A conflict means your branch and `main` changed the same part of a file.

Do not randomly delete code to make the conflict disappear.

If you are unsure:

1. Stop.
2. Do not force-push.
3. Send the conflict details to the project lead.
4. Resolve it together.

---

# 18. Landing Page Team Workflow

The current landing-page work is split into four Issues:

### Issue 1 — Brand Identity + Navbar + Hero

Owner: Person 1

```text
feature/landing-brand-hero
```

### Issue 2 — Product Story + Student Experience

Owner: Person 2

```text
feature/landing-product-story
```

### Issue 3 — Employer + College + Ecosystem

Owner: Person 3

```text
feature/landing-employer-ecosystem
```

### Issue 4 — Motion + Responsive + QA

Owner: Person 4

```text
feature/landing-motion-qa
```

Issue 4 should perform the major integrated QA work after Issues 1–3 have usable implementations.

---

# 19. Team Rules

### DO

- Create an Issue before coding.
- Create a separate branch.
- Pull the latest `main` before starting.
- Keep commits focused.
- Test your changes.
- Create a Pull Request.
- Ask for review.
- Keep your work within the assigned scope.
- Use AI responsibly.
- Tell the team when you are blocked.

### DON'T

- Do not push directly to `main`.
- Do not work directly on another person's branch.
- Do not copy another person's implementation without discussion.
- Do not blindly accept AI-generated code.
- Do not add unnecessary libraries.
- Do not change the architecture without approval.
- Do not commit secrets or API keys.
- Do not commit `.env` files containing secrets.
- Do not force-push shared branches.
- Do not close an Issue just because coding is finished; follow the acceptance criteria.

---

# 20. Quick Command Cheat Sheet

```bash
# Clone
git clone https://github.com/heyvinay-lab/SkillBridge.git
cd SkillBridge

# Get latest main
git checkout main
git pull origin main

# Create your branch
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

**Build your assigned part well, communicate with the team, and integrate through Pull Requests.**
