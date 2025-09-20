# DevOps Lab

This project sets up a **Proxmox-based DevOps Lab** with Kubernetes, GitOps, and observability tools using **Terraform** and **Ansible**.

It is designed to create a DevOps lab for **learning and testing purposes**, while still following **production-like practices**.

---

## 📌 Steps

### 1. Install Proxmox VE

Install Proxmox VE on bare metal hardware. Configure networking and storage. After installation you can use post install script.

```
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
```

### 2. Create an Ubuntu 24.04 Template for Terraform Usage

This guide outlines the steps to create an Ubuntu 24.04 template for use with Terraform. The template creation process is based on [this blog post](https://thepaulo.medium.com/create-an-ubuntu-24-04-template-with-cloud-init-on-proxmox-f092080cecfb)

**Prerequisites:**

- Proxmox environment set up and running.
- Access to the Proxmox shell with appropriate privileges.
- Basic familiarity with Terraform and Proxmox CLI commands.


**Steps:** 
1. **Download the Ubuntu 24.04 Cloud Image:**
    ```
    wget -P /var/lib/vz/template/iso/ https://cloud-images.ubuntu.com/daily/server/releases/24.04/release/ubuntu-24.04-server-cloudimg-amd64.img
    ```

2. **Create Virtual Machine**

    Create a VM with the desired resources (e.g., 2 GB of RAM, 2 CPU cores). Adjust the parameters as necessary.

    ```
    qm create 9000 --name "ubuntu-24.04-template" --memory 2048 --cores 2 --net0 virtio,bridge=vmbr0
    ```

3. **Import the Cloud Image as the Disk for the VM:**
    ```
    qm importdisk 9000 /var/lib/vz/template/iso/ubuntu-24.04-server-cloudimg-amd64.img local-lvm
    ```

4. **Configure the VM to Use a VirtIO Disk:**
    ```
    qm set 9000 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9000-disk-0
    ```

5. **Set the Boot Disk:**
    ```
    qm set 9000 --boot c --bootdisk scsi0
    ```

6. **Add a Cloud-Init Disk:**
    ```
    qm set 9000 --ide2 local-lvm:cloudinit
    ```

7. **Create the Template:**
    ```
    qm template 9000
    ```

> Note: I didn’t configure Cloud-Init at this stage because I plan to use Terraform for provisioning and will pass Cloud-Init parameters during that process.

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

