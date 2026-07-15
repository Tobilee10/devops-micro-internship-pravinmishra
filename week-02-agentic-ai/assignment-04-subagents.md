# Assignment 4 — Building Your AI Team

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build and configure a set of specialized AI subagents inside your project. You will learn how different models and tool permissions define agent behavior, and you will trigger two real agent delegations to analyze security and cost aspects of your Terraform infrastructure.

---

# Task 1 — Create the Agents Folder and Add Files

## Goal

Create the `.claude/agents/` directory and add all required agent files.

### Evidence

#### Screenshot 1 — VS Code sidebar showing `.claude/agents/` with all 3 files

![screenshot-1](./screenshots/assignment4-screenshot-1.png)

---

# Task 2 — Compare the Agent Configurations

## Goal

Analyze the configuration differences between the three agents and demonstrate understanding of model and tool selection.

### Written Answers

#### 1. Why does the cost optimizer use Haiku instead of Sonnet?

The cost optimizer uses haiku because it's extremely fast in terms of speed, best for simple extraction and cost effective because it cost 3x to 4x cheaper than sonnet. While sonnet is best for performing Complex analysis, code generation and review, multi-step reasoning, and creative writing and very expensive compared to haiku.

---

#### 2. Why does the security auditor NOT have Write in its tools list?

The security auditor operates with read-only access because its role is to identify and report vulnerabilities, not modify code. This prevents accidental changes or regressions that could result from automated or unchecked fixes.
Using read-only permissions also follows the principle of least privilege by granting only the access required to perform security reviews
---

#### 3. Why does the tf-writer use `inherit` instead of a specific model?

The tf-writer uses inherit instead of a fixed model so it automatically uses the model defined by the calling workflow. This avoids hard-coded model dependencies, simplifies configuration, and ensures consistent model selection across related tasks. It also makes future model updates easier, as changes only need to be made in one place.
---

### Evidence

#### Screenshot 2 — `security-auditor.md` frontmatter showing model and tools configuration

![screenshot-2](./screenshots/assignment4-screenshot-2.png)

---

#### Screenshot 3 — `cost-optimizer.md` frontmatter showing the model and tools configuration

![screenshot-3](./screenshots/assignment4-screenshot3.png)

---

# Task 3 — Run the Security Auditor

## Goal

Trigger the security auditor agent and analyze the generated security report for your Terraform infrastructure.

### Evidence

#### Screenshot 4 — The delegation message showing Claude launched the security-auditor

![screenshot-4](./screenshots/assignment4-screenshot-4.png)

---

#### Screenshot 5 — Security audit report output

![screenshot-5](./screenshots/assignment4-screenshot-5.png)

---

# Task 4 — Run the Cost Optimizer

## Goal

Trigger the cost optimizer agent and review the generated cost optimization report.

### Evidence

#### Screenshot 6 — The full cost optimization report

![screenshot-6](./screenshots/assignment4-screenshot-6.png)

---

# Submission Instructions

- Ensure all agent files are committed in `.claude/agents/`
- Complete all written answers in your GitHub Repo
- Push final changes to your forked GitHub repository
<<<<<<< HEAD
- Submit only the Google Doc link as required

---

## Google Doc Link

Paste your Google Doc URL here:

`https://docs.google.com/document/d/1uviUVdOoZNVkHMoS1gcsoW3b0yAMpA7VE-IVJ0L5Zd4/edit?usp=sharing`
=======
>>>>>>> upstream/main

---

## GitHub Repository URL

Paste your forked repository URL here:

`https://github.com/Tobilee10/Ultimate-Agentic-DevOps-with-Claude-Code/tree/main/.claude/agents`

---

# Completion Checklist

<<<<<<< HEAD
- [x] `.claude/agents/` folder contains all 3 agent files
- [x] Screenshot 2 shows correct `security-auditor.md` configuration
- [x] Screenshot 3 shows correct `cost-optimizer.md` configuration
- [x] All 3 written answers completed in Google Doc
- [x] Security auditor executed successfully
- [x] Cost optimizer executed successfully
- [x] Security report is visible with findings
- [x] Cost report is visible with recommendations
- [x] All required screenshots added
- [x] GitHub repo updated with agents
=======
- [x] `.claude/agents/` folder contains all 3 agent files
- [x] Screenshot 2 shows correct `security-auditor.md` configuration
- [x] Screenshot 3 shows correct `cost-optimizer.md` configuration
- [x] All 3 written answers completed 
- [x] Security auditor executed successfully
- [x] Cost optimizer executed successfully
- [x] Security report is visible with findings
- [x] Cost report is visible with recommendations
- [x] All required screenshots added
- [x] GitHub repo updated with agents
>>>>>>> upstream/main

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