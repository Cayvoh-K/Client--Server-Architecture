# 🗄️ MySQL Client–Server Architecture Project

This project demonstrates a **basic Client–Server architecture** using **MySQL Database Management System (DBMS)**.  
It shows how two virtual servers can communicate securely — one acting as a **MySQL Server**, and the other as a **MySQL Client**.

---

## 📘 Project Overview

In this setup:

- **Server A (mysql-server)** hosts the MySQL database.
- **Server B (mysql-client)** connects remotely to the MySQL database on Server A.
- Both instances are Linux-based EC2 servers within the **same VPC and subnet**, allowing private communication.

This demonstrates how applications can interact with a database securely and efficiently in a client-server model.

---

## ⚙️ Architecture Diagram

```
+-------------------+                 +-------------------+
|   MySQL Client    |  <----------->  |   MySQL Server    |
| (Server B: EC2)   |   Port 3306     | (Server A: EC2)   |
|-------------------|                 |-------------------|
| mysql-client CLI  |                 | mysqld service    |
| App/Script Layer  |                 | Database Storage  |
+-------------------+                 +-------------------+
```

---

## 🧩 Steps Implemented

### 1️⃣ Create and Configure Virtual Servers
- Two EC2 instances were created:
  - **Server A** — `mysql-server`
  - **Server B** — `mysql-client`
- Both were configured in the same **VPC and subnet** for private networking.
<img width="1277" height="393" alt="Screen Shot 2025-10-18 at 20 58 18" src="https://github.com/user-attachments/assets/eb8cb460-c693-4621-9461-101d085cef24" />

### 2️⃣ Install MySQL Server
On **mysql-server**:
```bash
sudo dnf install -y mariadb105-server
sudo systemctl enable --now mariadb
sudo systemctl status mariadb
```
<img width="1081" height="493" alt="Screen Shot 2025-10-24 at 10 55 37" src="https://github.com/user-attachments/assets/fb7b3a31-5497-4a7a-87b3-1995e8d4f943" />

### 3️⃣ Install MySQL Client
On **mysql-client**:
```bash
sudo dnf install -y mariadb105
```
<img width="1265" height="652" alt="Screen Shot 2025-10-18 at 21 19 20" src="https://github.com/user-attachments/assets/03812c97-8d8f-452d-9504-55279e082865" />

### 4️⃣ Configure Networking & Security
- Open **port 3306** on `mysql-server` security group.
- Restrict inbound access to only the **private IP** of the `mysql-client`.
- Obtain private IP using:
  ```bash
  hostname -I
  ```
<img width="1273" height="599" alt="Screen Shot 2025-10-24 at 10 47 51" src="https://github.com/user-attachments/assets/bb4b14fe-d22a-408f-97a5-546f277f4648" />

### 5️⃣ Configure MySQL for Remote Access
Edit `/etc/my.cnf` on the **server**:
```bash
sudo vi /etc/my.cnf
```
Under `[mysqld]`, add:
```
bind-address = 0.0.0.0
```
Then restart MySQL:
```bash
sudo systemctl restart mariadb
```

Grant access to a remote user:
```sql
CREATE USER 'remote_user'@'172.31.x.x' IDENTIFIED BY 'StrongPassword';
GRANT ALL PRIVILEGES ON *.* TO 'remote_user'@'172.31.x.x';
FLUSH PRIVILEGES;
```

### 6️⃣ Connect from the Client
From the **mysql-client**:
```bash
mysql -h 172.31.x.x -u remote_user -p
```
If successful, you’ll be connected to the MySQL server from the client instance.
<img width="1172" height="308" alt="Screen Shot 2025-10-24 at 11 56 00" src="https://github.com/user-attachments/assets/9a1540bf-91bf-4630-a1eb-0fa8ac4e1d65" />
