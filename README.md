# ShopVerse — MERN E-Commerce Application

A simple e-commerce application built with the **MERN stack** (MongoDB, Express, React, Node.js) designed for deployment on **AWS EC2**.

## Features

- 🛍️ Browse 12+ products across 4 categories
- 🔍 Search and filter products by category
- 🛒 Shopping cart with quantity management (persisted in localStorage)
- 📦 Simulated checkout with order creation
- ✅ Order confirmation page
- 📱 Fully responsive design
- 🌙 Modern dark theme with glassmorphism UI

## Tech Stack

| Layer      | Technology          |
|------------|---------------------|
| Frontend   | React 18 + Vite     |
| Styling    | Vanilla CSS         |
| Backend    | Node.js + Express   |
| Database   | MongoDB + Mongoose  |
| HTTP       | Axios               |

## Quick Start (Local Development)

### Prerequisites

- **Node.js** v18+
- **MongoDB** running locally (`mongodb://localhost:27017`)

### 1. Clone & Setup

```bash
cd ecommerce-app
```

### 2. Backend Setup

```bash
cd backend
npm install
```

Create a `.env` file (already included):

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/ecommerce
NODE_ENV=development
```

### 3. Seed the Database

```bash
npm run seed
```

### 4. Start Backend

```bash
npm run dev
```

The API will be running at `http://localhost:5000`.

### 5. Frontend Setup (new terminal)

```bash
cd frontend
npm install
npm run dev
```

The app will be running at `http://localhost:5173`.

## API Endpoints

| Method | Endpoint             | Description                 |
|--------|----------------------|-----------------------------|
| GET    | /api/products        | Get all products (with optional `category` and `search` query params) |
| GET    | /api/products/:id    | Get single product          |
| POST   | /api/orders          | Create a new order          |
| GET    | /api/orders          | Get all orders              |
| GET    | /api/orders/:id      | Get order by ID             |
| GET    | /api/health          | Health check                |

## AWS EC2 Deployment Guide

### 1. Launch an EC2 Instance

- **AMI**: Amazon Linux 2023 or Ubuntu 22.04
- **Instance type**: t2.micro (free tier) or t2.small
- **Security Group**: Allow inbound on ports 22 (SSH), 80 (HTTP), 5000 (API)

### 2. SSH into EC2

```bash
ssh -i your-key.pem ec2-user@<your-ec2-public-ip>
```

### 3. Install Dependencies

```bash
# Node.js
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs   # Amazon Linux
# OR
sudo apt install -y nodejs npm   # Ubuntu

# MongoDB
# Follow MongoDB's official docs for your OS
# Or use MongoDB Atlas (cloud) and update MONGO_URI
```

### 4. Clone and Setup

```bash
git clone <your-repo-url>
cd ecommerce-app

# Backend
cd backend
npm install
npm run seed

# Frontend
cd ../frontend
npm install
npm run build
```

### 5. Serve Frontend from Backend

Copy the frontend build output to be served by Express:

```bash
cp -r frontend/dist backend/public
```

Add static file serving to `backend/server.js` (already compatible).

### 6. Run with PM2

```bash
sudo npm install -g pm2
cd backend
pm2 start server.js --name ecommerce
pm2 save
pm2 startup
```

Your app will be accessible at `http://<your-ec2-public-ip>:5000`.

## Project Structure

```
ecommerce-app/
├── backend/
│   ├── config/db.js
│   ├── models/Product.js, Order.js
│   ├── controllers/productController.js, orderController.js
│   ├── routes/productRoutes.js, orderRoutes.js
│   ├── seeder.js
│   ├── server.js
│   └── .env
├── frontend/
│   ├── src/
│   │   ├── api/index.js
│   │   ├── components/Navbar, ProductCard, Footer
│   │   ├── context/CartContext.jsx
│   │   ├── pages/HomePage, ProductPage, CartPage, OrderSuccessPage
│   │   ├── App.jsx
│   │   └── index.css (design system)
│   └── vite.config.js
└── README.md
```

## License

MIT
