# Naming Convention Files

This directory contains standardized semantic guidelines and development conventions that keep our codebase unified, predictable, and easy to maintain. Strict adherence to these conventions ensures smooth collaboration across the entire development team.

## 📂 Conventions Overview

### 1. API & Database Naming Convention
* **Purpose:** Defines the rules for structuring the database (tables, columns, indexes) and designing RESTful APIs. It standardizes JSON payloads, exact HTTP status responses, standardized error objects, query parameters for handling pagination/filtering, webhooks, and common code casing structures.
* **When to use:** Reference this document when implementing new backend features, building integrations, or mapping new database schemas. It ensures that consumers of the API receive incredibly predictable, resource-oriented responses.

### 2. Git Branch Naming Convention
* **Purpose:** Outlines standardized branch naming structures (heavily aligned with Conventional Commits principles) and defines the overarching git lifecycle. It instructs the team on exactly when to use branch prefixes like `feature/`, `bugfix/`, `hotfix/`, `refactor/`, and `chore/`.
* **When to use:** Read this before you create a new Git branch and start a piece of work. It also documents vital cleanup policies, such as pushing Draft PRs and automatically deleting merged branches to keep the repository unpolluted.

### 3. Release Naming Convention
* **Purpose:** Guides the team on how to version deployments and strictly track what code lives in which environment utilizing Semantic Versioning (SemVer). It explains how underlying Git commit types (`feat:`, `fix:`) automatically dictate version bumps. It also outlines detailed workflows for handling critical hotfixes versus standard release branches.
* **When to use:** Reference this when wrapping up a development sprint, rolling out a new staging environment (`-beta` or `-rc`), tagging a production deployment, or generating an official `CHANGELOG.md`.

---

## 🚀 How to Use These Conventions

* **Onboarding:** These documents should be thoroughly reviewed by every new engineer joining the project during their onboarding week.
* **Code Review Standard:** The rules detailed in these conventions serve as the ultimate objective source of truth during Code Reviews. Pull requests that significantly deviate from these structures should be requested for changes until corrected.
* **Continuous Updates:** Technology and team practices naturally evolve. When they do, these convention files must be updated via a reviewed Pull Request so that the engineering team always relies on a single, living source of truth.
