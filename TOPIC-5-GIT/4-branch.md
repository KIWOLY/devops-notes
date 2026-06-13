# Git Branching Strategies Guide

A comprehensive guide to the most popular Git branching strategies used in professional software development.

---

## What is a Branching Strategy?

A branching strategy is a **set of rules and conventions** that defines how a team creates, names, merges, and manages branches in Git.

It answers critical questions:
- Where do developers write new code?
- How does code move from development → production?
- How are bugs fixed?
- How are releases managed?

---

## Major Git Branching Strategies

---

## Git Flow

The most **popular and structured** strategy. Best for projects with **scheduled releases**.

### Branch Structure

```
main          ─────────────────────────────────────→  (production)
                  ↑                        ↑
release       ───────── v1.0 ──────────────────────→
                  ↑            ↑
develop       ──────────────────────────────────────→ (integration)
               ↑      ↑    ↑        ↑
feature       f/login  f/payment  f/dashboard
```

### Branches Overview

| Branch | Purpose | Lifespan |
|---|---|---|
| `main` | Production-ready code | Permanent |
| `develop` | Integration branch | Permanent |
| `feature/*` | New features | Temporary |
| `release/*` | Release preparation | Temporary |
| `hotfix/*` | Emergency bug fixes | Temporary |

### Workflow Example

```bash
# 1. Start a feature
git checkout develop
git switch -c feature/login

# 2. Work and commit
git add .
git commit -m "Add login feature"

# 3. Merge back to develop
git switch develop
git merge --no-ff feature/login
git branch -d feature/login

# 4. Create release branch
git switch -c release/1.0 develop

# 5. Fix bugs on release, then merge to main + develop
git switch main
git merge --no-ff release/1.0
git tag -a v1.0

git switch develop
git merge --no-ff release/1.0
git branch -d release/1.0

# 6. Hotfix (emergency fix in production)
git switch -c hotfix/1.0.1 main
git commit -m "Fix critical bug"
git switch main
git merge --no-ff hotfix/1.0.1
git switch develop
git merge --no-ff hotfix/1.0.1
```

### Full Git Flow Diagram

```
main     ──●───────────────────────●────────●───→
            \                     /|hotfix  |
             \                   / └──●──●──┘
release       \             ────●
               \           /    \
develop    ─────●──●──●──●────────●──●──────────→
                   \  \  \      /
feature             ●  ●  ●────
                   f1  f2  f3
```

**Best for:** Apps with versioned releases (mobile apps, desktop software)  
**Avoid when:** You need continuous deployment

---

## GitHub Flow

A **simpler, lightweight** strategy. Best for **continuous deployment**.

### Branch Structure

```
main      ────────────────────────────────────────→ (always deployable)
               ↑          ↑           ↑
feature    f/login    f/payment    f/search
```

### Core Rules

1. `main` is always deployable
2. New work = new branch → Pull Request → merge to main

### Workflow Example

```bash
# 1. Create a branch from main
git switch -c feature/user-profile

# 2. Make commits
git add .
git commit -m "Add user profile page"

# 3. Push to GitHub
git push origin feature/user-profile

# 4. Open Pull Request on GitHub
# → Team reviews code
# → CI/CD runs tests

# 5. Merge to main after approval
git switch main
git merge feature/user-profile

# 6. Deploy immediately
git push origin main
```

### Diagram

```
main    ──●──────────────────●──────────●──────→
           \                /            \    /
            ●──●──●──●─────           ●──●──●
           feature/login              feature/pay
```

**Best for:** Web apps, SaaS, continuous delivery teams  
**Avoid when:** You need to manage multiple versions

---

## GitLab Flow

A **middle ground** between Git Flow and GitHub Flow. Adds **environment branches**.

### Branch Structure

```
main        ──────────────────────────────────→
                ↓            ↓
pre-production  ────────────────────────────→
                                 ↓
production      ────────────────────────────→
```

### Workflow Example

```bash
# Feature development
git switch -c feature/search
git commit -m "Add search feature"

# Merge to main via PR
git switch main
git merge feature/search

# Promote to pre-production
git switch pre-production
git merge main

# Promote to production
git switch production
git merge pre-production
```

### Diagram

```
feature   ●──●──●
               ↓ PR
main      ─────●──────────────────────────→
               ↓ merge when ready
staging   ─────────●──────────────────────→
               ↓ after testing
production─────────────●──────────────────→
```

**Best for:** Teams needing staging/QA environments  
**Avoid when:** Simple projects without multiple environments

---

## Trunk Based Development (TBD)

Everyone commits **directly to main** (trunk). Very short-lived branches (< 1 day).

### Branch Structure

```
main/trunk  ──●──●──●──●──●──●──●──●──────→
                        \  /
                    short feature branch
                      (< 1 day)
```

### Workflow Example

```bash
# Option 1: Commit directly to main
git switch main
git add .
git commit -m "Small change"
git push origin main

# Option 2: Very short branch
git switch -c fix/button-color
git commit -m "Fix button color"
git push origin fix/button-color
# → PR → merge same day
```

### Key Practices

- **Feature Flags** hide incomplete features
- Very small, frequent commits
- Strong CI/CD pipeline required
- Code review via pair programming

### Feature Flag Example

```bash
# In your code
if (featureFlags.newDashboard) {
    showNewDashboard();
} else {
    showOldDashboard();
}
```

**Best for:** Large teams, Google/Facebook scale, experienced developers  
**Avoid when:** Team is small or inexperienced

---

## Release Branching

Maintain **separate branches for each release version**.

### Branch Structure

```
main        ──●──●──●──●──●──────────────────→
               \         \
release/1.x     ●──●──●   \
(v1.0, v1.1)               \
release/2.x                 ●──●──●
(v2.0, v2.1)
```

**Best for:** Open source projects, libraries, APIs  
**Avoid when:** Single-version web apps

---

## Strategy Comparison

| Strategy | Complexity | Release Style | Team Size | Best For |
|---|---|---|---|---|
| Git Flow | High | Scheduled | Medium-Large | Versioned software |
| GitHub Flow | Low | Continuous | Any | Web/SaaS apps |
| GitLab Flow | Medium | Environment-based | Medium | Multi-environment |
| Trunk Based | Low-Medium | Continuous | Large | High-velocity teams |
| Release Branch | Medium | Per version | Any | Libraries/OSS |

---

## Branch Naming Conventions

Standard naming patterns for consistent branch organization:

```bash
# Features
feature/user-authentication
feature/payment-integration
feat/login-page

# Bug fixes
fix/login-error
bugfix/null-pointer
hotfix/critical-payment-bug

# Releases
release/1.0.0
release/2024-Q1

# Others
chore/update-dependencies
docs/api-documentation
refactor/database-layer
test/add-unit-tests
```

---

## How to Choose the Right Strategy

Decision tree for selecting the appropriate branching strategy:

```
Is your app continuously deployed?
├── YES → GitHub Flow or Trunk Based
│         ├── Large team + experienced → Trunk Based
│         └── Small-medium team → GitHub Flow
│
└── NO → Scheduled releases?
         ├── YES → Git Flow
         │         ├── Multiple versions → Release Branching
         │         └── Single version → Git Flow
         │
         └── Multiple environments?
                   └── YES → GitLab Flow
```

---

## Best Practices for Any Strategy

Universal best practices applicable to all branching strategies:

```bash
# 1. Keep branches short-lived
# Merge within 1-3 days max

# 2. Use descriptive names
git switch -c feature/user-auth-jwt

# 3. Pull latest before branching
git pull origin main
git switch -c feature/new-feature

# 4. Write meaningful commit messages
git commit -m "feat: add JWT authentication for user login"

# 5. Delete merged branches
git branch -d feature/completed
git push origin --delete feature/completed

# 6. Always use Pull Requests for code review
# Never push directly to main/develop
```

---

## Summary: Choosing Your Strategy

| If you are... | Recommended Strategy |
|---|---|
| Building a web app with CI/CD | **GitHub Flow** |
| Managing versioned software releases | **Git Flow** |
| Working with staging/production environments | **GitLab Flow** |
| Large team with high velocity | **Trunk Based Development** |
| Maintaining an open source library | **Release Branching** |

