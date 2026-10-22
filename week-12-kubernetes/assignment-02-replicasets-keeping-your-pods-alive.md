# Assignment 02 — ReplicaSets: Keeping Your Pods Alive

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will create three NGINX Pods using a ReplicaSet, test auto-healing by deleting one Pod, and scale the ReplicaSet from three to five Pods.

---

# Task 0 — Pre-check: Remove the Pod from Assignment 01

## Goal

Confirm that you are connected to your learning cluster and remove the previous standalone Pod to prevent the ReplicaSet from adopting it.

### Evidence

No screenshots required.

---

# Task 1 — Set Up Your Working Directory

## Goal

Create and enter a dedicated directory for this ReplicaSet lab.

### Evidence

#### Screenshot 01 — Output of `pwd` showing the working directory ending in `/k8s-labs/replicasets`

![screenshot](./screenshots/ass2-screen1.png)

---

### Notes

**1. Why is it useful to keep Kubernetes manifests in organized directories?**

keeping Kubernetes manifests in organized directories makes files easier to find and manage.
It helps group related resources like Pods, Services, and Deployments.
It simplifies updating and maintaining configurations.
It makes tracking changes through Git easier.
It allows configurations to be reused across different environments.
It improves team collaboration and simplifies deployments.

---

**2. What file will you create in this directory for this assignment?**

I will create kubernetes replicaset yaml manifests

---

# Task 2 — Create and Apply the NGINX ReplicaSet

## Goal

Create a ReplicaSet that maintains three NGINX Pods.

### Evidence

#### Screenshot 02 — Completed `nginx-replicaset.yaml` manifest showing `replicas: 3`

![screenshot](./screenshots/ass2-screen2.png)

---

#### Screenshot 03 — Output of `kubectl apply -f nginx-replicaset.yaml`

![screenshot](./screenshots/ass2-screen3.png)

---

#### Screenshot 04 — Output of `kubectl get pods` showing three NGINX Pods with `1/1` under `READY` and `Running` under `STATUS`

![screenshot](./screenshots/ass2-screen4.png)

---

### Notes

**1. What does `replicas: 3` mean in this manifest?**

replicas: 3 means Kubernetes will maintain three identical copies (Pods) of the application. If one Pod fails, Kubernetes will try to create another to maintain the desired number of three replicas.

---

**2. Why must `selector.matchLabels` match `template.metadata.labels`?**

selector.matchLabels must match template.metadata.labels so that the Deployment can identify and manage the Pods it creates. If they do not match, Kubernetes cannot correctly manage the Pods, and the Deployment may fail to create.

---

**3. What image and image tag are used for the NGINX container?**

he NGINX container uses the image nginx, and the image tag depends on the manifest. If it specifies nginx:latest, the image is nginx and the tag is latest.

---

# Task 3 — Test ReplicaSet Auto-Healing

## Goal

Delete one Pod managed by the ReplicaSet and observe Kubernetes automatically create a replacement.

### Evidence

#### Screenshot 05 — Output of the command deleting one ReplicaSet-managed Pod

![screenshot](./screenshots/ass2-screen5.png)

---

#### Screenshot 06 — Output of `kubectl get pods` showing the replacement Pod with a different name from the deleted Pod

![screenshot](./screenshots/ass2-screen6.png)

---

#### Screenshot 07 — Output showing three NGINX Pods with `1/1` under `READY` and `Running` under `STATUS` again

![screenshot](./screenshots/ass2-screen7.png)

---

### Notes

**1. What happened after you deleted one Pod?**

After deleting one Pod, Kubernetes automatically created a new Pod to maintain the desired number of replicas specified in the Deployment.

---

**2. How does the ReplicaSet know that a replacement Pod is needed?**

The ReplicaSet continuously monitors the number of running Pods and compares it with the desired replica count. When one Pod is deleted, it detects the difference and creates a replacement Pod to maintain the desired number.

---

**3. What proves that auto-healing worked successfully?**

The auto-healing worked successfully because Kubernetes automatically created a replacement Pod after one Pod was deleted, maintaining the desired number of replicas.

---

# Task 4 — Manually Scale the ReplicaSet

## Goal

Scale the ReplicaSet from three NGINX Pods to five NGINX Pods by changing the YAML manifest.

### Evidence

#### Screenshot 08 — Updated `nginx-replicaset.yaml` showing `replicas: 5`

![screenshot](./screenshots/ass2-screen8.png)

---

#### Screenshot 09 — Output of `kubectl get pods` showing five NGINX Pods with `1/1` under `READY` and `Running` under `STATUS`

![screenshot](./screenshots/ass2-screen9.png)

---

### Notes

**1. What change did you make to scale the ReplicaSet?**

I changed the replicas value to 5, so Kubernetes scaled the ReplicaSet to maintain five Pods running simultaneously.

---

**2. Did you manually create the additional Pods? Explain why or why not.**

No, I did not manually create the additional Pods. Kubernetes automatically created them because I changed the replicas value to 5, and the ReplicaSet ensured that five Pods were running as desired.

---

**3. What would happen if you changed the replica count from five back to three?**

If I changed the replica count from five back to three, Kubernetes would automatically terminate two Pods and maintain three running Pods to match the desired state.

---

# LinkedIn Requirement

Create a LinkedIn post that includes:

- A short explanation of what a Kubernetes ReplicaSet does.
- What happened when you deleted one NGINX Pod.
- How Kubernetes automatically created a replacement Pod to maintain the desired replica count.
- What you learned by changing the replica count from three to five.
- One screenshot showing either:
  - Three Pods running after the auto-healing test, or
  - Five Pods running after scaling the ReplicaSet.

Do not share sensitive information, cluster credentials, tokens, or kubeconfig details in your post.

### Evidence

#### Screenshot 10 — Published LinkedIn post showing your name, the required explanation, and the attached Pod screenshot

Add your screenshot here.

---

### Post Link

Add the URL of your published LinkedIn post here.

---

# Submission Instructions

- Add all required screenshots, numbered 01–10, to this template.
- Full Name must be visible in required screenshots.
- Ensure screenshots are clear and readable.
- Submit the final `nginx-replicaset.yaml` file showing `replicas: 5`. Screenshot 02 provides evidence of the initial manifest with `replicas: 3`.
- Complete the Notes sections in Tasks 1–4 in your own words.
- Include the published LinkedIn post URL.
- Do not expose tokens, passwords, private keys, kubeconfig files, or account IDs.

---

# Completion Checklist

- [x] Completed Task 0 and confirmed that the previous `nginx-pod` is no longer listed
- [x] Created and entered the ReplicaSet working directory (Screenshot 01)
- [x] Created `nginx-replicaset.yaml` with an initial replica count of three (Screenshot 02)
- [x] Applied the ReplicaSet manifest successfully (Screenshot 03)
- [x] Confirmed three NGINX Pods are running (Screenshot 04)
- [x] Deleted one ReplicaSet-managed Pod (Screenshot 05)
- [x] Observed the automatically created replacement Pod (Screenshot 06)
- [x] Confirmed three NGINX Pods are running again (Screenshot 07)
- [x] Changed the replica count from three to five (Screenshot 08)
- [x] Confirmed five NGINX Pods are running (Screenshot 09)
- [x] Submitted the final `nginx-replicaset.yaml` file with `replicas: 5`
- [x] Completed the Notes sections in Tasks 1–4
- [x] Published the LinkedIn post with the required explanation and Pod screenshot (Screenshot 10)
- [x] Included the published LinkedIn post URL
- [x] Included all required screenshots
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

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track.*
