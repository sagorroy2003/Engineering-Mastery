# 🐧 08. Linux Fundamentals for Engineers

![Difficulty: Intermediate](https://img.shields.io/badge/Difficulty-Intermediate-yellow?style=for-the-badge)
![Focus: Infrastructure](https://img.shields.io/badge/Focus-Infrastructure-blue?style=for-the-badge)
![Status: Essential](https://img.shields.io/badge/Status-Essential-success?style=for-the-badge)

"It works on my Windows/Mac machine" is an excuse that will not survive in a professional DevOps environment. The vast majority of the internet, including the servers that will host your Node.js APIs and PostgreSQL databases, runs on Linux. 

Understanding Linux is not about memorizing CLI commands; it is about understanding how the operating system manages resources, users, and security.

---

## 🗂️ The Linux File System Hierarchy

Unlike Windows, which uses drive letters (`C:\`), Linux organizes everything under a single root directory (`/`). Everything in Linux is considered a file—even hardware devices and active processes.

* `/bin` & `/sbin`: Essential user and system binaries (compiled executable programs like `ls`, `cp`, `node`).
* `/etc`: System-wide configuration files. If you need to configure Nginx or a firewall, you will be editing files here.
* `/var`: Variable data. This is where your system writes log files (`/var/log`) and where databases often store their raw data (`/var/lib/postgresql`).
* `/home`: Personal directories for users (e.g., `/home/sagor`).
* `/tmp`: Temporary files that are cleared upon reboot. Never store persistent marketplace data here.



---

## 🔐 Permissions and Ownership (The 777 Trap)

The most common mistake junior developers make when a file "won't run" or a server throws a "Permission Denied" error is running `chmod 777 <file>`. **This is a massive security vulnerability.** It gives every user on the system the right to read, write, and execute that file.

### How Permissions Actually Work
Permissions are divided into three groups: **Owner (u)**, **Group (g)**, and **Others (o)**.
Each group has three rights: **Read (r=4)**, **Write (w=2)**, and **Execute (x=1)**.

When you see a permission like `755`, it is simple math:
* **7 (Owner):** Read (4) + Write (2) + Execute (1). The owner has full control.
* **5 (Group):** Read (4) + Execute (1). The group can read and run it, but cannot modify it.
* **5 (Others):** Read (4) + Execute (1). Anyone else can read and run it.

**Professional Best Practices:**
* Directory permissions should generally be `755`.
* Standard file permissions should be `644` (Read/Write for owner, Read-only for everyone else).
* Private SSH Keys (`.pem` or `.pub`) **must** be `400` (Read-only for the owner, completely locked out for everyone else). If your key is too open, SSH will actively refuse to use it.

---

## ⚙️ Process Management & Systemd

When you deploy your Node.js backend, you cannot simply run `npm start` and close your terminal. If you close the SSH session, the process dies. If the app crashes, it stays dead.

### The Standard Tools
* `top` / `htop`: Real-time dashboards of system resources (CPU, RAM). Use this when your server feels sluggish to identify memory leaks.
* `ps aux`: Lists every running process on the system.
* `kill -9 <PID>`: Forcefully terminates a process by its Process ID. 

### Systemd (The Professional Way)
To keep an application running in the background persistently, you create a `systemd` service. This tells Linux to manage your app, restart it on failure, and start it automatically when the server boots.

```ini
# /etc/systemd/system/unibazer-api.service
[Unit]
Description=UniBazer Node.js Backend API
After=network.target

[Service]
User=sagor
WorkingDirectory=/var/www/unibazer-api
ExecStart=/usr/bin/node dist/server.js
Restart=always
Environment=NODE_ENV=production PORT=8080

[Install]
WantedBy=multi-user.target
```
*Commands to manage it:*
* `sudo systemctl start unibazer-api`
* `sudo systemctl enable unibazer-api` (Starts on boot)

---

## 📡 SSH & Asymmetric Cryptography

Secure Shell (SSH) is how you securely connect to a remote Virtual Private Server (VPS). It relies on asymmetric cryptography (a public key and a private key).

* **Public Key:** Think of this as a padlock. You put this on the remote server.
* **Private Key:** Think of this as the physical key. It stays on your local machine and never leaves.

**Security Hardening Rule:** Once you successfully SSH into a production server using keys, you must edit `/etc/ssh/sshd_config` and set `PasswordAuthentication no`. This prevents hackers from brute-forcing passwords.

---

## 🕵️‍♂️ Logs and Troubleshooting Like a Senior

When your backend throws a 500 error in production, you don't have a VS Code console. You have Linux logs.

* **View the end of a log file in real-time:**
  `tail -f /var/log/nginx/error.log`
* **Search for a specific error inside a massive file (using Regex):**
  `grep -i "UnhandledPromiseRejection" /var/www/unibazer/app.log`
* **View logs managed by Systemd:**
  `journalctl -u unibazer-api -f`

---

## 🎯 Practical Exercises
1. Spin up a free Linux virtual machine (e.g., Ubuntu on AWS EC2 or a local VirtualBox/WSL instance).
2. Generate an SSH key pair (`ssh-keygen -t ed25519`) and configure the server to accept it. Then disable password authentication completely.
3. Write a simple infinite loop in Node.js (`console.log("Running...")`), upload it to the server, and write a `systemd` service file to keep it running in the background. Crash the script intentionally and verify that Linux restarts it.

## 🎤 Interview Questions
* *"What is the difference between an absolute path and a relative path in Linux?"*
* *"You try to execute a bash script using `./deploy.sh` and receive a 'Permission Denied' error. You own the file. What exact command fixes this without opening a security hole?"*
* *"Your production server is suddenly unresponsive. Walk me through the Linux commands you would use to identify if the issue is a CPU spike, a memory leak, or a full disk."*
