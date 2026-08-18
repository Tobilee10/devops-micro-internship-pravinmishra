# Assignment 5 — Deploy a Highly Available Two-Tier Application on AWS (VPC + ALB + ASG + Multi-AZ RDS)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will design and deploy a highly available two-tier web application on AWS: highly available networking across two Availability Zones, an Application Load Balancer, an Auto Scaling Group for the web tier, and a private Multi-AZ RDS database. You must prove high availability with real failure tests.

---

# Task 1 — Create HA Networking (VPC + 4 Subnets + IGW + NAT + Route Tables)

## Goal

Build a VPC (10.0.0.0/16) with two public and two private subnets across two Availability Zones, an Internet Gateway, a NAT Gateway, and the matching public/private route tables.

### Evidence

#### Screenshot 1 — VPC details showing CIDR 10.0.0.0/16

![screenshot](./screenshots/ass5-tk1-screen1.png)

---

#### Screenshot 2 — Subnets list showing four subnets and their Availability Zones

![screenshot](./screenshots/ass5-tk1-screen2.png)

---

#### Screenshot 3 — Public route table showing the Internet Gateway route and both public-subnet associations

![screenshot](./screenshots/ass5-tk1-screen3.png)

---

#### Screenshot 4 — Private route table showing the NAT Gateway route and both private-subnet associations

![screenshot](./screenshots/ass5-tk1-screen4.png)

---

#### Screenshot 5 — NAT Gateway status showing Available and the Elastic IP

![screenshot](./screenshots/ass5-tk1-screen5.png)

---

# Task 2 — Create Security Groups (ALB, EC2, RDS) with Least Privilege

## Goal

Create `ha-alb-sg` (HTTP public), `ha-web-sg` (HTTP only from `ha-alb-sg`, SSH from your IP), and `ha-db-sg` (database port only from `ha-web-sg`).

### Evidence

#### Screenshot 6 — ALB Security Group inbound rules

![screenshot](./screenshots/ass5-tk2-screen6.png)

---

#### Screenshot 7 — EC2 Security Group inbound rules showing the ALB Security Group reference and SSH from your IP

![screenshot](./screenshots/ass5-tk2-screen7.png)

---

#### Screenshot 8 — RDS Security Group inbound rule showing the database port allowed only from the EC2 Security Group

![screenshot](./screenshots/ass5-tk2-screen8.png)

---

# Task 3 — Deploy Database Tier (RDS Multi-AZ in Private Subnets)

## Goal

Launch a private, Multi-AZ RDS database (MySQL or PostgreSQL) using the private DB Subnet Group and `ha-db-sg`.

### Evidence

#### Screenshot 9 — RDS summary showing Multi-AZ = Yes and Publicly accessible = No

![screenshot](./screenshots/ass5-tk3-screen9.png)

---

#### Screenshot 10 — RDS connectivity section showing the DB Subnet Group and Security Group

![screenshot](./screenshots/ass5-tk3-screen10.png)

---

# Task 4 — Build a Launch Template (User Data Installs App + Connects to DB)

## Goal

Create a Launch Template whose user data installs the web-server runtime, deploys the application, configures the database connection, and starts the required services.

### Evidence

#### Screenshot 11 — Launch Template details showing that user data exists, including a visible snippet

![screenshot](./screenshots/ass5-tk4-screen11.png)

---

#### Screenshot 12 — A running instance created from the template showing that the application responds on port 80 through a local test or browser using its public IP

![screenshot](./screenshots/ass5-tk4-screen12.png)

---

# Task 5 — Create an Application Load Balancer (ALB) Across 2 Public Subnets

## Goal

Create an internet-facing ALB across both public subnets with an HTTP listener and a healthy instance target group.

### Evidence

#### Screenshot 13 — ALB details showing two public subnets in two Availability Zones

![screenshot](./screenshots/ass5-tk5-screen13.png)

---

#### Screenshot 14 — Target group showing at least one healthy target

![screenshot](./screenshots/ass5-tk5-screen14.png)

---

# Task 6 — Create Auto Scaling Group (ASG) in 2 Public Subnets

## Goal

Create an Auto Scaling Group from the Launch Template across both public subnets, with desired capacity 2, minimum 2, and maximum 4, registered to the ALB target group.

### Evidence

#### Screenshot 15 — Auto Scaling Group showing desired, minimum, and maximum capacity and the selected subnet Availability Zones

![screenshot](./screenshots/ass5-tk6-screen15.png)

---

#### Screenshot 16 — EC2 instances list showing two running instances in different Availability Zones

![screenshot](./screenshots/ass5-tk6-screen16.png)

---

# Task 7 — Configure App to Use RDS + Validate Read/Write

## Goal

Confirm the application communicates with the RDS database through the ALB DNS name with at least one read and one write operation.

### Evidence

#### Screenshot 17 — Browser showing the application loaded through the ALB DNS name with the URL visible

![screenshot](./screenshots/ass5-tk7-screen17.png)

---

#### Screenshot 18 — Proof of a database write through a UI message or database query output

![screenshot](./screenshots/ass5-tk7-screen18.png)

---

# Task 8 — High Availability Tests (Must Do Both)

## Goal

Test A: terminate one web instance and confirm the Auto Scaling Group replaces it automatically without interrupting the ALB.

Test B: simulate an Availability Zone impact (stop, detach, or reduce desired capacity in one AZ) and confirm the application stays available.

### Evidence

#### Screenshot 19 — EC2 showing the terminated instance and the newly launched instance; timestamps are helpful

![screenshot](./screenshots/ass5-tk8-screen19.png)

---

#### Screenshot 20 — Target group showing healthy targets after replacement

![screenshot](./screenshots/ass5-tk8-screen20.png)

---

#### Screenshot 21 — Evidence that an instance was removed, detached, placed in Standby, or stopped in one Availability Zone

![screenshot](./screenshots/ass5-tk8-screen21.png)

---

#### Screenshot 22 — Browser showing that the ALB DNS endpoint still works during the change

![screenshot](./screenshots/ass5-tk8-screen22.png)

---

# Task 9 — Architecture and Test-Results Summary

## Goal

Summarize the VPC/subnet layout, the ALB and Auto Scaling Group setup, the private Multi-AZ RDS setup, and the results of both high-availability tests.

### Evidence

#### Screenshot 23 — A simple architecture diagram, which may be hand-drawn, or an AWS console overview showing the components

![screenshot](./screenshots/ass5-tk9-screen23.png)

---

### Notes

Summarize the VPC and subnets across the two Availability Zones.

The architecture consists of one VPC spanning across two Availability Zones.
Each Availability Zone contains one public subnet and one private subnet.
This results in a total of four subnets: two public and two private.
Public subnets are typically used for internet-facing resources such as load balancers.
Private subnets are used for application servers and other internal resources.
Distributing the subnets across two AZs improves availability and provides fault tolerance.

Summarize the ALB and Auto Scaling Group setup.

The architecture uses an Application Load Balancer (ALB) to distribute incoming traffic across multiple application instances.
The ALB is deployed across the two public subnets for high availability.
An Auto Scaling Group (ASG) manages the application instances in the two public subnets.
The ASG automatically launches or terminates instances based on demand and configuration.
The ALB forwards requests to healthy instances in the ASG through a target group.
This setup provides high availability, fault tolerance, and automatic scaling.

Summarize the private Multi-AZ RDS setup.

The architecture uses a private Amazon RDS database deployed across two Availability Zones.
The RDS instance is placed in private subnets, keeping the database inaccessible directly from the internet.
A Multi-AZ configuration provides a standby database in a separate Availability Zone.
Data is synchronously replicated between the primary and standby instances.
If the primary database fails, RDS can automatically fail over to the standby instance.
This setup provides high availability, fault tolerance, and improved database reliability.

Summarize the results of both high-availability tests.


Test A: One web instance was terminated, and the Auto Scaling Group automatically launched a replacement instance. The ALB continued serving traffic without interruption, confirming instance-level fault tolerance.

Test B: An Availability Zone impact was simulated by reducing capacity in one AZ. The application remained available through the resources in the other AZ, confirming Multi-AZ resilience and high availability.

---

# LinkedIn Post (Required)

## Goal

Publish a LinkedIn post about the high-availability build, including the ALB URL (or a redacted screenshot), three to five lines on what you built and how you tested high availability, and one proof screenshot.

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://www.linkedin.com/posts/oluwatobiloba-adeje-2572b42a6_devops-aws-cloudcomputing-activity-7494000714311110656-QCCB?utm_source=share&utm_medium=member_desktop&rcm=ACoAAEm6D2MBiHlTtqXxAdNL2_2Taiskof8w_Lw`

---

#### Screenshot of LinkedIn post

![screenshot](./screenshots/ass5-post.png)

---

# Submission Instructions

- Add all required screenshots in your submission
- Do not expose passwords, connection strings, private keys, or account IDs

---

# Completion Checklist

- [x] Task 1: VPC, four subnets, IGW, NAT Gateway, and route tables created (Screenshots 1–5)
- [x] Task 2: Least-privilege ALB, EC2, and RDS security groups created (Screenshots 6–8)
- [x] Task 3: Private Multi-AZ RDS created (Screenshots 9–10)
- [x] Task 4: Self-configuring Launch Template created and tested (Screenshots 11–12)
- [x] Task 5: ALB created across both public subnets (Screenshots 13–14)
- [x] Task 6: Auto Scaling Group running two instances across two AZs (Screenshots 15–16)
- [x] Task 7: Application verified through the ALB with a database read and write (Screenshots 17–18)
- [x] Task 8: Both high-availability tests completed (Screenshots 19–22)
- [x] Task 9: Architecture and test-results summary completed (Screenshot 23 & Notes)
- [x] LinkedIn post published and URL submitted
- [x] No sensitive data exposed

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