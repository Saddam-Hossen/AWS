# AWS
Sure! Here's a concise and complete **documentation guide** for **connecting to an AWS EC2 Ubuntu instance from a local Ubuntu system** using a `.pem` key file (e.g., `snvn.pem`).

---

# 📘 Documentation: Connecting AWS EC2 (Ubuntu) from Local Ubuntu

## 📌 Prerequisites

* You have launched an **EC2 instance** running Ubuntu.
* You have the **`.pem` key file** (e.g., `snvn.pem`) downloaded from AWS.
* You have access to the **public IP address** of your EC2 instance.
* Your **Security Group** allows inbound SSH (port 22) from your IP.

---

## ✅ Step-by-Step Instructions

### 1. 📥 Move the `.pem` key file to a secure location

```bash
mkdir -p ~/.ssh
cp /path/to/snvn.pem ~/.ssh/
```

For example, if the file is in your Windows path `/mnt/c/Users/01957/.ssh/snvn.pem`:

```bash
cp /mnt/c/Users/01957/.ssh/snvn.pem ~/.ssh/
```

---

### 2. 🔐 Set correct permissions on the `.pem` file

```bash
chmod 400 ~/.ssh/snvn.pem
```

This is required so SSH won’t reject the key for being too open.

---

### 3. 🌐 Get your EC2 instance’s Public IP

From the AWS EC2 Dashboard → Instances → copy **Public IPv4 address** (e.g., `16.171.0.77`).

---

### 4. 🔑 Connect to EC2 instance via SSH

```bash
ssh -i ~/.ssh/snvn.pem ubuntu@<EC2-PUBLIC-IP>
```

Example:

```bash
ssh -i ~/.ssh/snvn.pem ubuntu@16.171.0.77
```

> `ubuntu` is the default username for Ubuntu-based AMIs.

---

### 5. 🛠 Troubleshooting

| Issue                           | Fix                                              |
| ------------------------------- | ------------------------------------------------ |
| `UNPROTECTED PRIVATE KEY FILE`  | Run `chmod 400 ~/.ssh/snvn.pem`                  |
| `Permission denied (publickey)` | Verify correct key and username (`ubuntu`)       |
| `Connection timed out`          | Ensure port 22 is open in **EC2 Security Group** |
| `Host key verification failed`  | Try: `ssh-keygen -R <EC2-PUBLIC-IP>`             |

---

## 🧰 Optional: Add to SSH Config (to avoid typing every time)

Edit the file:

```bash
nano ~/.ssh/config
```

Add:

```ssh
Host aws
  HostName 16.171.0.77
  User ubuntu
  IdentityFile ~/.ssh/snvn.pem
  IdentitiesOnly yes

```

Then you can connect with:

```bash
ssh aws
```

---

## ✅ Connected!

After login, you can:

* Install packages: `sudo apt update && sudo apt install -y ...`
* Clone repos
* Deploy applications
* Set up a web server

---

