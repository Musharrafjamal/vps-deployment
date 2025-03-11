# 🚀 Deploying a Next.js App on a VPS

This guide will walk you through setting up and deploying a Next.js application on a VPS using **Node.js, PM2, and Nginx**.

---

## 📌 Prerequisites

- A VPS with Ubuntu installed
- A domain name (optional but recommended)
- Basic knowledge of Linux commands

---

## 🛠 1. Connect to Your VPS via SSH

```bash
ssh root@your_vps_ip  # Replace with your VPS IP
```

---

## 🔄 2. Update & Install Required Packages

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl ufw nginx
```

---

## 💻 3. Install Node.js using NVM

NVM (Node Version Manager) provides flexibility to install and manage different Node.js versions.

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc
nvm install --lts  # Install latest LTS version
```

---

## 🔄 4. Install PM2 for Process Management

```bash
npm install -g pm2
pm2 startup  # Enable auto-restart on reboot
```

---

## 🚀 5. Deploy Your Next.js Application

### 📥 Clone Your Repository

```bash
cd /var/www
git clone git@github.com:your_username/your_repo.git your_app_name
cd your_app_name
npm install
```

### 🔨 Build the Application

```bash
npm run build  # Generates the .next folder
```

### ▶ Start the Application with PM2

```bash
pm2 start npm --name "your_app" -- start
pm2 save  # Persist the process list
```

---

## 🌐 6. Configure Nginx as a Reverse Proxy

### 📦 Install Nginx

```bash
sudo apt install -y nginx
```

### ✍ Create an Nginx Configuration File

```bash
sudo nano /etc/nginx/sites-available/your_domain.conf
```

#### 🔧 Add the following configuration (replace `your_domain.com` with your actual domain):

```nginx
server {
    listen 80;
    server_name your_domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### 🔗 Enable the Configuration & Restart Nginx

```bash
sudo ln -s /etc/nginx/sites-available/your_domain.conf /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default  # Remove default config
sudo nginx -t  # Validate syntax
sudo systemctl restart nginx
```

---

## 🔒 7. Domain and SSL Setup

### 🌍 Point Domain to VPS IP

Go to your domain registrar’s DNS settings and create an **A Record** pointing `@` and `www` to your VPS IP.

### 🔐 Install SSL Certificate with Certbot

```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d your_domain.com -d www.your_domain.com  # Follow prompts
```

Certbot will automatically update your Nginx config to use **HTTPS** and set up **auto-renewal**.

---

## 🎉 Done!

Your Next.js app is now deployed, secured with SSL, and managed with PM2 for reliability. 🚀

### ✅ Useful PM2 Commands:

- **Check running processes**: `pm2 list`
- **Stop the app**: `pm2 stop your_app`
- **Restart the app**: `pm2 restart your_app`
- **Delete the app from PM2**: `pm2 delete your_app`

---

### 📖 References:
- [Next.js Documentation](https://nextjs.org/docs/deployment)
- [PM2 Process Manager](https://pm2.keymetrics.io/)
- [Certbot SSL Guide](https://certbot.eff.org/)

---

🎯 _Happy Coding!_ 🎯
