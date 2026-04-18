# Enterprise CI/CD Infrastructure (Provisioned by Ansible)

This project automates the provisioning and configuration of a core Enterprise-grade CI/CD infrastructure, encompassing Internal Networking, OS Kernel Optimization, and strict Security hardening.

## 🏗️ Architecture Highlights
- **Local DNS (Bind9):** Isolates physical IPs by routing internal traffic via domain names (e.g., `jenkins.abc`, `nexus.abc`), ensuring a decoupled network topology.
- **Reverse Proxy (Nginx):** Hides internal application ports, exposing only port 80/443 for external communication. Configured with extended `client_max_body_size` to prevent artifact upload bottlenecks.
- **Cross-OS Compatibility:** Foundational roles (`common_tools`) dynamically detect `ansible_os_family` to ensure seamless compatibility across RedHat (dnf) and Debian (apt) distributions.
- **OS Kernel Tuning:** Automatically adjusts `vm.max_map_count` for Elasticsearch (SonarQube) and unlocks `LimitNOFILE` for Nexus to prevent out-of-memory exceptions and file descriptor exhaustion.
- **Security First:** Passwords and API Tokens are strictly encrypted using **Ansible Vault** (`secrets.yml`). Firewall ports are dynamically managed via Firewalld, fully compliant with SELinux policies (`httpd_can_network_connect`).

## 🚀 Execution Flow
The `setup.yml` playbook is structured into 4 decoupled stages (Plays):
1. **Platform:** Installs the foundational Java JDK environment (All nodes).
2. **CI/CD Core:** Deploys Jenkins, Nexus, SonarQube, and GCloud CLI (Control Node).
3. **Networking:** Provisions internal DNS and Reverse Proxy rules (Control Node).
4. **App Server:** Equips the execution node with Git, Htop, and Docker Engine (Target Nodes).

## 🛠️ Usage
```bash
# Execute the full infrastructure provisioning process
ansible-playbook -i inventories/hosts setup.yml -K --ask-vault-pass
```