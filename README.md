# frappe-lms-ubuntu24.04

A step-by-step setup of **Frappe LMS** on **Ubuntu 24.04 LTS**.

This repository provides a comprehensive guide to installing and configuring Frappe LMS, including all required dependencies, database setup, virtual environments, and app installation. By following these instructions, users will have a fully working **Learning Management System (LMS)** running on top of the Frappe framework.

## 📌 Prerequisites
```
MariaDB 10.6.6+                               (11.3 is recommended on develop)
Python 3.10+
Node 18+
Redis 6                                       (caching and realtime updates)
yarn 1.12+                                    (js dependency manager)
pip 20+                                       (py dependency manager)
wkhtmltopdf (version 0.12.5 with patched qt)  (for pdf generation)
cron                                          (bench's scheduled jobs: automated certificate renewal, scheduled backups)
```
## 🚀 Installation Steps

Follow the steps below to set up **Frappe LMS** on Ubuntu 24.04.

---

### 1️⃣ System Update
```bash
sudo apt update && sudo apt upgrade -y
```
- **apt update** → refreshes package list
- **apt upgrade** → upgrades installed packages to latest versions

### 2️⃣ Install Git
```bash
sudo apt install git -y
```
- **git** → distributed version control system to track code changes and collaborate on projects

### 3️⃣ Install Python & Dependencies
```bash
sudo apt install python3-dev -y
sudo apt install python3-setuptools python3-pip -y
sudo apt install python3.12-venv -y
sudo apt install software-properties-common -y
```
- **python3-dev** → header files & tools for building Python modules in C/C++
- **python3-setuptools** → helps build, package & distribute Python projects
- **python3-pip** → Python package manager
- **python3.12-venv** → create isolated virtual environments
- **software-properties-common** → manage and add external repositories

### 4️⃣ Install MariaDB
```bash
sudo apt install mariadb-server -y
sudo systemctl status mariadb
sudo mysql_secure_installation

  In order to log into MariaDB to secure it, we'll need the current
  password for the root user. If you've just installed MariaDB, and
  haven't set the root password yet, you should just press enter here.

  Enter current password for root (enter for none): # PRESS ENTER
  OK, successfully used password, moving on...
  
  
  Switch to unix_socket authentication [Y/n] Y
  Enabled successfully!
  Reloading privilege tables..
   ... Success!

  Change the root password? [Y/n] Y
  New password: 
  Re-enter new password: 
  Password updated successfully!
  Reloading privilege tables..
   ... Success!

  Remove anonymous users? [Y/n] Y
   ... Success!

   Disallow root login remotely? [Y/n] Y
   ... Success!

   Remove test database and access to it? [Y/n] Y
   - Dropping test database...
   ... Success!
   - Removing privileges on test database...
   ... Success!

   Reload privilege tables now? [Y/n] Y
   ... Success!
```
- **mariadb-server** → open-source database to store app data
- **systemctl status mariadb** → check MariaDB service status
- **mysql_secure_installation** → secure MariaDB installation

***Configure MariaDB***
```bash
sudo apt install libmysqlclient-dev -y
sudo vim /etc/mysql/my.cnf
```

***Add the following lines:***
```ini
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```

***Restart MariaDB:***
```bash
sudo systemctl restart mariadb
```

### 5️⃣ Install Redis
```bash
sudo apt install redis-server -y
```
- **redis-server** → in-memory key-value store for caching & message brokering

### 6️⃣ Install cURL & Node.js
```bash
sudo apt install curl -y
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source ~/.profile
nvm install 18
node -v
```
- **cURL** → transfers data from/to servers
- **NVM** → Node Version Manager for Node.js
- **nvm install 18** → installs Node.js v18
- **node -v** → verify Node.js version

### 7️⃣ Install NPM & Yarn
```bash
sudo apt install npm -y
sudo npm install -g yarn
```
- **npm** → Node package manager
- **yarn** → alternative JS package manager

### 8️⃣ Install PDF Tools
```bash
sudo apt install xvfb libfontconfig wkhtmltopdf -y
```
- **wkhtmltopdf** → convert HTML to PDF (used for reports/invoices in Frappe)

### 9️⃣ Setup Frappe Bench Environment
```bash
mkdir -p ~/frappe-uat
cd ~/frappe-uat
python3 -m venv venv
source venv/bin/activate
pip install frappe-bench
```
- Creates project directory
- Creates & activates Python virtual environment
- Installs Frappe Bench CLI tool

### 🔟 Initialize Bench
```bash
bench init frappe-bench --frappe-branch version-15
cd frappe-bench
bench start
```
- Initializes new Frappe Bench with version 15
- Starts the development server

### 1️⃣1️⃣ Create a New App
```bash
bench new-app library_management
```
- Creates a custom app named library_management
You should get some prompts, and output like the following. You can enter information manually, or press enter to select the defaults
```
App Title (default: Library Management):
App Description: Library Management System
App Publisher: Faris Ansari
App Email: faris@example.com
App Icon (default 'octicon octicon-file-directory'):
App Color (default 'grey'):
App License (default 'MIT'):
'library_management' created at /home/frappe/frappe-bench/apps/library_management

Installing library_management
$ ./env/bin/pip install -q -U -e ./apps/library_management
$ bench build --app library_management
yarn run v1.22.4
$ FRAPPE_ENV=production node rollup/build.js --app library_management
Production mode
✔ Built js/moment-bundle.min.js
✔ Built js/libs.min.js
✨  Done in 1.95s.
```

### 1️⃣2️⃣ Create a New Site
```bash
bench new-site library.localhost
```
- Creates a new site named library.localhost
- Frappe will identify which site to serve by matching the hostname of the request with the site name, so you should be able to access your site on http://library.localhost:8000

### 1️⃣3️⃣ Install Custom App
```bash
bench --site library.localhost install-app library_management
bench --site library.localhost list-apps
```
- Installs library_management app on the site
- Lists installed apps on the site

### 1️⃣4️⃣ Get & Install LMS App
```bash
bench get-app lms
bench --site library.localhost install-app lms
```
- Downloads LMS app into bench
- Installs it on library.localhost

### ✅ What You Will Achieve
- By following this guide, you will have:
- A fully configured Frappe Bench environment
- MariaDB and Redis services running
- A custom app (library_management) installed
- The Frappe LMS app successfully deployed
- Ability to extend, customize, and use LMS features on Ubuntu 24.04
