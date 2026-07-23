# Assignment 6 — Building an AI-Assisted Git Safety Net (PR Ready Check)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In Week 2 you built Claude Code hooks that block a dangerous action *before* it happens (`PreToolUse`), and a restricted skill that could look but not touch (`allowed-tools` without `Write`). In this assignment you will discover that Git has the exact same idea, decades older: a **pre-commit hook** that blocks a commit before it's created.

You will build both halves of a real "PR Ready" workflow:

1. A **Git hook that follows fixed rules** — scans staged changes for hardcoded secrets and oversized files and refuses the commit. No AI involved, no guessing, just a rule that gives the same answer every time.
2. A **restricted Claude Code skill** (`/pr-ready`) that reads your staged diff and drafts a Pull Request title, description, and a short list of things worth a second look — the kind of judgment a fixed rule can't make (mixed changes, missing context, unclear intent). The skill never commits, pushes, or opens the PR. You do that yourself, using its draft as a starting point.

This mirrors the Agentic Loop from Week 3's Linux triage assignment: **Gather → Analyze → Human Act → Verify**. The hook and the skill both gather and analyze; only you act.

---

# Task 0 — Confirm Your Fork and Create a Feature Branch

## Goal

Confirm you are working in your own fork, then create a dedicated branch for this assignment.

### Evidence

#### Screenshot 1 — Output of git remote -v and git branch showing the new branch

![sceen](./screenshots/ass-6-tk0-screen-0.png)

---

### Notes

**1. Why create a dedicated branch instead of doing this work on main?**

A dedicated branch keeps work separate from the main branch ensuring the main codebase remains stable and reliable. It allows develops to test, and fix issues without affecting others, thereby making code reviews and collaboration easier through pull requests

---

# Task 1 — Stage a Change With Realistic Risk

## Goal

On your own fork of this repository (the one you've been submitting your DMI work in since onboarding), create a new branch and stage a change that a real reviewer should catch: a hardcoded-looking secret and a leftover debug statement.

### Evidence

#### Screenshot 1 — Output of  `git status` showing the staged file on feature/ai-pr-ready

![screen](./screenshots/ass-6-tk1-screen-1.png)

---

### Notes

**1. Why does this assignment use an obviously fake key instead of a real one?**

This assignment uses an obviously fake key to ensure that no real credentials are exposed or misused during training. As a DevOps intern, I learned that hardcoding real API keys or secrets in code is a serious security risk. Using a fake key allows us to safely practice configuration and deployment workflows while reinforcing the importance of managing sensitive information securely through tools like environment variables or secret managers.
---

# Task 2 — Write a Real Git Pre-Commit Hook

## Goal

Create a tracked, shareable pre-commit hook that blocks a commit containing secret-like patterns or files over 1MB.

### Evidence

#### Screenshot 2 — `hooks/pre-commit` open in VS Code showing the full script

![screen](./screenshots/ass-6-tk2-screen-2.png)

---

#### Screenshot 3 — Output of `git config core.hooksPath` confirming it points to `hooks`

![screen](./screenshots/ass-6-tk2-screen-3.png)

---

### Notes

**1. Why is `hooks/pre-commit` tracked in the repo instead of living only in `.git/hooks/`?**

Tracking hooks/pre-commit in the repository allows the hook to be shared with every team member instead of existing only in each developer's local .git/hooks/ directory. This ensures everyone follows the same code quality and validation checks before committing changes. It also makes the hook version-controlled, so updates are documented and distributed consistently.

---

**2. Compare this to `PreToolUse` from Week 2 Assignment 6. What does each one intercept, and what do they have in common?**

I observed that hooks/pre-commit intercepts Git commits by running checks before changes are committed to the repository, helping catch issues early. In contrast, PreToolUse from Week 2 Assignment 6 intercepts tool execution requests before a tool is allowed to run, enforcing policies or validating actions. Both act as preventive checkpoints that validate operations before they proceed, improving security, consistency, and reliability in the development workflow.

---

# Task 3 — Prove the Hook Blocks the Risky Commit

## Goal

Attempt to commit the staged file from Task 1 and show the hook rejecting it.

### Evidence

#### Screenshot 4 — Terminal showing `git commit` rejected with the hook's "BLOCKED" message naming the exact file

![screen](./screenshots/ass-6-tk3-screen-4.png)

---

### Notes

**1. Which line in `hooks/pre-commit` matched your fake key, and why did it match?**

I found that the line containing the secret-detection pattern (if git diff --cached -- "$file" | grep -qE 'AKIA[0-9A-Z]{16}|-----BEGIN (RSA|OPENSSH|PRIVATE) KEY-----'; ) matched the fake key because the key followed the same format as a real API key. The hook checks for patterns that resemble valid secrets rather than verifying whether the key actually works. This helps prevent developers from accidentally committing sensitive credentials to the repository.

---

**2. Could this hook have caught a poorly-named variable that stores a secret without the `AKIA` prefix? What does that tell you about the limits of a fixed rule like this?**

I realized that this hook would likely not catch a poorly named variable storing a secret if it didn't match the expected pattern, such as the AKIA prefix. Since the hook relies on fixed rules and regular expressions, it can only detect secrets that fit predefined formats. This highlights a limitation of pattern-based detection, it may miss valid secrets with unexpected names or formats. 

---

# Task 4 — Build the `/pr-ready` Skill

## Goal

Create a manually invoked Claude Code skill that reads your staged changes and produces a PR-readiness report and a draft PR description — without writing, committing, or pushing anything itself.

### Evidence

#### Screenshot 5 — `SKILL.md` frontmatter showing `allowed-tools: Bash, Read, Grep` (no `Write`) and `disable-model-invocation: true`

![screen](./screenshots/ass-6-tk4-screen-5.png)

---

#### Screenshot 6 — `/pr-ready` output while the risky file is still staged, showing it flagged the secret and/or debug statement

![screen](./screenshots/ass-6-tk4-screen-6.png)

---

### Notes

**1. Why does `/pr-ready` have `Bash` and `Read` but not `Write`?**

The /pr-ready has Bash and Read permissions because it only needs to inspect the repository, run validation commands, and gather information before preparing a pull request. It does not require Write permission since it isn't meant to modify files or make changes to the repository. This follows the principle of least privilege, where a tool is granted only the permissions necessary to perform its task, improving security and reducing the risk of accidental changes.

---

**2. The pre-commit hook and `/pr-ready` both looked at the same staged diff. Did they flag the same things? What did one catch that the other didn't?**

I noticed that although the pre-commit hook and /pr-ready both examined the same staged diff, they did not flag exactly the same issues. The pre-commit hook focused on predefined checks, such as detecting secret patterns and preventing unsafe commits before they were made. In contrast, /pr-ready performed broader readiness checks, including code quality, project standards, and pull request preparation. This showed me that pre-commit hooks are effective for enforcing specific rules, while /pr-ready provides a more comprehensive review of the overall changes before submission.

---

# Task 5 — Fix the Issues and Re-Verify

## Goal

Remove the secret and debug statement, then prove both gates now pass clean.

### Evidence

#### Screenshot 7 — `git commit` succeeding after the fix (no BLOCKED message)

![screen](./screenshots/ass-6-tk5-screen-7.png)

---

#### Screenshot 8 — Second `/pr-ready` run showing a clean risk report and a drafted PR title + description

![screen](./screenshots/ass-6-tk5-screen-7.png)

---

### Notes

**1. What exactly did you change to satisfy the pre-commit hook?**

I changed the line that contain the AWS-ACCESS-KEY by removing it from the notify.sh script

---

# Task 6 — Push and Open a Pull Request Using the AI Draft

## Goal

Push your branch and open a real Pull Request, using `/pr-ready`'s drafted title and description as your starting point — read it critically and edit before you use it.

**Important:** Open this Pull Request with base repository set to **your own fork** — not the shared upstream `pravinmishraaws/devops-micro-internship-pravinmishra` repository. This assignment's hook and skill files are your own practice work, not a change meant for the shared class repo.

### Evidence

#### Screenshot 9 — Your Pull Request showing the base repository is your own fork, plus the title and description, with the `/pr-ready` draft visible for comparison (paste it in the PR conversation or your notes below)

![screen](./screenshots/ass-6-tk6-screen-9.png)

---

#### PR Link

https://github.com/Tobilee10/devops-micro-internship-pravinmishra/pull/1

---

### Notes

**1. What, if anything, did you edit in the AI's drafted PR description before using it? Why?**

I reviewed the AI-generated pull request and found it to be well-structured and properly formatted. So I didn't edit it.

---

**2. If you had blindly copy-pasted the AI's draft without reading it, what could go wrong?**

Blindly copying an AI-generated pull request could result in inaccurate or misleading information being submitted. It may not follow the project's contribution guidelines or accurately describe in the changes. Reviewing the AI-generated content ensures it is correct, relevant, and reflects your actual work before submission.

---

**3. Why does this PR need to target your own fork instead of the shared upstream repository?**

The PR is created from my fork because I don't have direct write access to the upstream repository. Making changes in my fork keeps the original repository safe while allowing the maintainers to review and merge my contribution if it's approved.

---

# Task 7 — Map the Workflow to the Agentic Loop

## Goal

Explain this assignment's workflow using the same Gather → Analyze → Human Act → Verify structure from Week 3.

### Notes

**1. Which step(s) represent Gather?**

The Gather step occurs when the staged changes are collected and examined. Both the pre-commit hook and /pr-ready read the staged Git diff to understand what has changed.

---

**2. Which step(s) represent Analyze?**

The Analyze step happens when the pre-commit hook scans for fixed patterns (such as secrets) and /pr-ready reviews the staged changes for code quality, best practices, and PR readiness.

---

**3. Which step is Human Act, and why must a human — not Claude — run `git commit`, `git push`, and open the PR?**

The Human Act step is when I run git commit, git push, and open the pull request. A human must perform these actions because they modify the repository and publish changes, ensuring that the developer reviews and approves the work before it is shared.

---

**4. Which step is Verify?**

The Verify step occurs after the checks pass and the pull request is reviewed. This confirms that the code meets quality standards, contains no prohibited secrets, and is ready to be merged.

---

**5. In one or two sentences: why do you need *both* the fixed-rule pre-commit hook and the AI skill? Isn't one enough?**

The pre-commit hook quickly enforces fixed, predictable rules such as detecting known secret patterns, while the AI skill provides broader, context-aware analysis that can identify issues beyond simple pattern matching. Using both creates a stronger and more reliable review process than relying on either one alone.

---

# Task 8 — LinkedIn Post

## Goal

Publish a LinkedIn post summarizing what you built and what you learned about combining fixed-rule safety checks with AI-assisted review.

### Evidence

#### LinkedIn Post URL

https://www.linkedin.com/posts/oluwatobiloba-adeje-2572b42a6_devops-devsecops-git-activity-7486040116877193216-Q5gT?utm_source=share&utm_medium=member_desktop&rcm=ACoAAEm6D2MBiHlTtqXxAdNL2_2Taiskof8w_Lw

---

## Key Learnings

Add 3-5 bullet points on what you learned this week.

- Feature branches keep the main branch stable while enabling safe experimentation and collaboration.
- Git pre-commit hooks act as automated gatekeepers by enforcing fixed security and quality rules before every commit.
- AI-assisted review analyzes code context, maintainability, and overall pull request readiness—not just predefined patterns.
- The principle of least privilege is essential when designing secure developer workflows.
- Fixed-rule automation and AI are complementary technologies; together they provide stronger validation than either can achieve alone.


---

# Submission Instructions

- Ensure `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md` are committed to your GitHub repository
- Add all required screenshots to your submission
- All written answers must be in your own words
- Do not use a real secret or credential anywhere in your submission — the fake key in Task 1 is intentional and must stay clearly fake
- Open your Pull Request against your own fork, not the shared upstream repository
- Push your final changes to your forked repository
- Include your PR link and LinkedIn post URL

---

## GitHub Repository URL

Paste your forked repository URL here:

`https://github.com/Tobilee10/devops-micro-internship-pravinmishra`

---

# Completion Checklist

- [x] Branch `feature/ai-pr-ready` created with a staged file containing a fake secret and a debug statement
- [x] `hooks/pre-commit` created and tracked in the repo (not only in `.git/hooks/`)
- [x] `core.hooksPath` configured to point at `hooks/`
- [x] Pre-commit hook shown blocking the risky commit
- [x] `.claude/skills/pr-ready/SKILL.md` created with correct `allowed-tools` (no `Write`) and `disable-model-invocation: true`
- [x] `/pr-ready` run against the risky diff and shown flagging issues
- [x] Risky file fixed; `git commit` succeeds cleanly
- [x] `/pr-ready` re-run showing a clean report and drafted PR title/description
- [x] Pull Request opened using the AI draft as a starting point, with your own fork as the base repository (not upstream), PR link included
- [x] Agentic Loop mapping (Task 7) completed in your own words
- [x] LinkedIn post published and URL submitted
- [x] All required screenshots added
- [x] GitHub repository URL provided

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
