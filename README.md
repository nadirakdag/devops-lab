# DevOps Lab

This project sets up a **Proxmox-based DevOps Lab** with Kubernetes, GitOps, and observability tools using **Terraform** and **Ansible**.
It’s designed for homelab use, but follows production-like practices.

---

## 📌 Steps

### 1. Install Proxmox VE on Bare Metal

* Download [Proxmox VE ISO](https://www.proxmox.com/en/downloads).
* Install on bare metal server.
* Configure networking (`vmbr0` and optional VLANs).
* Update system:

  ```bash
  apt update && apt full-upgrade -y
  ```

---

### 2. Create Templates

* **VM Template** (Ubuntu Server with cloud-init)

  ```bash
  qm create 9000 --name "ubuntu-24.04-template" --memory 2048 --cores 2 --net0 virtio,bridge=vmbr0
  qm importdisk 9000 ubuntu-24.04-server-cloudimg-amd64.img local-lvm
  qm set 9000 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9000-disk-0
  qm set 9000 --ide2 local-lvm:cloudinit
  qm set 9000 --boot c --bootdisk scsi0
  qm template 9000
  ```

* **LXC Template** (Ubuntu Server)

  ```bash
  pveam update
  pveam available | grep ubuntu
  pveam download local ubuntu-24.04-standard_24.04-1_amd64.tar.zst
  ```

---

### 3. Infrastructure as Code with Terraform

* Define networks, VM, and LXC resources.
* Example folder structure:

  ```
  terraform/
    main.tf
    variables.tf
    outputs.tf
    kubernetes-vms.tf
    lxc-containers.tf
    network.tf
  ```
* Provision:

  ```bash
  terraform init
  terraform apply
  ```

---

### 4. Configuration with Ansible

* Use Ansible to install and configure everything inside the provisioned VMs and LXCs.

#### 4.1 Monitoring for Proxmox

* Install node exporter, smartmontools, etc.
* Optional: integrate with Prometheus/Grafana.

#### 4.2 Kubernetes Cluster

* Install Kubernetes (control plane + workers).
* Configure kubeadm or k3s.

#### 4.3 Core Services

* Install CNI + services:

  * **Calico** → pod networking.
  * **MetalLB** → LoadBalancer IPs.
  * **Longhorn** → storage.
  * **Ingress Controller** → NGINX/Traefik.

#### 4.4 Gitea (LXC)

* Install Gitea in its LXC.
* Configure persistent storage.
* Setup HTTPS (Let’s Encrypt or reverse proxy).

#### 4.5 ArgoCD (LXC)

* Install ArgoCD in its LXC.
* Connect to Kubernetes + Gitea repo.

---

### 5. Observability

* Deploy:

  * **Prometheus** (metrics).
  * **Grafana** (dashboards).
  * **Loki** (logs).

---

## 📂 Project Structure

```
devops-lab/
├── README.md
├── terraform/
│   ├── main.tf
│   ├── network.tf
│   ├── kubernetes-vms.tf
│   └── lxc-containers.tf
└── ansible/
    ├── inventory.ini
    ├── playbooks/
    │   ├── proxmox-monitoring.yml
    │   ├── kubernetes.yml
    │   ├── gitea.yml
    │   └── argocd.yml
    └── roles/
```

---

## 🔄 Workflow

1. Proxmox installed + templates ready.
2. Use **Terraform** to provision infra.
3. Use **Ansible** to configure services.
4. Push code to **Gitea** → ArgoCD deploys it to Kubernetes.
5. Monitor with Prometheus + Grafana.

---

👉 This `README.md` is a **living document**:

* Add **exact Terraform/Ansible commands** as you implement.
* Document **IP addresses, resource sizes, storage configs**.
* Extend with **CI/CD pipelines** later if you want.
