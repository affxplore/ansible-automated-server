# Automated Server Provisioning & Security Hardening with Ansible

## 📖 Project Overview

This project demonstrates Infrastructure as Code (IaC) principles by automating the provisioning and configuration of multiple Linux servers using Ansible.

The environment consists of one Ansible Control Node and two managed servers running on VMware. The playbook automates operating system updates, software installation, service configuration, web deployment, and firewall hardening.

A multi-tier architecture is implemented by separating the Web Server and Database Server while restricting database access using firewall policies.

---

## 🎯 Objectives

* Automate server provisioning using Ansible.
* Deploy a Web Server (Nginx) automatically.
* Deploy a Database Server (MySQL) automatically.
* Configure firewall rules using UFW.
* Implement secure communication between application and database tiers.
* Eliminate repetitive manual server configuration tasks.

---

## 🏗️ Infrastructure Architecture

```text
                +----------------------+
                |   Control Node       |
                |      (Ansible)       |
                +----------+-----------+
                           |
                     SSH (Port 22)
                           |
        +------------------+------------------+
        |                                     |
        v                                     v

+-------------------+             +-------------------+
|    Web Server     |             |  Database Server  |
| Ubuntu 22.04 LTS  |             |  Ubuntu 22.04 LTS |
| Nginx             |             |  MySQL            |
| UFW Firewall      |             |  UFW Firewall     |
| 192.168.253.130   |             |  192.168.253.xxx  |
+-------------------+             +-------------------+

              HTTP/HTTPS
                  |
                  v

              End Users
```

---

## 🛠️ Technology Stack

| Category         | Technology              |
| ---------------- | ----------------------- |
| Automation       | Ansible                 |
| Virtualization   | VMware                  |
| Operating System | Ubuntu Server 22.04 LTS |
| Web Server       | Nginx                   |
| Database         | MySQL                   |
| Firewall         | UFW                     |
| Remote Access    | SSH                     |

---

## 📂 Repository Structure

```text
.
├── inventory.ini
├── playbook.yml
├── server1.html
└── README.md
```

### File Description

| File          | Description                           |
| ------------- | ------------------------------------- |
| inventory.ini | Defines managed hosts and groups      |
| playbook.yml  | Main automation playbook              |
| server1.html  | Custom landing page deployed to Nginx |
| README.md     | Project documentation                 |

---

## ⚙️ Automated Tasks

### Web Server Configuration

The playbook automatically:

* Updates package repositories.
* Upgrades installed packages.
* Installs Nginx.
* Enables and starts the Nginx service.
* Deploys a custom landing page.
* Configures UFW firewall rules.
* Allows:

  * SSH (22)
  * HTTP (80)
  * HTTPS (443)

### Database Server Configuration

The playbook automatically:

* Updates package repositories.
* Upgrades installed packages.
* Installs MySQL Server.
* Enables and starts the MySQL service.
* Configures UFW firewall rules.
* Allows:

  * SSH (22)
* Restricts MySQL access:

  * TCP 3306 accessible only from the Web Server IP (`192.168.253.130`)
  * Blocks public access to MySQL

---

## 🔒 Security Hardening

### Firewall Segmentation

Database access is restricted using UFW firewall rules.

```yaml
- name: Allow MySQL traffic specifically from Server 1 IP
  ufw:
    rule: allow
    from_ip: 192.168.253.130
    port: '3306'
    proto: tcp
```

Only the Web Server can communicate with MySQL.

### Principle of Least Privilege

Only required ports are exposed.

| Server          | Allowed Ports              |
| --------------- | -------------------------- |
| Web Server      | 22, 80, 443                |
| Database Server | 22, 3306 (Web Server only) |

### Information Disclosure Reduction

The default Nginx welcome page is replaced with a custom landing page to minimize unnecessary service information exposure.

---

## 🚀 Deployment

Clone the repository:

```bash
git clone https://github.com/affxplore/ansible-automated-server.git

cd ansible-automated-server
```

Verify inventory configuration:

```bash
cat inventory.ini
```

Run the playbook:

```bash
ansible-playbook -i inventory.ini playbook.yml
```

---

## ✅ Verification & Testing

### Verify Web Server Deployment

Open:

```text
http://192.168.253.130
```

Expected result:

```text
Deployment Successful!
This server was configured automatically using Ansible.
```

Verify Nginx service:

```bash
sudo systemctl status nginx
```

Expected:

```text
active (running)
```

---

### Verify Database Service

```bash
sudo systemctl status mysql
```

Expected:

```text
active (running)
```

---

### Verify Firewall Rules

Web Server:

```bash
sudo ufw status numbered
```

Expected:

```text
22/tcp ALLOW
80/tcp ALLOW
443/tcp ALLOW
```

Database Server:

```bash
sudo ufw status numbered
```

Expected:

```text
22/tcp ALLOW
3306/tcp ALLOW FROM 192.168.253.130
```

---

### Verify Database Isolation

From Web Server:

```bash
nc -zv <database-server-ip> 3306
```

Expected:

```text
Connection succeeded
```

From Host Machine:

```bash
nc -zv <database-server-ip> 3306
```

Expected:

```text
Connection timed out
```

This confirms that the database is protected from unauthorized external access.

---

## 📸 Screenshots

Include screenshots for:

1. Successful Ansible Playbook execution.
<img width="478" height="125" alt="Screenshot_212" src="https://github.com/user-attachments/assets/b7249ff2-7ef4-4602-a9af-c55a6cafd155" />

2. Custom Nginx landing page.
<img width="459" height="467" alt="Screenshot_209" src="https://github.com/user-attachments/assets/149ba155-162c-4d11-af84-3f981355dddb" />

3. Nginx service status.
<img width="1003" height="375" alt="image" src="https://github.com/user-attachments/assets/7abc34b3-4267-468d-92cd-bb921687e087" />

4. MySQL service status.
<img width="865" height="337" alt="image" src="https://github.com/user-attachments/assets/d244ef5c-05eb-4aff-9fd6-f814ff798ba8" />

5. UFW status on Web Server.
<img width="317" height="140" alt="Screenshot_208" src="https://github.com/user-attachments/assets/f462c03d-8aa5-4ab6-a354-2621e545ce6a" />

6. UFW status on Database Server.
<img width="510" height="234" alt="image" src="https://github.com/user-attachments/assets/81b898ed-e770-460a-8dcb-726a651c10a7" />

7. Successful MySQL connectivity test from Web Server.
<img width="316" height="50" alt="Screenshot_210" src="https://github.com/user-attachments/assets/2a7073fd-cc84-4a34-a534-e3c711881c52" />

8. Blocked MySQL connectivity test from Host Machine.
<img width="465" height="183" alt="Screenshot_211" src="https://github.com/user-attachments/assets/dffd9a9e-1c7a-450a-af0c-9510658612bb" />


---

## 🎯 Key Achievements

* Provisioned and configured multiple Ubuntu servers from a single Ansible Control Node.
* Automated installation and configuration of Nginx and MySQL.
* Eliminated repetitive manual server setup processes.
* Implemented firewall-based database isolation.
* Applied least-privilege access control for backend services.
* Successfully deployed a secure multi-tier infrastructure in a local virtualized environment.

---

## 💼 Skills Demonstrated

* Infrastructure as Code (IaC)
* Configuration Management
* Linux System Administration
* Ansible Automation
* Service Deployment & Management
* SSH Administration
* Firewall Management (UFW)
* Network Segmentation
* Security Hardening
* Multi-tier Architecture Design
* DevOps Fundamentals

---

## 📚 Lessons Learned

Through this project, I learned how to:

* Manage multiple servers from a single control node.
* Automate repetitive Linux administration tasks.
* Deploy and manage web and database services using Ansible.
* Apply Infrastructure as Code principles to improve consistency and repeatability.
* Implement network-level security controls using firewall rules.
* Design a simple but secure multi-tier architecture.

---

## 🚀 Future Improvements

Potential future enhancements include:

### Application Layer

* Deploy a real web application instead of a static HTML page.
* Configure Nginx as a reverse proxy.
* Connect the application directly to MySQL.

### Security

* Enforce SSH key authentication.
* Disable password-based SSH login.
* Implement Fail2Ban for brute-force protection.
* Enable HTTPS with Let's Encrypt certificates.

### Scalability

* Add multiple web servers.
* Implement load balancing using Nginx.
* Configure MySQL replication.

### DevOps Practices

* Integrate GitHub Actions CI/CD pipelines.
* Automate deployment workflows.
* Implement infrastructure validation testing.

### Monitoring & Observability

* Deploy Prometheus for metrics collection.
* Visualize infrastructure health using Grafana.
* Implement centralized logging with ELK Stack or Loki.
* Configure automated alerting.

```
```
