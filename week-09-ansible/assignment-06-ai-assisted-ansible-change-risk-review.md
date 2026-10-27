# Assignment 6 — AI-Assisted Ansible Change Risk Review

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will build an AI-assisted Ansible risk-review workflow using `ansible-playbook --check --diff`, Bash scripting, and Claude Code.

You will review possible server changes before applying them, classify risky tasks, and keep the final apply decision under human control.

---

# Task 1 — Confirm EpicBook Connectivity and Create the Workspace

## Goal

Confirm that your previous EpicBook Ansible project is working before creating the risk-review automation.

### Evidence

#### Screenshot 1 — Output of `ansible web -i inventory.ini -m ping`

Add your screenshot here.

---

#### Screenshot 2 — Output of `ansible-playbook -i inventory.ini site.yml --syntax-check`

Add your screenshot here.

---

#### Screenshot 3 — Output of `pwd` and `find . -maxdepth 4 -type d | sort`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What proves that Ansible can reach your EpicBook VM?**

Ansible can reach the EpicBook VM when the ping command returns a SUCCESS status with "ping": "pong". This confirms that Ansible can connect to the VM and execute commands on it.

---

**2. Why should you confirm playbook syntax before building a risk-review script?**

Playbook syntax should be checked before building a risk-review script to identify YAML errors and structural issues early. Syntax validation helps prevent deployment failures, saves debugging time, and improves automation reliability.

---

# Task 2 — Create Project Context and Safety Rules in CLAUDE.md

## Goal

Create a `CLAUDE.md` file that tells Claude Code how this project must behave.

### Evidence

#### Screenshot 4 — `CLAUDE.md` open in VS Code or terminal showing the safety rules

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Why should Claude Code have project-specific safety rules?**

Claude Code should have project-specific safety rules to ensure it follows the project's requirements, protects sensitive information, and avoids unsafe changes to production systems.

---

**2. Why should the human run the real Ansible playbook manually?**

The human should run the real Ansible playbook manually to review the changes, verify the target servers, and maintain control over production deployments.

---

**3. Which rule prevents Claude Code from applying changes automatically?**

The rule that requires human approval before executing deployment commands or applying changes prevents Claude Code from making automatic changes to production systems.

---

# Task 3 — Ask Claude Code to Plan the Risk Review

## Goal

Use Claude Code to produce a read-only plan before writing the Bash script.

### Evidence

#### Screenshot 5 — Claude Code showing the four-category risk-classification plan

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Which part of this task represents the Gather phase?**

The Gather phase involves running the Ansible playbook in dry-run mode using --check --diff to collect evidence about possible changes without applying them. The generated risk report serves as the main source of evidence.

---

**2. Which part represents the Analyze phase?**

The Analyze phase involves reviewing the generated risk report to identify risky changes, classify their risk categories, and explain their potential impacts. Claude Code provides a recommendation while allowing the human to make the final decision.

---

**3. How did you verify Claude Code did not create or edit files?**

verified that Claude Code did not create or edit files by checking the Git status and reviewing the project directory for unexpected changes. This follows the safety rules that prohibit editing playbooks, roles, inventory, Terraform, and secret files.

---

# Task 4 — Build the Ansible Risk Review Script

## Goal

Create a Bash script that runs an Ansible dry run and classifies risky changes.

### Evidence

#### Screenshot 6 — Top section of `ansible-check-review.sh` showing `full_name`, `playbook_path`, `inventory_path`, and the `checks` array

Add your screenshot here.

---

#### Screenshot 7 — Middle section showing `extract_changed_tasks` and `check_tasks_matching_pattern`

Add your screenshot here.

---

#### Screenshot 8 — Bottom section showing the loop, summary, and exit behavior

Add your screenshot here.

---

#### Screenshot 9 — Output of `bash -n ansible-check-review.sh` and `ls -l ansible-check-review.sh`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What is stored in the `changed_tasks` array?**

The changed_tasks array stores the names of Ansible tasks that are expected to make changes to the system during the dry run. These tasks are extracted from the raw Ansible output for further risk analysis.

---

**2. Which function finds changed tasks from the Ansible output?**

The extract_changed_tasks() function identifies changed tasks by examining the raw Ansible output for task names followed by a changed status. It stores the extracted task names in the changed_tasks array.

---

**3. Why does the script use `--check --diff`?**

The script uses --check --diff to simulate playbook execution and display the changes that would occur without intentionally applying them. This allows the script to identify potential risks before the human approves the actual deployment.

---

**4. Why does the script use different exit codes for healthy, warning, and failed results?**

The script uses exit code 0 for healthy results, 1 for warnings, and 2 for failures. These codes help distinguish the overall risk level, communicate whether changes require review, and allow other scripts or automation tools to respond appropriately.

---

# Task 5 — Run the Baseline Dry-Run Review

## Goal

Run the script against your current EpicBook playbook and confirm the baseline risk status.

### Evidence

#### Screenshot 10 — Output of `./ansible-check-review.sh`

Add your screenshot here.

---

#### Screenshot 11 — Output of `echo "Captured Exit Code: $script_exit_code"` and `cat reports/ansible-risk-report.txt`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What was the overall status of your baseline run?**

The overall status was WARN – changes present, review recommended. The dry run completed successfully, but one task was detected as changed.

---

**2. Did any tasks report `changed`?**

Yes, one task was detected: Update apt cache. Ansible reported changed=1, meaning the task would make a change if applied.

---

**3. Were any changed tasks flagged as risky?**

No, none of the changed tasks were flagged as risky by the script. However, the Update apt cache task requires review before applying changes.

---

**4. What does the script exit code mean?**

The script returned exit code 1, indicating that warnings were detected and changes require review. The dry run completed with no failed or unreachable hosts, but a human should review the report before deciding whether to apply the playbook.

---

# Task 6 — Create and Run the Claude Code Skill

## Goal

Turn the Bash script into a reusable Claude Code skill called `/ansible-risk-review`.

### Evidence

#### Screenshot 12 — `SKILL.md` showing the frontmatter, allowed tools, and safety rules

Add your screenshot here.

---

#### Screenshot 13 — Claude Code output after running `/ansible-risk-review`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Why does this skill allow `Bash`, `Read`, and `Grep`?**

Add your answer here.

---

**2. Why does this skill not allow file editing?**

Add your answer here.

---

**3. What part is handled by Bash?**

Add your answer here.

---

**4. What part is handled by Claude Code?**

Add your answer here.

---

**5. Why is this better than asking Claude Code if the playbook is safe without giving it evidence?**

Add your answer here.

---

# Task 7 — Introduce a Controlled Risky Change and Let the Skill Catch It

## Goal

Add a small controlled risky change in your lab playbook and confirm the script and Claude Code catch it before applying.

### Evidence

#### Screenshot 14 — The added risky task inside the role file

Add your screenshot here.

---

#### Screenshot 15 — Output of `./ansible-check-review.sh`

Add your screenshot here.

---

#### Screenshot 16 — Claude Code `/ansible-risk-review` output showing the risky finding

Add your screenshot here.

---

#### Screenshot 17 — Output of `cat reports/risky-change-report.txt`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Which risk category did the added task fall into?**

Add your answer here.

---

**2. What evidence proves the task would change something?**

Add your answer here.

---

**3. Did Claude Code apply the playbook?**

Add your answer here.

---

**4. Why is it important that Claude Code only analyzed the risk?**

Add your answer here.

---

**5. Which phase of the Agentic Loop is represented by the Bash report?**

Add your answer here.

---

# Task 8 — Apply as the Human, Verify, and Write the Change Summary

## Goal

Review the risky-change report, apply the playbook manually as the human operator, and verify the result.

### Evidence

#### Screenshot 18 — Output of the real playbook run showing the final recap with `failed=0`

Add your screenshot here.

---

#### Screenshot 19 — Output of `ansible web -i inventory.ini -m ping`

Add your screenshot here.

---

#### Screenshot 20 — Second `/ansible-risk-review` output after applying the change

Add your screenshot here.

---

#### Screenshot 21 — Output of `ls -lah reports`

Add your screenshot here.

---

#### Screenshot 22 — `change-summary.md` showing all required sections and your Full Name

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What command did you run to apply the change for real?**

Add your answer here.

---

**2. Who made the final decision to apply the playbook?**

Add your answer here.

---

**3. What evidence proves the VM is still reachable?**

Add your answer here.

---

**4. Why should the risk review be run again after applying?**

Add your answer here.

---

**5. What could go wrong if an AI agent applied Ansible changes automatically?**

Add your answer here.

---

# LinkedIn Post Required

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`Add your URL here`

---

#### Screenshot — Published LinkedIn post

Add your screenshot here.

---

# Required Files

Confirm that the following files are included in your GitHub repository or assignment folder:

- [ ] `CLAUDE.md`
- [ ] `ansible-check-review.sh`
- [ ] `.claude/skills/ansible-risk-review/SKILL.md`
- [ ] `reports/risky-change-report.txt`
- [ ] `reports/post-apply-report.txt`
- [ ] `change-summary.md`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots and reports.
- All required notes must be answered clearly.
- Do not expose SSH private keys, passwords, cloud credentials, database credentials, or secret environment variables.
- Add your GitHub repository or folder URL inside this document.

---

# Completion Checklist

- [ ] Task 1: EpicBook connectivity confirmed and workspace created
- [ ] Task 2: `CLAUDE.md` created with safety rules
- [ ] Task 3: Claude Code produced a read-only risk-review plan
- [ ] Task 4: `ansible-check-review.sh` created and syntax checked
- [ ] Task 5: Baseline dry-run review completed
- [ ] Task 6: Claude Code `/ansible-risk-review` skill created and tested
- [ ] Task 7: Controlled risky change introduced and detected
- [ ] Task 8: Human applied the change and verified the result
- [ ] Risky-change report saved
- [ ] Post-apply report saved
- [ ] Change summary completed
- [ ] All screenshots added
- [ ] All notes answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*