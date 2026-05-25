# Hybrid K3s Cluster - Infrastructure Specification

This repository serves as the central **Single Source of Truth** for the declarative configuration of resources within a hybrid Kubernetes (**K3s**) cluster. The project implements a modern **Infrastructure as Code (IaC)** approach, completely separating the application source code from its runtime environment definitions.

The infrastructure is designed to simulate professional **Cloud Native** operations, deployment automation, and advanced Linux systems administration.

## 🛠️ Tech Stack and Key Concepts
* **Orchestration:** K3s (a lightweight, optimized Kubernetes distribution)
* **Operating Systems:** Flatcar Container Linux (Immutable/Atomic OS), Debian 12 (Classic Server), EndeavourOS (Arch-based Control Plane)
* **Networking & Security:** Flannel (VxLAN), Kubernetes Network Policies, Linux UFW (Uncomplicated Firewall)
* **Scalability:** Horizontal Pod Autoscaler (HPA) driven by resource metrics

## 🏗️ Hybrid Environment Architecture

The cluster is distributed across a local area network (LAN) (`192.168.8.0/24`) on three architecturally diverse nodes, demonstrating versatility in managing different Linux distribution families:

1.  **Node 1 (Control Plane + Worker + GPU)** | `192.168.8.150` | **EndeavourOS**
    * *Role:* Cluster management (API Server), Ingress controller hosting, and execution of AI/LLM workloads requiring hardware acceleration via the NVIDIA Container Toolkit and `k8s-device-plugin`.
2.  **Node 2 (Classic Worker)** | `192.168.8.160` | **Debian 12**
    * *Role:* Maintenance of traditional system services outside the cluster. It hosts a native MariaDB database instance managed by `systemd` and secured by `ufw` to demonstrate the integration of traditional server workloads with containerized environments.
3.  **Node 3 (Atomic Worker)** | `192.168.8.170` | **Flatcar Container Linux**
    * *Role:* Secure runtime environment for production applications. An immutable, *Read-Only* operating system configured fully declaratively during boot using *Ignition* files.

## 🚀 Application Deployment (TabView)

The core workload hosted on this infrastructure is **TabView**, a containerized web application running inside the isolated production environment (`prod` namespace).

### Key Deployment Characteristics:
* **Containerization:** The application runs on an image hosted via GitHub Container Registry (`ghcr.io/maxpolak04/tab_view`).
* **Decoupled Configuration:** Application settings and database credentials are completely separated from the deployment layer using Kubernetes `ConfigMaps` and `Secrets` to ensure secure credential handling.
* **High Availability & Autoscaling:** Outfitted with a `HorizontalPodAutoscaler` (HPA) configured for a minimum of 2 and a maximum of 5 replicas, dynamically scaling based on a target CPU utilization of 80%.
* **Traffic Control & Ingress:** Traffic routing is managed via an Ingress Controller mapping to a dedicated ClusterIP Service. Security is enforced through a strict `NetworkPolicy` that isolates the application, allowing ingress traffic exclusively from the cluster's core Ingress Controller in the `kube-system` namespace.

The source code and application development repository can be found here:
🔗 **[TabView Application Source Code](https://github.com/maxpolak04/tab_view)**.

## 🔐 Security Considerations & Day-2 Roadmap
* **Current State (Secrets Management):** For the scope of this academic/lab project, native Kubernetes `Secrets` are stored within the Git manifests using standard Base64 encoding. While acceptable for demonstrating container configuration with mock/test credentials in an isolated laboratory network, this represents a known anti-pattern for production GitOps workflows.
* **Production Roadmap (Day-2 Operations):** To scale this infrastructure to a production-ready state, the next phase involves implementing a secure, encrypted GitOps secrets workflow. The defined roadmap includes deploying **Bitnami Sealed Secrets** (utilizing asymmetric encryption so that encrypted manifests can safely live in public Git history and only be decrypted by the in-cluster controller) or integrating **Mozilla SOPS** backed by an external Key Management Service (KMS) or HashiCorp Vault.

## 🎓 Academic Context & Full Documentation

This repository and the underlying infrastructure were originally designed, implemented, and defended as the final engineering project for the **Linux Server Management** laboratory at Collegium Da Vinci.

The project successfully demonstrates the practical application of Linux administration, container orchestration (K3s), and modern GitOps workflows. For a deep dive into the exact installation procedures, hardware setup, network configuration, and architectural diagrams used during the defense, refer to the full presentation documentation below:

📄 **[View / Download the Full Technical Presentation (PDF)](./K3s-Hybrid-Cluster-Documentation.pdf)**

## 🎯 Key Engineering Takeaways (Value for Recruiters)
1.  **Infrastructure as Code (IaC) Paradigm:** Full mastery of declarative Kubernetes manifests. Infrastructure changes are tracked, auditable, and kept in sync.
2.  **Hybrid Cloud & Linux Administration:** Demonstrated ability to bridge immutable, container-optimized OS environments (Flatcar) with traditional Linux distributions (Debian & Arch/EndeavourOS). Strong proficiency in `systemd`, native firewalls, cluster networking, and storage layers.
3.  **Security-First Design:** Practical implementation of zero-trust network principles through micro-segmentation (**Network Policies**), secure management of environment secrets, and reducing attack surfaces via immutable operating systems.
4.  **Scalability and Resilience:** Hands-on experience configuring automated horizontal scaling (HPA) based on live resource metrics, paired with robust self-healing mechanisms via container health checks (`livenessProbe` and `readinessProbe`).