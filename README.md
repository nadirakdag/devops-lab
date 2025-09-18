# DevOps Lab

This project sets up a **Proxmox-based DevOps Lab** with Kubernetes, GitOps, and observability tools using **Terraform** and **Ansible**.

It is designed to create a DevOps lab for **learning and testing purposes**, while still following **production-like practices**.

---

## 📌 Steps

### 1. Install Proxmox VE

* Install Proxmox VE on bare metal hardware. Configure networking and storage.

### 2. Create Templates

* Create an **Ubuntu 24.04** template for Terraform usage.

### 3. Infrastructure as Code with Terraform

* Define VM and LXC resources.


### 4. Configuration with Ansible

* Use Ansible to configure VMs and LXCs after provisioning.

#### 4.1 Monitoring for Proxmox

* Install node exporter, smartmontools, etc.
* Optional: integrate with Prometheus/Grafana.

#### 4.2 Kubernetes Cluster

* Install Kubernetes (control plane + workers).
* Configure kubeadm

#### 4.3 Core Services

* Install CNI + services:

  * **Calico** → pod networking.
  * **MetalLB** → LoadBalancer IPs.
  * **Longhorn** → storage.
  * **Ingress Controller** → NGINX.

#### 4.4 Gitea (LXC)

* Install Gitea in its LXC.
* Configure persistent storage.

#### 4.5 ArgoCD (LXC)

* Install ArgoCD in its LXC.
* Connect to Kubernetes + Gitea repo.

### 5. Observability

