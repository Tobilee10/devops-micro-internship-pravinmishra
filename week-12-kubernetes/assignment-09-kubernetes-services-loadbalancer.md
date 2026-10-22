# Assignment 09 — Kubernetes Services: LoadBalancer on AKS

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

Deploy a healthy NGINX application to Azure Kubernetes Service (AKS), expose it publicly using a LoadBalancer Service, observe readiness behavior, and scale the application behind the same public endpoint.

---

# Task 1 — Create or Connect to an AKS Cluster

## Goal

Create a small AKS cluster for this lab, or connect to an existing approved AKS lab cluster.

### Evidence

#### Screenshot 1 — Output of `kubectl get nodes` showing ready AKS node(s)

Add your screenshot here.

### Notes

**1. Why does this lab use a cloud-capable Kubernetes environment such as AKS for the LoadBalancer Service?**

This lab uses Azure Kubernetes Service (AKS) because it supports integration between Kubernetes and Azure cloud resources. When a LoadBalancer Service is created, AKS can automatically configure an Azure Load Balancer. It can also assign a public IP address to expose the application to external users. This allows users to access applications running inside the Kubernetes cluster from outside. A cloud-capable environment makes it easier to demonstrate how Kubernetes manages external access to applications.

**2. What Azure resources can Kubernetes provision when a LoadBalancer Service is created?**

When a LoadBalancer Service is created in AKS, Kubernetes can trigger Azure to provision or configure several cloud resources. These include an Azure Load Balancer to distribute incoming traffic and a public IP address for external access. Azure can also configure load-balancing rules and health probes to manage traffic and check the availability of backend nodes. Network settings, such as backend pools, may also be updated. These resources allow applications running in Kubernetes to receive external traffic efficiently.

**3. Why is it important to delete unused AKS resources after the lab?**

It is important to delete unused AKS resources after the lab because Azure resources may continue to incur charges even when they are no longer needed. Resources such as AKS clusters, load balancers, and public IP addresses can increase cloud expenses. Cleaning up unused resources helps prevent unexpected billing costs. It also frees up resources and keeps the Azure environment organized. Therefore, deleting the resources after completing the lab is a good cloud management practice.

---

# Task 2 — Set Up Your Lab Directory

## Goal

Create and enter a dedicated directory for the LoadBalancer Service manifests.

### Evidence

#### Screenshot 2 — Terminal showing the output of `pwd` confirming that you are working inside `~/k8s-labs/services/loadbalancer`

Add your screenshot here.

### Notes

**1. Why is it useful to keep Kubernetes manifests organized in dedicated directories?**

Keeping Kubernetes manifests in dedicated directories makes project files easier to organize, find, and manage. It helps separate configuration files for Deployments, Services, and other Kubernetes resources. This improves readability and reduces confusion when working with multiple resources. It also makes updating, testing, and maintaining configurations easier. Overall, organized directories help keep Kubernetes projects clean and manageable.

**2. What are the names of the main Deployment and LoadBalancer Service YAML files you will create in this assignment?**

The main YAML files are typically nginx-deploy.yaml and nginx-svc-loadbalance.yaml. 

---

# Task 3 — Deploy a Healthy NGINX Application

## Goal

Create a healthy NGINX Deployment with readiness and liveness probes.

### Evidence

#### Screenshot 3 — Completed `00-nginx-deploy.yaml` manifest

Add your screenshot here.

---

#### Screenshot 4 — Output showing successful rollout and two ready NGINX Pods

Add your screenshot here.

### Notes

**1. Why must Pods be ready before they should receive public traffic?**

Pods must be ready before receiving public traffic to ensure that the application can handle incoming requests correctly. If traffic is sent to a Pod that is not fully initialized, users may experience errors or failed connections. Kubernetes uses readiness checks to determine whether a Pod is ready to serve requests.

**2. What is the role of the readiness probe in this Deployment?**

The readiness probe checks whether a container is ready to accept incoming requests. It helps Kubernetes determine when an application has started successfully and can handle traffic. If the probe fails, Kubernetes marks the Pod as not ready and stops sending Service traffic to it. Once the probe succeeds again, the Pod can receive traffic. This helps prevent requests from reaching applications that are temporarily unavailable or still starting.

**3. What is the role of the liveness probe in this Deployment?**

The liveness probe checks whether the application inside a container is still functioning properly. If the probe repeatedly fails, Kubernetes restarts the container to recover from the problem. This helps resolve issues such as a frozen application or an unresponsive process. Unlike the readiness probe, the liveness probe determines whether a container needs to be restarted. It improves application reliability by helping Kubernetes automatically recover from certain failures.

---

# Task 4 — Create and Test the LoadBalancer Service

## Goal

Create a public LoadBalancer Service and verify that the NGINX application is reachable from the internet.

### Evidence

#### Screenshot 5 — Completed `01-nginx-svc-loadbalancer.yaml` manifest

Add your screenshot here.

---

#### Screenshot 6 — Output showing the Service with an assigned EXTERNAL-IP

Add your screenshot here.

---

#### Screenshot 7 — Successful curl output from the public IP

Add your screenshot here.

### Notes

**1. What does `type: LoadBalancer` instruct AKS to do?**

The type: LoadBalancer instructs AKS to expose the Kubernetes Service externally using an Azure Load Balancer. It allows applications running inside the cluster to receive traffic from outside. Azure automatically provisions or configures the necessary load-balancing resources and a public IP address. This makes the application accessible over the internet. It is commonly used to expose web applications and other services to external users.

**2. What public IP was assigned to your Service?**

The public IP address assigned to the Service can be found by running the command kubectl get services. The output displays the Service name, type, cluster IP, and external IP address. The IP address listed under the EXTERNAL-IP column is the public IP assigned to the Service. The exact address depends on the Azure environment and the Service configuration. You should enter the actual IP address displayed in your terminal.

**3. How does the public request eventually reach the NGINX Pods?**

When a user sends a request to the Service's public IP address, the Azure Load Balancer receives the incoming traffic. It forwards the request to an appropriate node in the AKS cluster. Kubernetes networking then routes the request to a healthy NGINX Pod through the Service. The NGINX container processes the request and returns the response. This process allows external users to access the application running inside the cluster.

**4. Why should you wait for EXTERNAL-IP before testing the Service?**

You should wait for the EXTERNAL-IP to appear because Azure needs time to provision and configure the required networking resources. While the address is shown as pending, the Service may not yet be accessible externally. Testing too early can result in connection failures or timeouts. Once the public IP address is assigned, you can use it to access the application from a browser or another client. Waiting ensures that the Service is ready for external testing.

---

# Task 5 — Observe How Readiness Protects Public Traffic

## Goal

Intentionally break readiness and observe how Kubernetes excludes unready Pods from ready Service backends while existing healthy Pods continue serving public traffic.

### Evidence

#### Screenshot 8 — Completed `02-readiness-broken.yaml` patch file

Add your screenshot here.

---

#### Screenshot 9 — Output showing the expected incomplete rollout or readiness failure

Add your screenshot here.

---

#### Screenshot 10 — EndpointSlice output showing ready healthy backends and the effect of the readiness failure

Add your screenshot here.

---

#### Screenshot 11 — Successful public curl response after recovery

Add your screenshot here.

### Notes

**1. Why did the rollout not complete?**

The rollout did not complete because the newly created Pods failed their readiness probes. Kubernetes waits for new Pods to become ready before completing the rollout. Since the new Pods were not ready, the Deployment could not successfully replace the existing Pods. This prevented the rollout from completing successfully.

**2. Why can the public endpoint still work while new Pods fail readiness?**

The public endpoint can still work because the existing healthy Pods continue to serve incoming requests. Kubernetes uses readiness probes to determine which Pods are ready to receive traffic. When new Pods fail readiness checks, they are excluded from the Service's available endpoints. The Azure Load Balancer and Kubernetes Service can continue routing requests to healthy Pods. Therefore, the application may remain accessible even when the latest Deployment rollout is unsuccessful.

**3. What would happen if Kubernetes routed traffic to Pods before they were ready?**

If Kubernetes routed traffic to Pods before they were ready, users might experience failed requests, connection errors, or incomplete responses. The application might not have finished starting or could be unable to process incoming requests correctly. This would reduce the reliability and availability of the service. Readiness probes help prevent these problems by ensuring that only ready Pods receive traffic. As a result, users are more likely to receive successful responses.

**4. How did you restore the Deployment successfully?**

The Deployment was restored by correcting the configuration that caused the readiness probes to fail. The updated configuration was applied using kubectl apply, allowing Kubernetes to recreate or update the affected Pods. The Deployment was then monitored using kubectl rollout status deployment/<deployment-name> to confirm that the rollout completed. The Pod status and readiness checks were also verified using kubectl get pods.

---

# Task 6 — Scale the Deployment Behind the Load Balancer

## Goal

Scale the NGINX Deployment and confirm that more ready Pods become available behind the same public endpoint.

### Evidence

#### Screenshot 12 — Two ready NGINX Pods verified initially; four ready Pods verified after scaling

Add your screenshot here.

---

#### Screenshot 13 — Successful public curl response after scaling

Add your screenshot here.

### Notes

**1. What changed after scaling the Deployment from two to four replicas?**

After scaling the Deployment from two to four replicas, Kubernetes created two additional Pods to run the application. This increased the number of available application instances and allowed the workload to be distributed across more Pods. The LoadBalancer Service continued directing incoming traffic to the ready Pods. Scaling improved the application's capacity to handle multiple requests simultaneously. It also increased availability by providing more application instances to serve traffic.

**2. Did the public IP change after scaling? Explain why or why not.**

No, the public IP address did not change after scaling the Deployment. The public IP belongs to the LoadBalancer Service rather than to individual Pods. Scaling only changes the number of Pods running the application, while the Service remains the same. Kubernetes automatically updates the Service's endpoints to include the additional ready Pods. Therefore, the same public IP address continues to provide access to the application.

**3. Why does scaling behind a LoadBalancer Service not require users to change the URL or IP they use?**

Scaling behind a LoadBalancer Service does not require users to change the URL or IP because the Service provides a stable network endpoint. The LoadBalancer continues receiving requests through the same public IP address or DNS name. Kubernetes automatically distributes traffic among the available, ready Pods behind the Service. When Pods are added or removed, the Service updates its endpoints accordingly. This allows the application to scale without affecting how users access it.

---

# Task 7 — Share Your LoadBalancer Lab on LinkedIn

## Goal

Share what you learned about exposing an NGINX application publicly through a LoadBalancer Service on AKS.

### Evidence

#### Screenshot 14 — Published LinkedIn post about your LoadBalancer lab on AKS

Add your screenshot here.

#### LinkedIn post link

Add your link here.

---

# Submission Instructions

- Include the completed YAML files:
  - `00-nginx-deploy.yaml`
  - `01-nginx-svc-loadbalancer.yaml`
  - `02-readiness-broken.yaml`
- Add all required screenshots, numbered 1–14, under their corresponding Evidence sections.
- Use clear, readable screenshots showing the relevant commands, configuration, and outputs.
- Ensure your full name is visible in the required screenshots.
- Complete the Notes for Tasks 1–6 in your own words.
- Capture all required evidence before cleaning up Azure resources.
- Plan or complete Azure cleanup according to your lab ownership and reuse arrangements.
- Do not expose passwords, tokens, private keys, cluster credentials, kubeconfig contents, subscription IDs, account IDs, or infrastructure details you are not permitted to share.

---

# Completion Checklist

- [ ] Created or connected to an approved AKS lab cluster
- [ ] Confirmed ready AKS node(s) (Screenshot 1)
- [ ] Created and entered the LoadBalancer lab directory (Screenshot 2)
- [ ] Created the NGINX Deployment manifest (Screenshot 3)
- [ ] Verified successful rollout and two ready NGINX Pods initially (Screenshot 4)
- [ ] Created the LoadBalancer Service manifest (Screenshot 5)
- [ ] Verified the assigned external IP (Screenshot 6)
- [ ] Verified successful public access using curl (Screenshot 7)
- [ ] Created the broken readiness patch (Screenshot 8)
- [ ] Observed the expected incomplete rollout or readiness failure (Screenshot 9)
- [ ] Inspected EndpointSlices during the readiness failure (Screenshot 10)
- [ ] Restored the healthy Deployment and verified public access (Screenshot 11)
- [ ] Scaled the Deployment to four ready Pods and verified updated Service backends (Screenshot 12)
- [ ] Verified public access after scaling (Screenshot 13)
- [ ] Completed the Notes for Tasks 1–6
- [ ] Published the LinkedIn post (Screenshot 14)
- [ ] Included all required YAML files and screenshots
- [ ] Planned or completed Azure cleanup
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
