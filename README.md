# Assignment 11 - Deployment of E-Commerce Web Application

## Problem Statement

Deploy a sample e-commerce application on a cloud virtual machine. Configure the backend services and database so that users can browse products and simulate basic purchase functionality.

---

# Repository

```text
https://github.com/4SNA/ecommerce-app-LP2
```

---

# Technologies Used

- AWS EC2 Ubuntu Instance
- React + Vite Frontend
- Node.js + Express.js Backend
- MongoDB Atlas Database
- Git
- npm
- PM2 Process Manager

---

# Features

- Browse products
- View product details
- Simulate purchase functionality
- Backend API integration
- MongoDB Atlas database
- Cloud deployment on AWS EC2

---

# Architecture

```text
User Browser
     |
     v
AWS EC2 Instance
     |
     |-- React + Vite Frontend
     |-- Express Backend API (Port 5000)
     |
     v
MongoDB Atlas Database
```

---

# Step 1 - Create AWS EC2 Instance

1. Open AWS Console.
2. Go to EC2.
3. Click Launch Instance.
4. Select Ubuntu 22.04 LTS or Ubuntu 24.04 LTS.
5. Select t2.micro.
6. Create/select key pair.
7. Download `.pem` file.
8. Configure Security Group.

---

# Security Group Inbound Rules

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |
| HTTP | 80 | 0.0.0.0/0 |
| Custom TCP | 5000 | 0.0.0.0/0 |
| Custom TCP | 5173 | 0.0.0.0/0 |

---

# Step 2 - Connect to EC2

Open terminal or PowerShell where `.pem` file exists.

```bash
chmod 400 your-key.pem
```

```bash
ssh -i your-key.pem ubuntu@YOUR_PUBLIC_IP
```

Example:

```bash
ssh -i ecommerce.pem ubuntu@15.206.xxx.xxx
```

---

# Step 3 - Update Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

---

# Step 4 - Install Node.js v22, npm, Git and PM2

Vite requires a newer Node.js version. Install Node.js v22.

```bash
sudo apt install curl -y
```

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
```

```bash
sudo apt install -y nodejs
```

Install Git:

```bash
sudo apt install -y git
```

Install PM2:

```bash
sudo npm install -g pm2
```

Verify installations:

```bash
node -v
npm -v
git --version
pm2 --version
```

Node version should be:

```text
v22.x.x
```

---

# Step 5 - MongoDB Atlas Setup

Use MongoDB Atlas cloud database.

---

# MongoDB Atlas Details

```text
Username: lp2_user
Password: lp2password123
Database Name: ecommerce
Cluster Name: Cluster0
```

---

# Atlas Setup Steps

1. Open MongoDB Atlas.
2. Create free M0 cluster.
3. Create database user:
   ```text
   lp2_user
   ```
4. Set password:
   ```text
   lp2password123
   ```
5. Go to:
   ```text
   Network Access
   ```
6. Click:
   ```text
   Add IP Address
   ```
7. Click:
   ```text
   Allow Access From Anywhere
   ```
8. Confirm:
   ```text
   0.0.0.0/0
   ```

MongoDB Atlas automatically creates database `ecommerce` after first insertion.

---

# Step 6 - Clone Repository

```bash
git clone https://github.com/4SNA/ecommerce-app-LP2.git
```

```bash
cd ecommerce-app-LP2
```

Check files:

```bash
ls
```

Expected:

```text
backend
frontend
README.md
```

---

# Step 7 - Build Frontend

Go to frontend:

```bash
cd frontend
```

Clean npm cache:

```bash
npm cache clean --force
```

Install frontend dependencies:

```bash
npm install
```

---

# Step 7.1 - Fix localhost API Issue

Search localhost references:

```bash
grep -R "localhost" .
```

If you find:

```text
http://localhost:5000
```

replace it with your EC2 public IP.

Example:

```text
http://15.206.xxx.xxx:5000
```

Quick replace:

```bash
grep -rl "localhost:5000" . | xargs sed -i 's|http://localhost:5000|http://15.206.xxx.xxx:5000|g'
```

---

# Step 7.2 - Configure Vite Public Hosting

Open Vite config:

```bash
nano vite.config.js
```

OR:

```bash
nano vite.config.ts
```

Ensure server config exists:

```js
server: {
  host: "0.0.0.0",
  port: 5173
}
```

Example:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    host: "0.0.0.0",
    port: 5173
  }
})
```

---

# Step 7.3 - Build Frontend

```bash
npm run build
```

This creates a production `dist` folder.

---

# Step 8 - Setup Backend

Go to backend:

```bash
cd ../backend
```

Install backend dependencies:

```bash
npm install
```

Create `.env`:

```bash
nano .env
```

Paste:

```env
NODE_ENV=production
PORT=5000
MONGO_URI=mongodb+srv://lp2_user:lp2password123@cluster0.tki1kaj.mongodb.net/ecommerce?retryWrites=true&w=majority&appName=Cluster0
JWT_SECRET=sarthak123
```

Save:

```text
CTRL + X → Y → Enter
```

Check `.env`:

```bash
cat .env
```

---

# Step 9 - Run Backend Using PM2

Check backend files:

```bash
ls
```

If `server.js` exists:

```bash
pm2 start server.js --name ecommerce-app
```

Save PM2 process:

```bash
pm2 save
```

Check running processes:

```bash
pm2 list
```

---

# Step 10 - Test Backend Locally

```bash
curl http://localhost:5000
```

If HTML or JSON output appears, backend is working.

---

# Step 11 - Run Frontend Publicly

Open another SSH terminal or stop backend logs if needed.

Go to frontend:

```bash
cd ~/ecommerce-app-LP2/frontend
```

Run frontend:

```bash
npm run dev -- --host 0.0.0.0
```

Expected output:

```text
Local:   http://localhost:5173
Network: http://172.xxx.xxx.xxx:5173
```

---

# Step 12 - Access Application Publicly

## Backend

```text
http://YOUR_PUBLIC_IP:5000
```

---

## Frontend

```text
http://YOUR_PUBLIC_IP:5173
```

Example:

```text
http://15.206.xxx.xxx:5173
```

---

# Step 13 - Verify MongoDB Atlas

1. Open MongoDB Atlas.
2. Go to Database.
3. Click Browse Collections.
4. Use the application and perform product/purchase operation.
5. Database `ecommerce` should appear.
6. Collections/documents should be visible.

---

# Useful Commands

## Check PM2

```bash
pm2 list
```

---

## View PM2 Logs

```bash
pm2 logs
```

---

## Restart Backend

```bash
pm2 restart all
```

---

## Stop Backend

```bash
pm2 stop all
```

---

## Delete PM2 Processes

```bash
pm2 delete all
```

---

## Test Backend

```bash
curl http://localhost:5000
```

---

# Common Errors and Fixes

---

## 1. pm2 command not found

```bash
sudo npm install -g pm2
```

---

## 2. Node version error while building frontend

Install Node.js v22:

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

Check:

```bash
node -v
```

---

## 3. Website not opening publicly

Check Security Group.

Required:

| Type | Port |
|---|---|
| Custom TCP | 5000 |
| Custom TCP | 5173 |

Source:

```text
0.0.0.0/0
```

---

## 4. MongoDB Atlas connection failed

Check:

```text
Network Access → 0.0.0.0/0
```

Check `.env`:

```env
MONGO_URI=mongodb+srv://lp2_user:lp2password123@cluster0.tki1kaj.mongodb.net/ecommerce?retryWrites=true&w=majority&appName=Cluster0
```

Restart:

```bash
pm2 restart all
pm2 logs
```

---

## 5. Frontend works only on localhost

Fix Vite config:

```js
server: {
  host: "0.0.0.0",
  port: 5173
}
```

Run:

```bash
npm run dev -- --host 0.0.0.0
```

---

## 6. localhost API issue

Replace:

```text
http://localhost:5000
```

with:

```text
http://YOUR_PUBLIC_IP:5000
```

---

## 7. EADDRINUSE port 5000 already in use

Fix:

```bash
pm2 delete all
pm2 start server.js --name ecommerce-app
```

---

## 8. Cannot find module

Run:

```bash
npm install
```

inside frontend/backend.

---

## 9. server.js not found

Check:

```bash
ls
```

If app.js exists:

```bash
pm2 start app.js --name ecommerce-app
```

If index.js exists:

```bash
pm2 start index.js --name ecommerce-app
```

---

# Viva Questions

## What is EC2?

AWS virtual machine service used to host applications on cloud.

---

## What is MongoDB Atlas?

Cloud-based MongoDB database service.

---

## What is PM2?

Node.js process manager used to keep backend running continuously.

---

## Why Node.js v22?

Vite and modern frontend tools require newer Node.js versions.

---

## Why ports 5000 and 5173?

- 5000 → Backend API
- 5173 → React + Vite frontend

---

## What is Security Group?

AWS firewall that controls inbound and outbound traffic.

---

## What is `.env` file?

File used to store environment variables like database URL and port.

---

# Output

The E-Commerce Web Application is successfully deployed and publicly accessible using:

```text
http://YOUR_PUBLIC_IP:5173
```

---

# Conclusion

The E-Commerce Web Application was successfully deployed on AWS EC2 Ubuntu using React + Vite frontend, Node.js/Express backend, and MongoDB Atlas database. Users can browse products and simulate purchase functionality through the deployed cloud application.
