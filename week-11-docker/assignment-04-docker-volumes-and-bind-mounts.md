# Assignment 4 — Docker Volumes and Bind Mounts

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will use Docker Bind Mounts and Docker Volumes to persist logs and application data outside a container’s lifecycle. You will verify that data remains available after containers are removed and recreated.

---

# Task 1 — Persist Nginx Logs Using a Bind Mount

## Goal

Deploy an Nginx container with a Bind Mount and verify that its log files remain on the VM host after the container is removed.

### Evidence

#### Screenshot 1 — Nginx Image Pull

Add a screenshot of the terminal showing successful completion of:

```bash
docker pull nginx:alpine
```

![screenshot](./screenshots/ass4-screen1.png)

---

#### Screenshot 2 — Host Log Directory

Add a screenshot of the terminal showing the created host directory:

```text
$HOME/nginx-logs
```

![screenshot](./screenshots/ass4-screen2.png)

---

#### Screenshot 3 — Running Nginx Container with Port Mapping

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show the `myweb` container with:

```text
0.0.0.0:80->80/tcp
```

![screenshot](./screenshots/ass4-screen3.png)

---

#### Screenshot 4 — Nginx Welcome Page

Add a browser screenshot showing the Nginx Welcome Page at:

```text
http://3.15.202.63
```

Ensure that the VM public IP is visible in the address bar. Add your full name as a clear caption directly below the screenshot.

![screenshot](./screenshots/ass4-screen4.png)

---

#### Screenshot 5 — Bind-Mounted Log Files

Add a screenshot of the terminal showing the host log files and access-log content from:

```text
$HOME/nginx-logs
```

The output must show `access.log`, `error.log`, and an access-log entry created when you opened the Nginx page.

![screenshot](./screenshots/ass4-screen5.png)

---

#### Screenshot 6 — Nginx Container Removed

Add a screenshot of the terminal showing successful completion of:

```bash
docker stop myweb
docker rm myweb
```

![screenshot](./screenshots/ass4-screen6.png)

---

#### Screenshot 7 — Logs Persist After Container Removal

Add a screenshot of the terminal showing that `access.log` and `error.log` still exist in:

```text
$HOME/nginx-logs
```

The access log must retain its content after the container has been removed.

![screenshot](./screenshots/ass4-screen7.png)

---

# Task 2 — Share Persistent Data Using a Docker Volume

## Goal

Deploy backend and frontend containers that share data through a named Docker Volume. Verify that the data remains after both containers are removed and recreated.

### Evidence

#### Screenshot 8 — Project File Structure

Add a screenshot of the terminal showing the `two-tier-app` project structure, including separate `backend` and `frontend` directories with a `Dockerfile` and `index.js` file in each.

![screenshot](./screenshots/ass4-screen8.png)

---

#### Screenshot 9 — Custom Docker Network

Add a screenshot of the terminal showing `mynetwork` in:

```bash
docker network ls
```

![screenshot](./screenshots/ass4-screen9.png)

---

#### Screenshot 10 — Docker Volume

Add a screenshot of the terminal showing `shared-data` in:

```bash
docker volume ls
```

![screenshot](./screenshots/ass4-screen10.png)

---

#### Screenshot 11 — Backend Dockerfile

Add a screenshot of the terminal showing the completed backend `Dockerfile`.

![screenshot](./screenshots/ass4-screen11.png)

---

#### Screenshot 12 — Backend Image Build

Add a screenshot of the terminal showing successful completion of the `backend-app:latest` image build.

![screenshot](./screenshots/ass4-screen12.png)

---

#### Screenshot 13 — Running Backend Container

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show the running `backend` container.

![screenshot](./screenshots/ass4-screen13.png)

---

#### Screenshot 14 — Frontend Dockerfile

Add a screenshot of the terminal showing the completed frontend `Dockerfile`.

![screenshot](./screenshots/ass4-screen14.png)

---

#### Screenshot 15 — Frontend Image Build

Add a screenshot of the terminal showing successful completion of the `frontend-app:latest` image build.

![screenshot](./screenshots/ass4-screen15.png)

---

#### Screenshot 16 — Running Backend and Frontend Containers

Add a screenshot of the terminal showing:

```bash
docker ps
```

The output must show both `backend` and `frontend` containers running. Only `frontend` must have the published port mapping:

```text
0.0.0.0:80->80/tcp
```

![screenshot](./screenshots/ass4-screen16.png)

---

#### Screenshot 17 — Backend Write Operation

Add a screenshot of the terminal showing a successful backend write operation to the shared Docker Volume.

The output must include:

```text
Data written: Hello from Backend!
```

![screenshot](./screenshots/ass4-screen17.png)

---

#### Screenshot 18 — Frontend Reads Shared Data

Add a browser screenshot showing:

```text
Hello from Backend!
```

Add your full name as a clear caption directly below the screenshot.

![screenshot](./screenshots/ass4-screen18.png)

---

#### Screenshot 19 — First Shared-Data Update

Add a browser screenshot showing:

```text
Test Data 1
```

Add your full name as a clear caption directly below the screenshot.

![screenshot](./screenshots/ass4-screen19.png)

---

#### Screenshot 20 — Second Shared-Data Update

Add a browser screenshot showing:

```text
Test Data 2 - New Update
```

Add your full name as a clear caption directly below the screenshot.

![screenshot](./screenshots/ass4-screen20.png)

---

#### Screenshot 21 — Container Removal and Recreation

Add a screenshot of the terminal showing the `frontend` and `backend` containers removed and recreated using the same `shared-data` Docker Volume.

![screenshot](./screenshots/ass4-screen21.png)

---

#### Screenshot 22 — Data Persists After Recreation

Add a browser screenshot showing:

```text
Test Data 2 - New Update
```

This proves that the `shared-data` Docker Volume outlived both application containers.

Add your full name as a clear caption directly below the screenshot.

![screenshot](./screenshots/ass4-screen22.png)

---

# Storage Persistence Notes

Write a short explanation covering:

- The difference between a Bind Mount and a Docker Volume
- How Task 1 proved Bind Mount persistence
- How Task 2 proved Docker Volume persistence
- Why Docker Volumes are commonly used for application data

### 1. Difference Between a Bind Mount and a Docker Volume

A **Bind Mount** connects a specific file or directory on the host machine directly to a directory inside a Docker container. This allows the container to access and modify files stored in a chosen host location.

A **Docker Volume** is a storage mechanism managed by Docker. It stores data outside the container's writable layer, allowing data to persist independently of the container lifecycle.

### 2. How Task 1 Proved Bind Mount Persistence

In Task 1, an Nginx container was configured with a bind mount to store its access and error logs on the host machine. After generating traffic, the logs were verified on the host. The container was then removed, but the log files remained available. This demonstrated that data stored through a bind mount persists even after the container is deleted.

### 3. How Task 2 Proved Docker Volume Persistence

In Task 2, a named Docker volume called `shared-data` was created and shared between the backend and frontend containers. The backend wrote data to the shared volume, while the frontend accessed it. Both containers were removed and recreated, and the previously stored data was still accessible. This confirmed that Docker volumes preserve data independently of container removal.

### 4. Why Docker Volumes Are Commonly Used for Application Data

Docker volumes are commonly used because they provide reliable, persistent storage that is independent of containers. They make it easier to preserve important application data, share data between containers, and manage storage without depending on specific host directory paths. They are especially useful for databases, application uploads, logs, and other data that must survive container recreation.

---

# Public Application URL

**Application URL:** `3.15.202.63

---

# LinkedIn Requirement

## Goal

Create a LinkedIn post about Docker Volumes and Bind Mounts, including one difference between them, how you verified persistent storage, and your key learning outcomes.

### Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`https://www.linkedin.com/posts/oluwatobiloba-adeje-2572b42a6_devops-docker-containerization-ugcPost-7521150787226333184-ubP4/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAEm6D2MBiHlTtqXxAdNL2_2Taiskof8w_Lw`

---

#### LinkedIn Post Screenshot

![screenshot](./screenshots/ass4-post.png)

---

# Submission Instructions

- Complete all tasks in sequence.
- Include Screenshots 1–22 exactly as specified.
- Include the Storage Persistence Notes section.
- Include the public application URL.
- Include the LinkedIn post URL and screenshot.
- Ensure that your full name is visible in all terminal screenshots.
- Add your full name as a clear caption below every browser screenshot.
- Do not expose private keys, passwords, access keys, tokens, account IDs, or other sensitive information.

---

# Completion Checklist

- [x] Nginx image pulled successfully
- [x] Host log directory created
- [x] Bind Mount configured successfully
- [x] Nginx logs remain after container removal
- [x] Custom Docker network created
- [x] Docker Volume created
- [x] Backend Dockerfile and image created
- [x] Frontend Dockerfile and image created
- [x] Both containers mount `shared-data`
- [x] Backend writes data to the Docker Volume
- [x] Frontend reads the same data from the Docker Volume
- [x] Updated data appears after browser refresh
- [x] Data remains after frontend and backend containers are removed and recreated
- [x] All required screenshots included
- [x] Storage Persistence Notes completed
- [x] Public application URL included
- [x] LinkedIn post URL and screenshot included
- [x] Full name visible in terminal screenshots
- [x] Browser screenshots have full-name captions
- [x] No sensitive information exposed

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

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*
