# Assignment 02 — Provision Linux VMs with Terraform and Run Ansible Ad-Hoc Commands

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision three or four Ubuntu Linux Virtual Machines on either Microsoft Azure or Amazon Web Services.

You will configure SSH key-based authentication, organize the servers using a custom Ansible inventory, and run Ansible ad-hoc commands across individual hosts and inventory groups.

---

# Task 1 — Create the Multi-Host Lab Structure

## Goal

Create a separate project directory for the multi-host lab and prepare the Terraform, Ansible, and documentation files.

This project will use the Git repository and Ansible controller prepared in Assignment 01.

### Evidence

#### Screenshot 1 — Terminal showing the complete `ansible-adhoc-lab` project structure

![screenshot](./screenshots/ass2-screen1.png)

---

#### Screenshot 2 — Terminal showing `git status --short` with the new project files and updated `.gitignore`

![screenshot](./screenshots/ass2-screen2.png)

---

### Notes

Do not run git init inside ansible-adhoc-lab.

The existing Git repository from Assignment 01 will track this project.

Do not store cloud credentials or SSH private keys inside the project directory.

Do not commit Terraform state files because they may contain sensitive infrastructure information.

---

# Task 2 — Create the Terraform Configuration

## Goal

Create the Terraform configuration required to provision three or four Ubuntu Linux VMs on your selected cloud platform.

Complete only one option:

- Option A — Microsoft Azure
- Option B — Amazon Web Services

Do not configure both providers for this assignment.

### Evidence

#### Screenshot 3 — Terraform configuration showing the three or four server roles and the `for_each` or `count` implementation

![screenshot](./screenshots/ass2-screen3.png)

---

#### Screenshot 4 — Terraform configuration showing SSH restricted to the controller IP and HTTP allowed only for web hosts

![screenshot](./screenshots/ass2-screen4.png)

---

#### Screenshot 5 — Terraform output configuration showing how public IP addresses are associated with the server roles

![screenshot](./screenshots/ass2-screen5.png)

---

### Notes

Create the Terraform configuration yourself using the task requirements and official documentation.

The recommended file structure is not mandatory.

The variable name vm_roles is not mandatory.

Use the fixed cloud-resource names provided in this task.

Do not include cloud credentials or private keys.

Do not use 0.0.0.0/0 for SSH.

Select a suitable VM size or instance type for your account and region.

Do not run terraform apply in this task.

---

# Task 3 — Provision the Infrastructure with Terraform

## Goal

Initialize and validate the Terraform configuration, review the execution plan, provision the selected three or four VMs, and retrieve their public IP addresses.

### Evidence

#### Screenshot 6 — Final `terraform apply` output showing `Apply complete`

![screenshot](./screenshots/ass2-screen6a.png)

---

#### Screenshot 7 — `terraform output public_ips` showing the role-to-IP mapping for all three or four VMs

![screenshot](./screenshots/ass2-screen7.png)

---

#### Screenshot 8 — Azure Portal or AWS Management Console showing all three or four VMs in the `Running` state, with their role-based names visible

![screenshot](./screenshots/ass2-screen8a.png)

---

### Notes

Public IP addresses are not passwords, but you may partially redact them in public submission documents.

Never submit Terraform state-file contents.

Do not manually rename or recreate Terraform-managed resources in the cloud portal.

If provisioning fails because of quota or regional capacity, choose another suitable size, region, or the three-VM option and run Terraform again.

Keep the VMs running until all SSH and Ansible tasks are complete.

---

# Task 4 — Verify SSH Key-Based Access

## Goal

Verify that each managed VM can be accessed from the Ansible controller using SSH key-based authentication.

### Evidence

#### Screenshot 9 — Terminal showing successful SSH hostname output from all VMs

![screenshot](./screenshots/ass2-screen9a.png)

---

### Notes

For Azure, the SSH public key is usually configured with the Terraform admin_ssh_key block.

For AWS, the SSH public key is usually configured through an EC2 key pair and attached to each instance.

The first SSH connection to a new VM may ask for host fingerprint confirmation. Type yes to continue.

If your SSH private key has a passphrase, your terminal may ask for that local key passphrase.

A remote VM password prompt is not expected for this assignment.

If SSH asks for a remote VM password, or returns Permission denied (publickey), check the SSH key, username, security rule, and public IP address.

---

# Task 5 — Create the Custom Ansible Inventory

## Goal

Create an Ansible inventory file that groups the managed VMs by role.

The inventory allows Ansible to run commands against all servers, or only specific groups such as `web`, `app`, or `db`.

### Evidence

#### Screenshot 10 — `inventory.ini` showing the `web`, `app`, and `db` groups

![screenshot](./screenshots/ass2-screen10.png)

---

#### Screenshot 11 — Output of `ansible-inventory -i inventory.ini --graph`

![screenshot](./screenshots/ass2-screen11.png)

---

### Notes

Do not place your SSH private key inside the project folder.

Do not commit Terraform state files, SSH private keys, or credentials to GitHub.

ansible_ssh_private_key_file tells Ansible which private key to use when connecting to the VMs.

ansible_host stores the VM public IP address.

web1, web2, app1, and db1 are friendly host names used by Ansible.

host_key_checking = False is used only for this temporary lab environment.

In Ansible commands, -i inventory.ini means “use this inventory file.” It is different from the SSH -i option, which means “use this private key file.”

---

# Task 6 — Run Ansible Ad-Hoc Commands

## Goal

Run Ansible ad-hoc commands from the controller to verify connectivity, check server information, and manage packages and services across inventory groups.

This task proves that the inventory is working and that Ansible can control multiple managed VMs without writing a playbook.

### Evidence

#### Screenshot 12 — Output of `ansible all -i inventory.ini -m ping`

![screenshot](./screenshots/ass2-screen12.png)

---

#### Screenshot 13 — Output of `ansible all -i inventory.ini -m command -a "uptime"`

![screenshot](./screenshots/ass2-screen13.png)

---

#### Screenshot 14 — Output of `ansible web -i inventory.ini -m apt -a "name=nginx state=present update_cache=yes" --become`

![screenshot](./screenshots/ass2-screen14.png)

---

#### Screenshot 15 — Output of `ansible web -i inventory.ini -m service -a "name=nginx state=started enabled=yes" --become`

![screenshot](./screenshots/ass2-screen15.png)

---

#### Screenshot 16 — Output of `ansible all -i inventory.ini -m apt -a "name=htop state=present update_cache=yes" --become`

![screenshot](./screenshots/ass2-screen16.png)

---

#### Screenshot 17 — Output of `ansible web -i inventory.ini -m command -a "systemctl is-active nginx"`

![screenshot](./screenshots/ass2-screen17.png)

---

### Notes

Ad-hoc commands are useful for quick one-time actions.

Use --become when the command needs administrative privileges.

Package installation and service management require --become.

The web group should contain:

    web1 and web2 for the four-VM option

    web1 only for the three-VM option

The ping module is not an ICMP network ping. It checks whether Ansible can connect to the host and run Python successfully.

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

# Assignment Questions

Answer the following in your own words:

**1. What is the purpose of an Ansible inventory file?**

Add your answer here.

---

**2. What is the difference between the `web`, `app`, and `db` groups in your inventory?**

Add your answer here.

---

**3. What does the Ansible `ping` module verify?**

Add your answer here.

---

**4. Why do package installation commands require `--become`?**

Add your answer here.

---

**5. When would you use an ad-hoc command instead of a playbook?**

Add your answer here.

---

**6. What is one challenge you faced while setting up SSH or inventory, and how did you fix it?**

Add your answer here.

---

# Required Files

Confirm that the following files are included in your assignment workspace:

- [ ] `ansible-adhoc-lab/README.md`
- [ ] `ansible-adhoc-lab/terraform/providers.tf`
- [ ] `ansible-adhoc-lab/terraform/main.tf`
- [ ] `ansible-adhoc-lab/terraform/variables.tf`
- [ ] `ansible-adhoc-lab/terraform/outputs.tf`
- [ ] `ansible-adhoc-lab/ansible/inventory.ini`
- [ ] Updated `.gitignore`

---

# Submission Instructions

- Add all required screenshots from the tasks.
- Full Name must be visible in required screenshots.
- Mention whether you used Azure or AWS.
- Mention whether you used the three-VM option or four-VM option.
- Add the public IP addresses of the VMs, redacted if preferred.
- Add your `inventory.ini` proof.
- Add a short explanation of what you learned.
- Answer all assignment questions clearly in your own words.
- Add your LinkedIn post URL.
- Do not expose SSH private keys, Terraform state files, cloud credentials, passwords, access keys, secret keys, account IDs, or subscription IDs.

---

# Completion Checklist

- [ ] Task 1: `ansible-adhoc-lab` project structure created
- [ ] Task 1: `.gitignore` updated for Terraform files
- [ ] Task 2: Terraform configuration created
- [ ] Task 2: Server roles defined for either three or four VMs
- [ ] Task 2: `count` or `for_each` used
- [ ] Task 2: SSH restricted to the controller public IP
- [ ] Task 2: HTTP allowed only for web hosts
- [ ] Task 2: Terraform output maps roles to public IPs
- [ ] Task 3: Terraform initialized successfully
- [ ] Task 3: Terraform configuration validated
- [ ] Task 3: Terraform apply completed successfully
- [ ] Task 3: All selected VMs are running
- [ ] Task 4: SSH key-based access works for every VM
- [ ] Task 5: `inventory.ini` contains `web`, `app`, and `db` groups
- [ ] Task 5: `ansible-inventory -i inventory.ini --graph` shows the correct groups
- [ ] Task 6: `ansible all -i inventory.ini -m ping` returns `SUCCESS`
- [ ] Task 6: Ad-hoc commands run successfully
- [ ] Task 6: `--become` was used for package and service tasks
- [ ] Task 6: Nginx is active on the `web` group
- [ ] Screenshots 1–17 are included
- [ ] Assignment questions are answered
- [ ] LinkedIn post published
- [ ] LinkedIn post URL added
- [ ] No sensitive information is exposed

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