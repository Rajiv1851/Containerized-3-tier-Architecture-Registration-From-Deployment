# Three-Tier Architecture Application using Docker & Docker Compose

This project demonstrates a **containerized three-tier web application** using **Docker** and **Docker Compose**. It follows a classic **Web → Application → Database** architecture and is ideal for learning containerization, service networking, and basic DevOps practices.

---

## 🧱 Architecture Overview

**Three Tiers:**

1. **Web Tier (Nginx)**

   * Serves static HTML pages
   * Forwards PHP requests to the App tier using FastCGI

2. **Application Tier (PHP-FPM)**

   * Processes form submissions
   * Connects to the MySQL database
   * Inserts and fetches user data

3. **Database Tier (MySQL)**

   * Stores user registration data
   * Initialized automatically using `init.sql`

All services communicate over **Docker networks** defined in Docker Compose.

---

## 📁 Project Structure

```
three-tier/
├── docker-compose.yml
├── web/
│   ├── code/
│   │   └── signup.html
│   └── config/
│       └── default.conf
├── app/
│   └── code/
│       └── submit.php
└── db/
    ├── Dockerfile
    └── init.sql
```

---

## ⚙️ Technologies Used

* **Docker**
* **Docker Compose**
* **Nginx** (Web Server)
* **PHP-FPM** (Application Server)
* **MySQL** (Database)

---

## 🐳 Docker Compose Configuration

The `docker-compose.yml` file defines all three services and their networking:

* **web** → Nginx container (port `80` exposed)
* **app** → PHP-FPM container (internal port `9000`)
* **db** → MySQL container (port `3306` internal)

Key features:

* Custom Docker network (`webnet`, `dbnet`)
* Persistent MySQL volume
* Service dependency ordering using `depends_on`

---

## 📄 Database Initialization (`init.sql`)

The database is initialized automatically when the MySQL container starts.

**Database Name:** `FCT`

**Table:** `users`

```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(20),
  email VARCHAR(100),
  website VARCHAR(255),
  gender VARCHAR(6),
  comment VARCHAR(100)
);
```

---

## 🧪 MySQL Dockerfile (DB Tier)

The database container uses a custom Dockerfile:

```dockerfile
FROM mysql

ENV MYSQL_ROOT_PASSWORD=root
ENV MYSQL_DATABASE=FCT

COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
```

This ensures:

* Root password is set
* Database is auto-created
* Tables are initialized on first run

---

## 🌐 Web Tier (Nginx)

* Serves `signup.html`
* Forwards `.php` requests to PHP-FPM using FastCGI

Important configuration in `default.conf`:

```nginx
location ~ \.php$ {
    root /app;
    fastcgi_pass app:9000;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME /app$fastcgi_script_name;
    include fastcgi_params;
}
```

---

## 🧠 Application Tier (PHP)

The PHP script (`submit.php`):

* Reads form data using `$_POST`
* Connects to MySQL using `mysqli`
* Inserts data into the `users` table
* Displays submitted information

Database connection details:

```php
$servername = "db";
$username = "root";
$password = "root";
$dbname = "FCT";
```

---

## 🚀 How to Run the Project

### 1️⃣ Clone the Repository

```bash
git clone https://github.com/Rajiv1851/Containerized-3-tier-Architecture-Registration-From-Deployment.git
cd three-tier
```

### 2️⃣ Start Containers

```bash
docker-compose up -d
```

### 3️⃣ Verify Containers

```bash
docker ps
```

### 4️⃣ Access the Application

Open your browser and visit:

```
http://<EC2-PUBLIC-IP>
```

---

## 🗄️ Verify Database Data

```bash
docker exec -it three-tier-db-1 mysql -u root -p
USE FCT;
SHOW TABLES;
SELECT * FROM users;
```




---

## 👤 Author

**Rajiv Nakhawa**
DevOps | Docker | AWS | Cloud Enthusiast

---

⭐ If you found this helpful, don’t forget to star the repository!
