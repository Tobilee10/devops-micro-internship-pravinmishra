# Assignment 8 — Kubernetes Services (NodePort)

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

Create a healthy NGINX Deployment, expose it through a NodePort Service, test access using a node IP and NodePort, and observe how the Service behaves when a backend Pod is replaced.

---

# Task 1 — Set Up Your Lab Directory

## Goal

Create and enter a dedicated directory for the NodePort Service lab.

### Evidence

#### Screenshot 1 — Terminal showing that you are working inside `~/k8s-labs/services/nodeport`

![screenshot](./screenshots/ass7-screen1.png)

### Notes

**1. What is the difference between ClusterIP and NodePort?**

ClusterIP is the default Kubernetes Service type that allows applications to communicate within the cluster. It uses an internal IP address and is not directly accessible from outside the cluster by default. NodePort allows applications to be accessed from outside the cluster through a node's IP address and a specific port. ClusterIP is mainly used for internal communication, while NodePort is used to expose applications to external users.

**2. What does a NodePort Service allow users to do?**

A NodePort Service allows users to access applications running inside a Kubernetes cluster from outside the cluster. It exposes the application through a specific port on each Kubernetes node. Users can access the application using the node's IP address and the assigned NodePort.

---

# Task 2 — Create a Healthy NGINX Deployment

## Goal

Create a healthy NGINX Deployment that can act as the backend for the NodePort Service.

### Evidence

#### Screenshot 2 — Completed `00-nginx-deploy.yaml` manifest

![screenshot](./screenshots/ass7-screen2.png)

---

#### Screenshot 3 — Output showing successful rollout and two ready NGINX Pods

![screenshot](./screenshots/ass7-screen3.png)

### Notes

**1. Which label will the NodePort Service use to find the NGINX Pods?**

The NodePort Service will use the nginx label to identify and select the NGINX Pods. The Pods must have a matching label for the Service to route traffic to them.

**2. Why must the Pods be ready before they can receive Service traffic?**

Pods must be ready to ensure that the application is running correctly and can handle incoming requests. Kubernetes normally sends Service traffic only to ready Pods, preventing users from accessing applications that are not yet available.

**3. What port does the NGINX container listen on?**

The NGINX container listens on port 80 by default for HTTP traffic. The Service can forward incoming requests to this port so users can access the NGINX web server.

---

# Task 3 — Create the NodePort Service

## Goal

Create a NodePort Service that exposes the NGINX application through a port on every cluster node.

### Evidence

#### Screenshot 4 — Completed `01-nginx-svc-nodeport.yaml` manifest

![screenshot](./screenshots/ass7-screen4.png)

---

#### Screenshot 5 — Output showing the NodePort Service and its endpoints or EndpointSlices

![screenshot](./screenshots/ass7-screen5.png)

### Notes

**1. What is the purpose of `type: NodePort`?**

The purpose of type: NodePort is to expose an application running inside a Kubernetes cluster to users outside the cluster. It opens a specific port on each node so external users can access the application using the node's IP address and port number.

**2. What is the difference between `port`, `targetPort`, and `nodePort`?**

port is the port exposed by the Kubernetes Service, while targetPort is the port on which the application container receives traffic. nodePort is the port opened on each Kubernetes node to allow external access to the application. Together, these ports allow traffic to flow from external users through the Service to the application Pod.

**3. What NodePort was assigned or selected in your environment?**

The NodePort was assigned in my environment is 30475

**4. Why do Service endpoints need ready Pods?**

Service endpoints need ready Pods to ensure that incoming traffic is sent only to applications that are running and ready to handle requests. If a Pod is not ready, Kubernetes normally excludes it from the Service's usable endpoints. This helps prevent failed requests and ensures reliable application access.

---

# Task 4 — Find a Node IP and Test the NodePort

## Goal

Find a reachable node IP and access the NodePort Service.

### Evidence

#### Screenshot 6 — Output of `kubectl get nodes -o wide`

![screenshot](./screenshots/ass7-screen6.png)

---

#### Screenshot 7 — Output showing the `tester` Pod running

![screenshot](./screenshots/ass7-screen7.png)

---

#### Screenshot 8 — Successful NodePort response from the laptop or the `tester` Pod

If neither route is supported by your environment, include the failed request output and ready endpoint evidence, and explain the limitation in your Notes.

![screenshot](./screenshots/ass7-screen8.png)

### Notes

**1. Which node IP and NodePort did you test?**

The node port and IP i tested is 172.18.0.3:30475

**2. Was your laptop able to reach the NodePort directly? Explain your result.**

My laptop was unable to reach the NodePort directly because the Kubernetes node's IP address was not accessible from my laptop. However, I was able to access the application using port forwarding, which forwarded traffic from 127.0.0.1:8080 to port 80. The message Handling connection for 8080 indicates that a connection was received on the forwarded port. This shows that port forwarding provides an alternative way to access the application when direct NodePort access is unavailable.

**3. Why can external NodePort access behave differently in Minikube, kind, Docker Desktop, and cloud clusters?**

External NodePort access behaves differently because each Kubernetes environment uses a different networking setup. Minikube may require a special command or network configuration to access the Service. Kind runs Kubernetes nodes inside Docker containers, so additional port mappings may be needed. Docker Desktop uses its own networking layer to connect containers to the host machine. Cloud clusters may require appropriate firewall rules, security groups, and network routing to allow external access to the NodePort.

---

# Task 5 — Optional: Test the Label-Selector Contract

## Goal

Break the Service selector, observe that usable endpoints disappear, and restore the correct selector.

Complete the Evidence and Notes below only if you attempted this optional task.

### Evidence

#### Screenshot 9 — `02-nginx-svc-selector-broken.yaml` showing the incorrect selector

![screenshot](./screenshots/ass7-screen9.png)

---

#### Screenshot 10 — Empty endpoints or EndpointSlice output after applying the broken selector

![screenshot](./screenshots/ass7-screen10.png)

---

#### Screenshot 11 — Restored endpoints or EndpointSlices after applying the original Service manifest

![screenshot](./screenshots/ass7-screen11.png)

### Notes

**1. Why did the Service endpoints become empty?**

The Service endpoints became empty because there were no ready Pods matching the Service's label selector. Kubernetes uses labels to identify the Pods that belong to a Service. If no matching ready Pods exist, the Service has no available endpoints to forward traffic to.

**2. Why is NodePort only useful when matching ready Pods exist?**

NodePort allows external users to access an application through a specific port on a Kubernetes node. However, the Service must have matching ready Pods to handle incoming requests. If no ready Pods exist, the Service has no healthy backend to forward traffic to. Therefore, NodePort alone cannot guarantee that the application will be accessible.

**3. How did you restore the backend connectivity?**

I restored backend connectivity by checking the Service configuration and verifying that the NGINX Pods were running and ready. I ensured that the Pods had the correct nginx label so that the Service could select them. After the matching Pods became ready, Kubernetes recreated the Service endpoints and traffic could reach the application again. I then used port forwarding to verify access to the NGINX application through 127.0.0.1:8080.

---

# Task 6 — Observe Resilience During Pod Churn

## Goal

Delete one NGINX Pod and observe its replacement while the Service continues routing traffic to available Pods.

### Evidence

#### Screenshot 12 — Output of the Pod deletion command

![screenshot](./screenshots/ass7-screen12.png)

---

#### Screenshot 13 — Output showing the replacement Pod and two running, ready Pods

![screenshot](./screenshots/ass7-screen13.png)

### Notes

**1. What happened after you deleted one Pod?**

After I deleted one NGINX Pod, the application continued serving requests through the NodePort Service. The continuous curl command kept testing the application every two seconds. Even after the Pod was stopped and a replacement started, the application remained accessible because another ready Pod or the replacement Pod could handle the requests.

**2. Which Kubernetes resource created the replacement Pod?**

The Deployment created the replacement Pod automatically through its ReplicaSet. The ReplicaSet ensures that the desired number of Pod replicas remains running. When one Pod was deleted, Kubernetes created a replacement to maintain the desired state.

**3. Why can the NodePort Service continue working during Pod churn?**

AThe NodePort Service can continue working during Pod churn because it routes traffic to available, ready Pods that match its label selector. When one Pod is deleted, Kubernetes updates the Service endpoints and directs traffic to the remaining ready Pods. The Deployment and ReplicaSet work together to replace deleted Pods automatically. This helps maintain application availability during Pod restarts or replacements.

---

# Task 7 — Share Your NodePort Learning on LinkedIn

## Goal

Share what you learned about NodePort Services, backend Pods, and network access in your Kubernetes environment.

### Evidence

#### Screenshot 14 — Published LinkedIn post about your NodePort Service lab

Add your screenshot here.

#### Linkedin post link

Add your Linkdin post link.

---

# Submission Instructions

- Include the completed YAML manifests created during the lab.
- Add all required screenshots: Screenshots 1–8 and 12–14.
- Include Screenshots 9–11 only if you attempted optional Task 5.
- Complete the Notes for Tasks 1–4 and Task 6.
- Complete Task 5 Notes only if you attempted that task.
- Use clear, readable screenshots showing the relevant commands and outputs.
- Ensure your full name is visible in the required screenshots.
- If NodePort access is restricted by your environment, include the failed request output, ready endpoint evidence, and an explanation.
- Do not expose passwords, access tokens, private keys, kubeconfig contents, or account IDs.

---

# Completion Checklist

- [ ] Created and entered `~/k8s-labs/services/nodeport` (Screenshot 1)
- [ ] Created and applied the NGINX Deployment manifest (Screenshot 2)
- [ ] Confirmed successful rollout and two ready NGINX Pods (Screenshot 3)
- [ ] Created the NodePort Service manifest (Screenshot 4)
- [ ] Verified the Service and its endpoints or EndpointSlices (Screenshot 5)
- [ ] Recorded the assigned or selected NodePort
- [ ] Identified a node IP (Screenshot 6)
- [ ] Confirmed that the `tester` Pod is running (Screenshot 7)
- [ ] Tested NodePort access, or documented the environment limitation with evidence (Screenshot 8)
- [ ] Optional: Tested and restored the Service selector (Screenshots 9–11)
- [ ] Deleted one NGINX Pod (Screenshot 12)
- [ ] Confirmed the replacement Pod and two running, ready Pods (Screenshot 13)
- [ ] Completed all required Notes
- [ ] Published the LinkedIn post (Screenshot 14)
- [ ] No sensitive data exposed

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
