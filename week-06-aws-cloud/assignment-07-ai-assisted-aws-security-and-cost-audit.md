# Assignment 7 — AI-Assisted AWS Security and Cost Audit

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build a read-only Bash script that audits the AWS resources you deployed earlier this week — your S3 static site, EC2 instance(s), security groups, RDS database, and EBS volumes — for common security and cost misconfigurations.

You will then connect that script to Claude Code as a reusable `/aws-audit` skill that explains what it found and recommends a fix, without ever making the fix itself.

Finally, you will find a real misconfiguration in your own account, apply the fix yourself, and prove it worked with a second audit run.

---

# Task 1 — Confirm Your AWS Resources and Set Up Your Workspace

## Goal

Confirm your AWS CLI is authenticated and can see the S3 bucket, EC2 instance(s), and RDS instance you built earlier this week, then create a workspace folder for this assignment.

### Evidence

#### Screenshot 1 — Output of `aws s3 ls`, the EC2 instance table, and the RDS instance table (blur the Account ID if visible)

![screenshot](./screenshots/ass7-tk1-screen1.png)

---

#### Screenshot 2 — Output of `pwd` and `find . -maxdepth 4 -type d | sort`

![screenshot](./screenshots/ass7-tk2-screen2.png)

---

### Notes You Must Write (Very Important)

**1. Which resources from this week's earlier assignments did you see in the listings?**

- epicbook-instance
- epicbook-s3

**2. Why must you confirm your resources exist before writing an audit script against them?**

The reasons why I must confirm the existence of resources before writing an audit script is to:
- Avoid checking invalid or missing targets.
- Ensure the script works with the correct files, systems, databases, or services in other to prevents false audit results caused by resources that are simply not present.

---

# Task 2 — Define Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` in your workspace that tells Claude the audit script is read-only, that it must never run a command that creates, modifies, or deletes an AWS resource, and that any remediation must be recommended, never executed automatically.

### Evidence

#### Screenshot 3 — `CLAUDE.md` open in VS Code showing all four sections

![screenshot](./screenshots/ass7-tk2-screen3.png)

---

### Notes You Must Write (Very Important)

**1. Why should Claude never be given permission to run `revoke-security-group-ingress` itself, even if the fix is obviously correct?**

Claude should not be allowed to run revoke-security-group-ingress itself because it is a destructive security change.
Even if the fix looks correct, an incorrect target, rule, or environment could disrupt legitimate traffic.
The action should require human review and approval before execution.

**2. Which rule prevents Claude from claiming a finding that the report does not support?**

Safety rules define in the CLAUDE.md

---

# Task 3 — Plan the Audit with Claude Code

## Goal

Ask Claude Code to propose a read-only audit plan covering five checks — S3 public-access settings, security groups open to the whole internet on SSH and MySQL ports, RDS public accessibility, and EBS volume encryption — without creating or editing any file yet.

### Evidence

#### Screenshot 4 — Claude Code showing the five-check plan

![screenshot](./screenshots/ass7-tk3-screen4.png)

---

### Notes You Must Write (Very Important)

**1. Which part of this task represents the Gather phase?**

The Gather phase is where Claude Code reads the existing AWS configuration.
It collects information about S3, security groups, RDS, and EBS encryption.
This phase is read-only and does not make any changes.

**2. Did every proposed command start with `describe-`, `get-`, or `list-`? Why does that matter?**

Yes. Every proposed AWS command should use describe-, get-, or list- operations because 
these commands retrieve information without changing resources.
That matters because it prevents accidental modifications during the audit.
It also makes the audit safer, predictable, and easier to review.

---

# Task 4 — Build the AWS Audit Script

## Goal

Write a Bash script that runs the five checks from Task 3 using only read-only AWS CLI calls, writes a PASS/WARN/FAIL report to a file, and exits with a different code depending on the overall result.

Make it executable and confirm it has no syntax errors.

### Evidence

#### Screenshot 5 — Top section of `aws-audit.sh` showing the variables and the checks array

![screenshot](./screenshots/ass7-tk4-screen5.png)

---

#### Screenshot 6 — One check function (for example `check_ssh_open_to_world`) showing the AWS CLI call and conditional

![screenshot](./screenshots/ass7-tk4-screen6.png)

---

#### Screenshot 7 — Output of `bash -n scripts/aws-audit.sh` and `ls -l scripts/aws-audit.sh`

![screenshot](./screenshots/ass7-tk4-screen7.png)

---

### Notes You Must Write (Very Important)

**1. What is stored in the checks array, and how does the loop use it?**

The checks array stores the names of five audit-check functions.
The loop goes through each function name one at a time.
It then calls each check to perform that specific security audit.
This makes the script organized, reusable, and easy to extend.

**2. Why does every AWS CLI call in this script use `--query` and `--output text` instead of parsing raw JSON?**

The --query selects only the specific AWS data the audit needs and
--output text converts the result into simple, readable text.
This avoids parsing large and complex raw JSON responses.
It makes the audit script simpler, cleaner, and less error-prone.

**3. Why does the script use different exit codes for HEALTHY, WARN, and FAIL?**

The script uses different exit codes to clearly indicate the audit result.
HEALTHY means the check passed, WARN indicates a concern, and FAIL means a serious issue was found.
Different exit codes allow other tools or automation to detect the result automatically.
This makes the audit easier to monitor, report, and integrate into security workflows.

---

# Task 5 — Run the Baseline Audit

## Goal

Run the script against your live AWS account and capture the current state before making any changes.

### Evidence

#### Screenshot 8 — Output of `./scripts/aws-audit.sh` showing your Full Name and all five checks

![screenshot](./screenshots/ass7-tk5-sceen8.png)

---

#### Screenshot 9 — Output showing the captured exit code and final summary

![screenshot](./screenshots/ass7-tk5-screen9.png)

---

### Notes You Must Write (Very Important)

**1. What is the overall status of your baseline audit?**

WARN

**2. Did any check return FAIL or WARN? If so, which one, and what evidence did it show?**

Yes, the EBS check return warning concerning two unencrypted volumes.


**3. If every check passed, what does that tell you about the security posture of your account so far?**

It means my account is not vulnerable to attack.

---

# Task 6 — Build and Run the /aws-audit Skill

## Goal

Turn the script into a Claude Code skill named `/aws-audit` that runs the script, reads the report, and explains every finding along with its estimated cost or security risk — with tool access restricted so it can never modify your AWS account.

### Evidence

#### Screenshot 10 — `SKILL.md` showing the frontmatter, tool restrictions, and safety rules

![screenshot](./screenshots/ass7-tk6-screen10.png)

---

#### Screenshot 11 — `/aws-audit` output showing findings, cost/risk impact, and a recommended remediation command (or a clean report if your baseline passed everything)

![screenshot](./screenshots/ass7-tk6-scren11.png)

---

### Notes You Must Write (Very Important)

**1. Why does this skill have Bash, Read, and Grep, but not Write?**

The skill has Bash, Read, and Grep because it only needs to inspect and analyze existing resources.
It does not have Write because the task is designed to be read-only.
This prevents accidental creation or modification of files.

**2. What part is performed by Bash, and what part is performed by Claude?**

Bash executes the AWS CLI commands and collects the actual configuration data.
Claude interprets those results and determines whether each security check is healthy, a warning, or a failure.
In short, Bash gathers the facts, while Claude analyzes and explains them

**3. Why is estimating cost/risk impact something the AI adds on top of a plain PASS/FAIL script?**

A plain PASS/FAIL script only tells you whether a security check succeeded or failed.
AI can go further by explaining why the issue matters and estimating its potential cost or risk.
This adds useful context for prioritizing which problems should be fixed first.

---

# Task 7 — Fix a Real Finding and Re-Verify

## Goal

Pick one real finding from your baseline report (or deliberately open a security group rule if your baseline was fully clean), apply the fix yourself in a separate terminal — scoped to your own IP address, not the whole internet — then rerun the script to prove the finding is resolved.

### Evidence

#### Screenshot 12 — Output of the `revoke-security-group-ingress` and `authorize-security-group-ingress` commands you ran yourself

![screenshot](./screenshots/ass7-tk-7-screen12.jpg)

---

#### Screenshot 13 — Rerun of `./scripts/aws-audit.sh` showing the finding is now PASS

![screenshot](./screenshots/ass7-tk7-screen-13.png)

---

### Notes You Must Write (Very Important)

**1. Which exact finding did you fix, and what command did you run?**

Fix the unencrypted EBS volumes. The command run is in the screenshot below

![screenshot](./screenshots/command-run.jpg)

**2. Why did you scope the new rule to your own IP address instead of leaving it open to `0.0.0.0/0`?**

The reason why I encrypted the volume is because unencrypted volumes exposes sensitive data if hardware is compomised or snapshots are leaked

**3. Did Claude execute the remediation command, or did you? Why does that matter?**

No, I executed the remediation command, not Claude.
This matters because human retains control over high-impact security changes. This provides an important safety check against accidental or incorrect changes.

**4. Which phase of the Agentic Loop does the Bash script represent? Which phase does Claude's explanation represent? Which phase is you running the fix?**

Bash script: the Gather phase —> it collects the AWS configuration and audit results.
Claude’s explanation: the Reason/Analyze phase —> it interprets the results and explains the risk.
I represent the Act phase —> I executed the approved remediation.

---

# LinkedIn Post (Required)

## Goal

Create a LinkedIn post including:

- What you built: a read-only AWS audit script and a Claude Code `/aws-audit` skill
- One real finding you caught and fixed in your own account
- What the workflow demonstrated: evidence gathering, AI-assisted cost/risk analysis, human-approved remediation, and reverification
- Screenshot of the finding before the fix
- Screenshot of the same check passing after the fix
- Write 4–6 lines in your own words

Suggested tags:

`#DMIByPravinMishra #AWS #AgenticAI #ClaudeCode #DevOps`

### Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://lnkd.in/p/d4weTHkb`

---

#### Screenshot of Published LinkedIn Post

![screenshot](./screenshots/final-post.png)

---

# Submission Instructions

Complete all tasks in sequence.

Your submission must include:

- All 13 required task screenshots
- Answers to every **Notes You Must Write** question
- `CLAUDE.md`
- `scripts/aws-audit.sh`
- `.claude/skills/aws-audit/SKILL.md`
- `reports/aws-audit-report.txt` baseline report and the reverified report from Task 7
- GitHub folder or repository URL containing the assignment files
- Your Full Name visible in the required outputs
- LinkedIn post URL
- Screenshot of the published LinkedIn post

Submit only a Google Doc link.

Add the GitHub URL inside the Google Doc.

Follow the Assignment Submission Guidelines.

---

# Completion Checklist

- [x] Task 1: AWS resources confirmed and workspace created (Screenshots 1–2)
- [x] Task 2: `CLAUDE.md` created with project context and safety rules (Screenshot 3)
- [x] Task 3: Claude produced a read-only five-check audit plan before any script existed (Screenshot 4)
- [x] Task 4: `aws-audit.sh` built, executable, and passes `bash -n` (Screenshots 5–7)
- [x] Task 5: Baseline audit captured and saved with Full Name visible (Screenshots 8–9)
- [x] Task 6: `/aws-audit` skill loads and runs successfully with no Write permission (Screenshots 10–11)
- [x] Task 7: A real finding was fixed by you and reverified as PASS (Screenshots 12–13)
- [x] Skill never executed a remediation command
- [x] New security group rule is scoped to your own IP, not `0.0.0.0/0`
- [x] All 13 required task screenshots are included
- [x] All "Notes You Must Write" questions are answered in your own words
- [x] No AWS credentials or unblurred account IDs exposed
- [x] LinkedIn post published and URL submitted
- [x] GitHub URL included in the Google Doc
- [x] Google Doc is accessible
- [x] Link tested in incognito mode

---

# Final Submission

Submit only your Google Doc link.

### Question

Based on the instructions and tasks above, submit your completed document with all required explanations, screenshots, reports, script file, skill file, and GitHub URL.

`Add your Google Doc link here`

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*