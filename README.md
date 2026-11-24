# BMI & Health Tracker --> On Push Deploy

A production-ready, full-stack three-tier web application for tracking Body Mass Index (BMI), Basal Metabolic Rate (BMR), and daily calorie requirements with 30-day trend visualization. Features fully automated CI/CD deployment using GitHub Actions.

**🔗 Repository**: https://github.com/md-sarowar-alam/single-server-3tier-webapp-github-actions.git  
**🌐 Live Demo**: http://44.245.64.25 (if deployed)

## 📋 Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [How It Works](#how-it-works)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Quick Start](#quick-start)
- [Deployment](#deployment)
- [CI/CD with GitHub Actions](#cicd-with-github-actions)
- [API Documentation](#api-documentation)
- [Database Schema](#database-schema)
- [Security](#security)
- [Monitoring](#monitoring)
- [Troubleshooting](#troubleshooting)
- [Documentation](#documentation)
- [Contributing](#contributing)

## 🎯 Overview

BMI & Health Tracker is a comprehensive health monitoring application that helps users track their health metrics over time. The application calculates BMI, BMR, and daily calorie needs based on user inputs and visualizes trends to help users monitor their health journey.

### What Problem Does It Solve?

- **Health Tracking**: Monitor BMI, BMR, and calorie needs over time
- **Historical Data**: Enter measurements from past dates to build complete health history  
- **Trend Analysis**: Visualize 30-day BMI trends to track progress
- **Easy Deployment**: Fully automated deployment with GitHub Actions
- **Production Ready**: Enterprise-grade deployment with PM2, Nginx, and PostgreSQL

### What Makes It Special?

✅ **Smart CI/CD**: GitHub Actions automatically deploys to fresh or existing EC2 instances  
✅ **Zero Configuration**: Automated installation of Node.js, PostgreSQL, Nginx, PM2  
✅ **Database Migrations**: Automatic detection and execution of schema changes  
✅ **Health Checks**: Post-deployment verification with automatic rollback on failure  
✅ **Backup System**: Automatic backups before each deployment  
✅ **Custom Dates**: Enter historical measurements, not just current data  

## ✨ Key Features

## ✨ Key Features

### Health Calculations
- **BMI (Body Mass Index)**: Standard formula - weight(kg) / height(m)²
- **BMR (Basal Metabolic Rate)**: Mifflin-St Jeor equation for accurate calorie burn estimation
- **Daily Calorie Needs**: Harris-Benedict formula with 5 activity levels
- **BMI Categories**: Color-coded (Underweight, Normal, Overweight, Obese)

### Data Management
- **PostgreSQL Database**: Reliable storage with connection pooling
- **Custom Measurement Dates**: Enter historical data, not just current
- **30-Day Trend Visualization**: Interactive Chart.js graphs
- **Historical View**: Complete measurement history with dates
- **Statistics Dashboard**: Average BMI, latest measurements, total records

### Deployment & DevOps
- **GitHub Actions CI/CD**: Fully automated deployment pipeline
- **Smart Detection**: Auto-detects first-time vs update deployment
- **Zero Configuration**: Installs Node.js, PostgreSQL, Nginx, PM2 automatically
- **Database Migrations**: Automatic schema updates with tracking
- **Health Checks**: Post-deployment verification
- **Rollback Support**: Quick recovery to previous version
- **Backup System**: Automatic backups before updates (keeps last 5)

### Production Features
- **PM2 Process Management**: Auto-restart, logs, monitoring
- **Nginx Reverse Proxy**: Static file serving + API proxying
- **CORS Configuration**: Secure cross-origin requests
- **Environment-Based Config**: Separate dev/prod settings
- **Error Handling**: Graceful error messages, logging
- **Responsive Design**: Card-based UI, mobile-friendly

## 🔄 How It Works

### User Workflow

```
┌─────────────┐
│ User Enters │
│ Measurement │
│   Details   │
└──────┬──────┘
       │
       ↓
┌──────────────────────────────────────┐
│ Frontend Validation & Form Submit    │
│ - Height (cm), Weight (kg), Age      │
│ - Gender, Activity Level             │
│ - Measurement Date (can be past)     │
└──────┬───────────────────────────────┘
       │
       ↓ POST /api/measurements
┌──────────────────────────────────────┐
│ Backend Processing                   │
│ 1. Validate inputs                   │
│ 2. Calculate BMI = weight/height²    │
│ 3. Calculate BMR (Mifflin-St Jeor)  │
│ 4. Calculate Daily Calories          │
│ 5. Determine BMI Category            │
└──────┬───────────────────────────────┘
       │
       ↓ INSERT INTO measurements
┌──────────────────────────────────────┐
│ PostgreSQL Database                  │
│ - Store all measurements             │
│ - Index by date for fast queries     │
└──────┬───────────────────────────────┘
       │
       ↓ Return created measurement
┌──────────────────────────────────────┐
│ Frontend Updates                     │
│ 1. Display success message           │
│ 2. Refresh statistics cards          │
│ 3. Update 30-day trend chart         │
│ 4. Add to measurement history        │
└──────────────────────────────────────┘
```

### Calculation Logic

#### BMI Calculation
```javascript
BMI = weight(kg) / (height(m))²
```

**Categories:**
- Underweight: BMI < 18.5
- Normal: 18.5 ≤ BMI < 25
- Overweight: 25 ≤ BMI < 30
- Obese: BMI ≥ 30

#### BMR Calculation (Mifflin-St Jeor Equation)
```javascript
// For Men
BMR = (10 × weight_kg) + (6.25 × height_cm) - (5 × age) + 5

// For Women
BMR = (10 × weight_kg) + (6.25 × height_cm) - (5 × age) - 161
```

#### Daily Calorie Needs (Harris-Benedict Formula)
```javascript
Daily Calories = BMR × Activity Multiplier
```

**Activity Multipliers:**
- Sedentary (little/no exercise): 1.2
- Lightly active (1-3 days/week): 1.375
- Moderately active (3-5 days/week): 1.55
- Very active (6-7 days/week): 1.725
- Extra active (2x/day): 1.9

## 🏛️ Architecture

### Three-Tier Application Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     PRESENTATION TIER                        │
│  ┌─────────────────────────────────────────────────────┐   │
│  │   React 18.2 + Vite 5.0 (Single Page App)          │   │
│  │   - MeasurementForm.jsx (User Input)                │   │
│  │   - TrendChart.jsx (Chart.js Visualization)         │   │
│  │   - App.jsx (Main Component)                        │   │
│  │   - api.js (Axios HTTP Client)                      │   │
│  │   Port: 5173 (dev) | 80/443 via Nginx (prod)       │   │
│  └─────────────────────────────────────────────────────┘   │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTP REST API
                         │ /api/measurements, /api/measurements/stats, etc.
┌────────────────────────▼────────────────────────────────────┐
│                     APPLICATION TIER                         │
│  ┌─────────────────────────────────────────────────────┐   │
│  │   Node.js + Express 4.18 REST API (PM2 managed)    │   │
│  │   - routes.js (API Endpoints)                        │   │
│  │   - calculations.js (BMI/BMR Logic)                  │   │
│  │   - server.js (Express App)                          │   │
│  │   - db.js (PostgreSQL Connection Pool)              │   │
│  │   Port: 3000 (PM2 process: bmi-backend)             │   │
│  └─────────────────────────────────────────────────────┘   │
└────────────────────────┬────────────────────────────────────┘
                         │ PostgreSQL Protocol
                         │ Connection Pool (max 20 connections)
┌────────────────────────▼────────────────────────────────────┐
│                        DATA TIER                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │   PostgreSQL 12+ Database                           │   │
│  │   - Database: bmidb                                  │   │
│  │   - User: bmi_user                                   │   │
│  │   - Table: measurements (11 columns)                 │   │
│  │   - Indexes: date, created_at, BMI                   │   │
│  │   Port: 5432 (localhost only, not exposed)          │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Production Deployment on AWS EC2

```
                         Internet
                            ↓
         ┌──────────────────────────────────────┐
         │   AWS Security Group (Firewall)      │
         │   - Port 22 (SSH) - Your IP only     │
         │   - Port 80 (HTTP) - 0.0.0.0/0      │
         │   - Port 443 (HTTPS) - 0.0.0.0/0    │
         └──────────────────┬───────────────────┘
                            ↓
    ┌────────────────────────────────────────────────┐
    │      EC2 Instance (Ubuntu 22.04 LTS)           │
    │                                                 │
    │  ┌──────────────────────────────────────────┐  │
    │  │ Nginx Web Server (Port 80/443)           │  │
    │  │ - Serves /var/www/bmi-health-tracker/    │  │
    │  │ - Reverse proxy /api/* → localhost:3000  │  │
    │  │ - SSL/TLS with Certbot (optional)        │  │
    │  └─────────────┬────────────────────────────┘  │
    │                │                                │
    │  ┌─────────────▼────────────────────────────┐  │
    │  │ PM2 Process Manager                      │  │
    │  │ - Process name: bmi-backend              │  │
    │  │ - Auto-restart on crash/code changes     │  │
    │  │ - Startup on system reboot               │  │
    │  │ - Log management (error.log, out.log)    │  │
    │  └─────────────┬────────────────────────────┘  │
    │                │                                │
    │  ┌─────────────▼────────────────────────────┐  │
    │  │ Express API Server (Port 3000)           │  │
    │  │ - REST API endpoints                     │  │
    │  │ - BMI/BMR calculations                   │  │
    │  │ - CORS middleware                        │  │
    │  │ - PostgreSQL connection pool             │  │
    │  └─────────────┬────────────────────────────┘  │
    │                │                                │
    │  ┌─────────────▼────────────────────────────┐  │
    │  │ PostgreSQL Database (Port 5432)          │  │
    │  │ - Database: bmidb                        │  │
    │  │ - User: bmi_user (password protected)    │  │
    │  │ - Listens only on localhost              │  │
    │  │ - Connection pooling enabled             │  │
    │  └──────────────────────────────────────────┘  │
    │                                                 │
    │  GitHub Actions (CI/CD Pipeline)                │
    │  - SSH connection for automated deployment      │
    │  - Smart detection (first-time vs update)       │
    │  - Automatic backups before updates             │
    └─────────────────────────────────────────────────┘
```

### Data Flow
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack by Tier

#### Presentation Tier (Frontend)
- **Framework:** React 18 with functional components and hooks
- **Build Tool:** Vite 5 for fast development and optimized production builds
- **UI/UX:** Modern card-based design with gradients and animations
- **Charts:** Chart.js for trend visualization
- **HTTP Client:** Axios for API communication
- **Routing:** SPA with client-side routing

#### Application Tier (Backend)
- **Runtime:** Node.js 18+ with ES modules
- **Framework:** Express.js for REST API
- **Process Manager:** PM2 for production process management
- **Database Client:** node-postgres (pg) for PostgreSQL
- **Validation:** Custom middleware for input validation
- **Error Handling:** Centralized error handling middleware

#### Data Tier (Database)
- **Database:** PostgreSQL 12+ for relational data storage
- **Connection:** Connection pooling for optimal performance
- **Migrations:** SQL migration files for schema management
- **Backup:** Automated backup scripts in database folder

## 🌟 Features

### User Features
- ✅ **Modern Professional UI** - Card-based design with gradient backgrounds and smooth animations
- ✅ **Real-time Stats Dashboard** - Visual stat cards showing BMI, BMR, daily calories, and total records
- ✅ **BMI Calculation** - Instant BMI calculation with health categorization
- ✅ **BMR (Basal Metabolic Rate)** - Calculate your resting metabolic rate using scientifically-proven formulas
- ✅ **Daily Calorie Needs** - Personalized recommendations based on activity level
- ✅ **30-Day BMI Trend** - Beautiful chart visualization of your progress over time
- ✅ **Historical Tracking** - View all measurements with color-coded badges and sorting
- ✅ **Fully Responsive** - Optimized for desktop, tablet, and mobile devices
- ✅ **Enhanced UX** - Loading states, success/error alerts with animations, empty state messages
- ✅ **Professional Form Design** - Multi-column responsive forms with focus states and validation

### Technical Features
- ✅ **RESTful API** - Standard HTTP methods and status codes
- ✅ **Database Migrations** - Version-controlled schema changes
- ✅ **Connection Pooling** - Efficient database connection management
- ✅ **Error Handling** - Comprehensive error handling with proper HTTP status codes
- ✅ **CORS Configuration** - Environment-based CORS setup
- ✅ **Input Validation** - Backend validation for all user inputs
- ✅ **SQL Injection Protection** - Parameterized queries throughout
- ✅ **Health Check Endpoint** - `/health` endpoint for monitoring
- ✅ **Process Management** - PM2 for zero-downtime deployments
- ✅ **Reverse Proxy** - Nginx for static file serving and API proxying
- ✅ **SSL/TLS Support** - HTTPS with Let's Encrypt (Certbot)

## 🔧 Tech Stack

### Frontend
| Technology | Version | Purpose |
|------------|---------|---------|
| React | 18.3.1 | UI framework with hooks and functional components |
| Vite | 5.4.2 | Build tool for fast development and optimized production builds |
| Chart.js | 4.4.1 | Data visualization for BMI trends |
| react-chartjs-2 | 5.2.0 | React wrapper for Chart.js |
| Axios | 1.6.5 | HTTP client for API requests |

### Backend
| Technology | Version | Purpose |
|------------|---------|---------|
| Node.js | 18+ | JavaScript runtime |
| Express | 4.18.2 | Web framework for REST API |
| pg (node-postgres) | 8.11.3 | PostgreSQL client |
| cors | 2.8.5 | Cross-origin resource sharing |
| dotenv | 16.3.1 | Environment variable management |

### Database
| Technology | Version | Purpose |
|------------|---------|---------|
| PostgreSQL | 12+ | Relational database for data persistence |

### DevOps & Infrastructure
| Technology | Purpose |
|------------|---------|
| PM2 | Process manager for Node.js in production |
| Nginx | Reverse proxy and static file server |
| UFW | Firewall configuration |
| Certbot | SSL certificate management (Let's Encrypt) |
| AWS EC2 | Cloud hosting (Ubuntu 22.04 LTS) |

## 🏗️ Project Structure

```
single-ec2-three-tier-app/
│
├── frontend/                          # React Frontend (Presentation Tier)
│   ├── src/
│   │   ├── components/
│   │   │   ├── MeasurementForm.jsx   # Input form for health data
│   │   │   └── TrendChart.jsx         # Chart.js BMI trend visualization
│   │   ├── App.jsx                    # Main React component
│   │   ├── api.js                     # Axios API client configuration
│   │   ├── index.css                  # Global styles
│   │   └── main.jsx                   # React entry point
│   ├── index.html                     # HTML template
│   ├── vite.config.js                 # Vite build configuration
│   ├── package.json                   # Frontend dependencies
│   └── dist/                          # Production build output (generated)
│
├── backend/                           # Node.js Backend (Application Tier)
│   ├── src/
│   │   ├── server.js                  # Express server setup and middleware
│   │   ├── routes.js                  # API route definitions
│   │   ├── db.js                      # PostgreSQL connection pool
│   │   └── calculations.js            # BMI, BMR, calorie calculations
│   ├── migrations/
│   │   └── 001_create_measurements.sql # Database schema migration
│   ├── ecosystem.config.js            # PM2 configuration
│   ├── package.json                   # Backend dependencies
│   └── .env                           # Environment variables (not in git)
│
├── database/                          # Database Management Scripts
│   ├── setup-database.sh              # All-in-one database setup script
│   ├── backup-database.sh             # Database backup script (generated)
│   └── restore-database.sh            # Database restore script (generated)
│
├── GitHubActions-CICD/                # CI/CD Course Materials
│   ├── Session-1-GitHub-Actions/
│   ├── Session-2-Deploy-BMI-App/
│   └── Monitoring/
│       ├── Session-1-Prometheus/
│       └── Session-2-Grafana/
│
├── deploy.sh                          # Comprehensive deployment automation script
│
├── README.md                          # This file
├── AGENT.md                           # Complete project documentation
├── CONNECTIVITY.md                    # Three-tier connectivity configuration
├── BMI_Health_Tracker_Deployment_Readme.md  # Manual deployment guide
├── DEPLOYMENT_CHECKLIST.md            # Pre-deployment checklist
├── DEPLOYMENT_READY.md                # Deployment readiness verification
├── FINAL_AUDIT.md                     # Security and quality audit
└── DevOpsReadme.md                    # DevOps practices and procedures
```

### File Purposes

#### Backend Files
- **server.js:** Express server initialization, middleware setup, CORS configuration
- **routes.js:** All API endpoints (GET /api/measurements, POST /api/measurements, GET /health)
- **db.js:** PostgreSQL connection pool configuration
- **calculations.js:** Health metric calculation functions (BMI, BMR, daily calories)
- **.env:** Database credentials and configuration (DATABASE_URL, PORT)

#### Frontend Files
- **App.jsx:** Main component with state management, API calls, and child component coordination
- **MeasurementForm.jsx:** Form component for user input with validation
- **TrendChart.jsx:** Chart.js wrapper for BMI trend visualization
- **api.js:** Axios instance with base URL configuration
- **index.css:** Global styles, gradient backgrounds, animations

#### Infrastructure Files
- **deploy.sh:** Automated deployment script (backs up, builds, deploys, configures)
- **database/setup-database.sh:** Database initialization with migrations and sample data
- **ecosystem.config.js:** PM2 configuration for process management

## 🚀 Quick Start

### Prerequisites

Before you begin, ensure you have the following installed:

| Software | Version | Installation |
|----------|---------|--------------|
| Node.js | 18+ (LTS) | [nodejs.org](https://nodejs.org) or use NVM |
| PostgreSQL | 12+ | [postgresql.org](https://postgresql.org) |
| npm | 9+ | Included with Node.js |
| Git | Latest | [git-scm.com](https://git-scm.com) |

### Local Development Setup

#### 1. Clone the Repository
```bash
git clone <your-repo-url>
cd single-ec2-three-tier-app
```

#### 2. Database Setup

**Option A: Automated Setup (Recommended)**
```bash
cd database
chmod +x setup-database.sh
./setup-database.sh
```

This script will:
- Install PostgreSQL (if needed)
- Create database and user
- Run migrations
- Create backup/restore scripts
- Optionally add sample data

**Option B: Manual Setup**
```bash
# Create PostgreSQL user
sudo -u postgres createuser --pwprompt bmi_user
# Enter password when prompted: your_password

# Create database
sudo -u postgres createdb -O bmi_user bmidb

# Run migrations
cd backend
psql -U bmi_user -d bmidb -h localhost -f migrations/001_create_measurements.sql
psql -U bmi_user -d bmidb -h localhost -f migrations/002_add_measurement_date.sql
```

#### 3. Backend Setup
```bash
cd backend

# Install dependencies
npm install

# Create .env file
cat > .env << EOF
PORT=3000
DATABASE_URL=postgresql://bmi_user:your_password@localhost:5432/bmidb
NODE_ENV=development
EOF

# Verify database connection
npm run dev
# Should see: "Connected to PostgreSQL" and "Server running on port 3000"
```

#### 4. Frontend Setup
```bash
cd ../frontend

# Install dependencies
npm install

# Start development server
npm run dev
# Should see: "Local: http://localhost:5173"
```

#### 5. Access the Application

Open your browser and navigate to:
- **Frontend:** http://localhost:5173
- **Backend API:** http://localhost:3000/api/measurements
- **Health Check:** http://localhost:3000/health

### Verification Steps

1. **Test Backend:**
   ```bash
   curl http://localhost:3000/health
   # Should return: {"status":"ok","database":"connected"}
   ```

2. **Test Database:**
   ```bash
   curl http://localhost:3000/api/measurements
   # Should return: [] (empty array if no data)
   ```

3. **Test Frontend:**
   - Enter sample data in the form
   - Click "Calculate & Save"
   - Verify stats update and chart displays

## 📡 API Documentation

### Base URL
- **Development:** `http://localhost:3000/api`
- **Production:** `http://your-domain/api`

### Endpoints

#### 1. Get All Measurements
```http
GET /api/measurements
```

**Description:** Retrieves all measurements ordered by date (newest first)

**Response:**
```json
[
  {
    "id": 1,
    "height": 175,
    "weight": 70,
    "age": 30,
    "gender": "male",
    "activity_level": "moderately",
    "bmi": 22.86,
    "bmi_category": "Normal",
    "bmr": 1680,
    "daily_calories": 2604,
    "measurement_date": "2025-12-15",
    "created_at": "2025-12-15T10:30:00.000Z"
  }
]
```

**Status Codes:**
- `200 OK` - Success
- `500 Internal Server Error` - Database error

---

#### 2. Create Measurement
```http
POST /api/measurements
Content-Type: application/json
```

**Description:** Creates a new measurement with calculated health metrics

**Request Body:**
```json
{
  "height": 175,
  "weight": 70,
  "age": 30,
  "gender": "male",
  "activity_level": "moderately",
  "measurementDate": "2025-12-15"  // Optional: defaults to current date
}
```

**Field Validations:**
- `height` (required): Number, 50-300 cm
- `weight` (required): Number, 20-500 kg
- `age` (required): Integer, 10-120 years
- `gender` (required): String, "male" or "female"
- `activity_level` (required): String, one of:
  - "sedentary"
  - "lightly"
  - "moderately"
  - "very"
  - "extra"

**Response:**
```json
{
  "id": 1,
  "height": 175,
  "weight": 70,
  "age": 30,
  "gender": "male",
  "activity_level": "moderately",
  "bmi": 22.86,
  "bmi_category": "Normal",
  "bmr": 1680,
  "daily_calories": 2604,
  "measurement_date": "2025-12-15",
  "created_at": "2025-12-15T10:30:00.000Z"
}
```

**Status Codes:**
- `201 Created` - Measurement created successfully
- `400 Bad Request` - Invalid input data
- `500 Internal Server Error` - Database error

---

#### 3. Health Check
```http
GET /health
```

**Description:** Checks API and database connectivity

**Response:**
```json
{
  "status": "ok",
  "database": "connected"
}
```

**Status Codes:**
- `200 OK` - All systems operational
- `500 Internal Server Error` - Database connection failed

### Example API Usage

#### Using cURL
```bash
# Get all measurements
curl http://localhost:3000/api/measurements

# Create new measurement
curl -X POST http://localhost:3000/api/measurements \
  -H "Content-Type: application/json" \
  -d '{
    "height": 175,
    "weight": 70,
    "age": 30,
    "gender": "male",
    "activity_level": "moderately"
  }'

# Health check
curl http://localhost:3000/health
```

#### Using JavaScript (Axios)
```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://localhost:3000/api'
});

// Get all measurements
const measurements = await api.get('/measurements');

// Create measurement
const newMeasurement = await api.post('/measurements', {
  height: 175,
  weight: 70,
  age: 30,
  gender: 'male',
  activity_level: 'moderately'
});
```

## 🗄️ Database Schema

### Measurements Table

```sql
CREATE TABLE measurements (
    id SERIAL PRIMARY KEY,
    height DECIMAL(5,2) NOT NULL,           -- Height in cm (e.g., 175.50)
    weight DECIMAL(5,2) NOT NULL,           -- Weight in kg (e.g., 70.25)
    age INTEGER NOT NULL,                   -- Age in years
    gender VARCHAR(10) NOT NULL,            -- 'male' or 'female'
    activity_level VARCHAR(20) NOT NULL,    -- Activity level
    bmi DECIMAL(4,2) NOT NULL,              -- Calculated BMI
    bmi_category VARCHAR(20) NOT NULL,      -- BMI category
    bmr INTEGER NOT NULL,                   -- Calculated BMR
    daily_calories INTEGER NOT NULL,        -- Calculated daily calories
    measurement_date DATE NOT NULL DEFAULT CURRENT_DATE,  -- Date of measurement
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP        -- Record creation time
);

-- Index for faster date-based queries
CREATE INDEX idx_measurements_created_at ON measurements(created_at DESC);
CREATE INDEX idx_measurements_measurement_date ON measurements(measurement_date DESC);
```

### Field Descriptions

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | SERIAL | PRIMARY KEY | Auto-incrementing unique identifier |
| height | DECIMAL(5,2) | NOT NULL | Height in centimeters (e.g., 175.50) |
| weight | DECIMAL(5,2) | NOT NULL | Weight in kilograms (e.g., 70.25) |
| age | INTEGER | NOT NULL | Age in years |
| gender | VARCHAR(10) | NOT NULL | Gender: 'male' or 'female' |
| activity_level | VARCHAR(20) | NOT NULL | One of: sedentary, lightly, moderately, very, extra |
| bmi | DECIMAL(4,2) | NOT NULL | Calculated Body Mass Index |
| bmi_category | VARCHAR(20) | NOT NULL | One of: Underweight, Normal, Overweight, Obese |
| bmr | INTEGER | NOT NULL | Calculated Basal Metabolic Rate (calories/day) |
| daily_calories | INTEGER | NOT NULL | Calculated daily calorie needs |
| measurement_date | DATE | NOT NULL, DEFAULT CURRENT_DATE | Date when measurement was taken |
| created_at | TIMESTAMP | DEFAULT NOW() | Record creation timestamp |

### Database Queries

The application uses the following queries:

**Get All Measurements:**
```sql
SELECT * FROM measurements 
ORDER BY measurement_date DESC, created_at DESC;
```

**Insert Measurement:**
```sql
INSERT INTO measurements (
  height, weight, age, gender, activity_level,
  bmi, bmi_category, bmr, daily_calories, measurement_date
) VALUES ($1, $2, $3, $4, $5, $6, $7, $8, $9, $10)
RETURNING *;
```

**Get Recent Measurements (for trend chart):**
```sql
SELECT * FROM measurements 
WHERE measurement_date >= CURRENT_DATE - INTERVAL '30 days'
ORDER BY measurement_date ASC;
```

## 📦 Deployment

### Automated Deployment (Recommended)

The project includes a comprehensive deployment script that automates the entire deployment process.

```bash
# Basic deployment
./deploy.sh

# Fresh deployment (clean install)
./deploy.sh --fresh

# Skip Nginx reconfiguration
./deploy.sh --skip-nginx

# Skip backup creation
./deploy.sh --skip-backup
```

**What the deploy script does:**
1. ✓ Checks all prerequisites (Node.js, PostgreSQL, Nginx, PM2)
2. ✓ Creates backup of current deployment
3. ✓ Installs backend dependencies
4. ✓ Runs database migrations
5. ✓ Builds frontend for production
6. ✓ Deploys frontend to `/var/www/bmi-health-tracker`
7. ✓ Configures PM2 for backend process management
8. ✓ Sets up Nginx reverse proxy
9. ✓ Runs health checks
10. ✓ Displays deployment summary

### Manual Deployment

For detailed manual deployment instructions, see:
- **[BMI_Health_Tracker_Deployment_Readme.md](BMI_Health_Tracker_Deployment_Readme.md)** - Complete step-by-step guide
- **[DEPLOYMENT_CHECKLIST.md](DEPLOYMENT_CHECKLIST.md)** - Pre-deployment checklist

### Deployment Architecture on AWS EC2

```
┌──────────────────────────────────────────────────┐
│              AWS EC2 Instance                     │
│           Ubuntu 22.04 LTS (t2.micro)            │
│                                                   │
│  ┌────────────────────────────────────────────┐ │
│  │  Nginx (Port 80/443)                       │ │
│  │  - Serves frontend from /var/www           │ │
│  │  - Reverse proxy /api/* → localhost:3000  │ │
│  └────────────────┬───────────────────────────┘ │
│                   │                               │
│  ┌────────────────▼───────────────────────────┐ │
│  │  PM2 Process Manager                       │ │
│  │  - Manages Node.js backend                 │ │
│  │  - Auto-restart on failure                 │ │
│  │  - Auto-start on server reboot             │ │
│  └────────────────┬───────────────────────────┘ │
│                   │                               │
│  ┌────────────────▼───────────────────────────┐ │
│  │  Node.js Backend (Port 3000)               │ │
│  │  - Express API                             │ │
│  │  - Health calculations                     │ │
│  └────────────────┬───────────────────────────┘ │
│                   │                               │
│  ┌────────────────▼───────────────────────────┐ │
│  │  PostgreSQL (Port 5432)                    │ │
│  │  - Database: bmidb                         │ │
│  │  - User: bmi_user                          │ │
│  └────────────────────────────────────────────┘ │
│                                                   │
│  Security Group Rules:                           │
│  - Port 22 (SSH) - Your IP only                 │
│  - Port 80 (HTTP) - 0.0.0.0/0                   │
│  - Port 443 (HTTPS) - 0.0.0.0/0                 │
└──────────────────────────────────────────────────┘
```

### Post-Deployment Steps

After deployment:

1. **Test the Application:**
   ```bash
   curl http://your-ec2-ip/api/measurements
   ```

2. **Setup SSL (Optional but Recommended):**
   ```bash
   sudo certbot --nginx -d your-domain.com
   ```

3. **Monitor Logs:**
   ```bash
   # Backend logs
   pm2 logs bmi-backend
   
   # Nginx logs
   sudo tail -f /var/log/nginx/bmi-*.log
   
   # Database logs
   sudo tail -f /var/log/postgresql/postgresql-12-main.log
   ```

4. **Setup Monitoring:**
   - See **GitHubActions-CICD/Monitoring/** for Prometheus and Grafana setup

---

## 🚀 CI/CD with GitHub Actions

This project supports **fully automated deployment** using GitHub Actions. The workflow handles both initial deployment and updates automatically.

### Two Deployment Modes

#### 🆕 Fresh EC2 Instance (Everything Automated)
GitHub Actions will:
- Install Node.js, PostgreSQL, Nginx, PM2
- Setup database and run migrations
- Clone repository and deploy application
- Configure Nginx reverse proxy
- Start services with PM2

**Requirements:**
- Fresh Ubuntu EC2 instance with SSH access
- 4 GitHub Secrets: `EC2_HOST`, `EC2_USER`, `EC2_SSH_KEY`, `DB_PASSWORD`

#### 🔄 Existing Deployment (Updates Only)
GitHub Actions will:
- Create backup before deployment
- Pull latest code
- Run new migrations
- Rebuild and restart services
- Verify with health checks

### Features

- ✅ **Smart Detection:** Automatically detects first-time vs update deployment
- ✅ **Automated Setup:** Installs all prerequisites on fresh servers
- ✅ **Manual Deployment:** Trigger deployments from GitHub UI with options
- ✅ **Backup Creation:** Automatic backup before each update
- ✅ **Database Migrations:** Automatically runs new SQL migrations
- ✅ **Health Checks:** Verifies deployment success
- ✅ **Rollback Support:** Quick rollback to previous versions
- ✅ **Deployment Summary:** Detailed status reports in GitHub Actions

### Quick Setup (Fresh EC2)

1. **Launch EC2 instance** (Ubuntu 22.04, ports 22/80/443 open)

2. **Run minimal setup on EC2:**
   ```bash
   sudo apt update && sudo apt install -y git
   git clone https://github.com/md-sarowar-alam/single-server-3tier-webapp-github-actions.git temp && cd temp
   chmod +x scripts/initial-ec2-setup.sh && ./scripts/initial-ec2-setup.sh
   ```

3. **Generate SSH key locally:**
   ```bash
   ssh-keygen -t ed25519 -C "github-actions" -f ~/.ssh/github-actions-key -N ""
   cat ~/.ssh/github-actions-key.pub  # Copy this
   ```

4. **Add public key to EC2:**
   ```bash
   echo "PASTE_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
   ```

5. **Add 4 GitHub Secrets:**
   - `EC2_HOST`: Your EC2 IP (e.g., `44.245.64.25`)
   - `EC2_USER`: `ubuntu`
   - `EC2_SSH_KEY`: (entire private key from `cat ~/.ssh/github-actions-key`)
   - `DB_PASSWORD`: Choose a strong password for PostgreSQL

6. **Push to trigger deployment:**
   ```bash
   git add . && git commit -m "Initial deployment" && git push origin main
   ```

### Quick Setup (Existing Deployment)

1. **Generate SSH key and add to EC2** (steps 3-4 above)

2. **Add 3 GitHub Secrets** (EC2_HOST, EC2_USER, EC2_SSH_KEY) - DB_PASSWORD optional

3. **Push to main:**
   ```bash
   ```bash
   git add .
   git commit -m "Enable CI/CD"
   git push origin main
   ```

Watch deployment progress in **GitHub Actions** tab. First deployment takes 5-10 minutes.

### Deployment Workflow

```mermaid
graph TD
    A[Push to main] --> B{First Time?}
    B -->|Yes| C[Install Prerequisites]
    B -->|No| D[Create Backup]
    C --> E[Setup Database]
    D --> F[Pull Latest Code]
    E --> G[Clone Repository]
    F --> H{New Migrations?}
    G --> I[Run Migrations]
    H -->|Yes| I
    H -->|No| J[Deploy Backend]
    I --> J
    J --> K[Build Frontend]
    K --> L[Deploy to Nginx]
    L --> M[Health Checks]
    M -->|Pass| N[Success ✅]
    M -->|Fail| O[Rollback Available]
```

### Manual Deployment Options

Trigger deployments manually with options:

- **Backend Only:** Deploy only backend changes
- **Frontend Only:** Deploy only frontend changes
- **Skip Health Check:** Skip post-deployment verification

Go to: **GitHub Actions** → **Deploy to AWS EC2** → **Run workflow**

### Rollback

If deployment fails or causes issues, use the rollback script:

```bash
ssh ubuntu@44.245.64.25
cd ~/single-server-3tier-webapp
./scripts/rollback.sh
```

The script will:
1. Show available backups
2. Let you select which backup to restore
3. Restore files and restart services
4. Run health checks

### Documentation for both scenarios
- **[DATABASE_SETUP.md](DATABASE_SETUP.md)** - Database password configuration guide
- **[.github/workflows/deploy.yml](.github/workflows/deploy.yml)** - Workflow configuration
- **[scripts/rollback.sh](scripts/rollback.sh)** - Rollback script for quick recovery
- **[scripts/initial-ec2-setup.sh](scripts/initial-ec2-setup.sh)** - Minimal EC2 preparation
- **[GITHUB_ACTIONS_SETUP.md](GITHUB_ACTIONS_SETUP.md)** - Complete setup guide
- **[.github/workflows/deploy.yml](.github/workflows/deploy.yml)** - Workflow configuration
- **[scripts/rollback.sh](scripts/rollback.sh)** - Rollback script


## 🔒 Security

### Implemented Security Measures

#### Application Security
- ✅ **Environment Variables:** Sensitive credentials stored in `.env` (not in git)
- ✅ **CORS Configuration:** Environment-based CORS for development/production
- ✅ **SQL Injection Protection:** Parameterized queries with pg library
- ✅ **Input Validation:** Backend validation for all user inputs
- ✅ **Error Handling:** No internal details exposed in error responses
- ✅ **HTTP Security Headers:** X-Frame-Options, X-Content-Type-Options, X-XSS-Protection
- ✅ **Server Tokens:** Nginx version hidden

#### Infrastructure Security
- ✅ **Firewall (UFW):** Only necessary ports open (22, 80, 443)
- ✅ **SSH Access:** Key-based authentication recommended
- ✅ **Database Access:** PostgreSQL bound to localhost only
- ✅ **SSL/TLS:** HTTPS with Let's Encrypt certificates
- ✅ **Process Isolation:** PM2 runs as non-root user

### Security Best Practices

1. **Change Default Credentials:**
   ```bash
   # Update database password in .env
   nano backend/.env
   # Update PostgreSQL password
   sudo -u postgres psql -c "ALTER USER bmi_user WITH PASSWORD 'new_secure_password';"
   ```

2. **Enable Automatic Security Updates:**
   ```bash
   sudo apt install unattended-upgrades
   sudo dpkg-reconfigure --priority=low unattended-upgrades
   ```

3. **Setup Fail2Ban:**
   ```bash
   sudo apt install fail2ban
   sudo systemctl enable fail2ban
   ```

4. **Regular Backups:**
   ```bash
   # Use provided backup script
   ./database/backup-database.sh
   ```

5. **Monitor Logs Regularly:**
   ```bash
   # Check for suspicious activity
   sudo tail -f /var/log/nginx/bmi-access.log
   pm2 logs bmi-backend
   ```

## 📊 Monitoring

### Application Monitoring

#### PM2 Monitoring
```bash
# View process status
pm2 status

# View real-time logs
pm2 logs bmi-backend

# Monitor CPU/Memory
pm2 monit

# View process details
pm2 describe bmi-backend
```

#### Health Check Endpoint
```bash
# Check application health
curl http://localhost/health

# Expected response:
# {"status":"ok","database":"connected"}
```

### System Monitoring

For comprehensive monitoring with Prometheus and Grafana, see:
- **[GitHubActions-CICD/Monitoring/Session-1-Prometheus/](GitHubActions-CICD/Monitoring/Session-1-Prometheus/)** - Prometheus setup
- **[GitHubActions-CICD/Monitoring/Session-2-Grafana/](GitHubActions-CICD/Monitoring/Session-2-Grafana/)** - Grafana dashboards

### Key Metrics to Monitor

| Metric | Tool | Alert Threshold |
|--------|------|----------------|
| CPU Usage | PM2 / Prometheus | > 80% |
| Memory Usage | PM2 / Prometheus | > 85% |
| Response Time | Nginx logs / Prometheus | > 1000ms |
| Error Rate | PM2 logs / Application | > 5% |
| Database Connections | PostgreSQL | > 80% of max |
| Disk Space | System | > 90% |

## 🔧 Troubleshooting

### Common Issues and Solutions

#### 1. Database Connection Failed

**Symptoms:**
```
Error: Connection refused
Database connection failed
```

**Solutions:**
```bash
# Check if PostgreSQL is running
sudo systemctl status postgresql

# Start PostgreSQL if stopped
sudo systemctl start postgresql

# Verify database exists
sudo -u postgres psql -l | grep bmidb

# Check credentials in .env
cat backend/.env

# Test connection manually
psql -U bmi_user -d bmidb -h localhost
```

#### 2. Backend Not Starting

**Symptoms:**
```
pm2 status shows "errored"
Port 3000 already in use
```

**Solutions:**
```bash
# Check PM2 logs
pm2 logs bmi-backend --lines 50

# Kill process on port 3000
sudo lsof -ti:3000 | xargs kill -9

# Restart PM2 process
pm2 restart bmi-backend

# Check for missing dependencies
cd backend && npm install
```

#### 3. Frontend Build Fails

**Symptoms:**
```
npm run build fails
dist folder not created
```

**Solutions:**
```bash
# Clean and rebuild
cd frontend
rm -rf node_modules package-lock.json dist
npm install
npm run build

# Check for syntax errors
npm run lint

# Verify Node.js version
node -v  # Should be 18+
```

#### 4. Nginx 502 Bad Gateway

**Symptoms:**
```
502 Bad Gateway error
Cannot reach /api/* endpoints
```

**Solutions:**
```bash
# Check backend is running
pm2 status

# Test backend directly
curl http://localhost:3000/health

# Check Nginx configuration
sudo nginx -t

# Verify proxy_pass in Nginx config
sudo cat /etc/nginx/sites-available/bmi-health-tracker | grep proxy_pass

# Restart Nginx
sudo systemctl restart nginx
```

#### 5. CORS Errors in Browser

**Symptoms:**
```
Access to XMLHttpRequest blocked by CORS policy
```

**Solutions:**
```javascript
// In backend/.env, ensure CORS is configured
NODE_ENV=production  // or development

// In backend/src/server.js, verify CORS setup
const corsOptions = {
  origin: process.env.NODE_ENV === 'production' 
    ? 'http://your-domain.com' 
    : 'http://localhost:5173'
};
```

#### 6. Migrations Not Applied

**Symptoms:**
```
relation "measurements" does not exist
```

**Solutions:**
```bash
# Run migrations manually
cd backend
psql -U bmi_user -d bmidb -h localhost -f migrations/001_create_measurements.sql
psql -U bmi_user -d bmidb -h localhost -f migrations/002_add_measurement_date.sql

# Verify table exists
psql -U bmi_user -d bmidb -h localhost -c "\dt"
```

### Log Locations

| Component | Log Location | Command |
|-----------|-------------|---------|
| Backend (PM2) | PM2 logs | `pm2 logs bmi-backend` |
| Nginx Access | /var/log/nginx/bmi-access.log | `sudo tail -f /var/log/nginx/bmi-access.log` |
| Nginx Error | /var/log/nginx/bmi-error.log | `sudo tail -f /var/log/nginx/bmi-error.log` |
| PostgreSQL | /var/log/postgresql/ | `sudo tail -f /var/log/postgresql/postgresql-*-main.log` |
| System | /var/log/syslog | `sudo tail -f /var/log/syslog` |

## 📚 Documentation

Comprehensive documentation is available in the following files:

| Document | Description |
|----------|-------------|
| [README.md](README.md) | This file - Project overview, architecture, and quick start |
| [AGENT.md](AGENT.md) | **Complete reconstruction guide** - Everything needed to recreate the project |
| [GITHUB_ACTIONS_SETUP.md](GITHUB_ACTIONS_SETUP.md) | CI/CD setup guide for fresh and existing deployments |
| [DATABASE_SETUP.md](DATABASE_SETUP.md) | Database configuration and password management |
| [CONNECTIVITY.md](CONNECTIVITY.md) | Three-tier architecture and connectivity details |
| [IMPLEMENTATION_GUIDE.md](IMPLEMENTATION_GUIDE.md) | Step-by-step manual AWS EC2 deployment guide |
| [AppUpdate.md](AppUpdate.md) | Manual update procedures and app updates |
| [ADMINISTRATOR_DB.md](ADMINISTRATOR_DB.md) | Database administration guide |

## 🧪 Testing

### Manual Testing

**Backend API:**
```bash
# Health check
curl http://localhost:3000/health

# Get measurements
curl http://localhost:3000/api/measurements

# Create measurement
curl -X POST http://localhost:3000/api/measurements \
  -H "Content-Type: application/json" \
  -d '{"height":175,"weight":70,"age":30,"sex":"male","activity_level":"moderately","measurement_date":"2025-12-18"}'
```

**Database:**
```bash
# Connect to database
psql -U bmi_user -d bmidb -h localhost

# Run test queries
SELECT COUNT(*) FROM measurements;
SELECT * FROM measurements ORDER BY created_at DESC LIMIT 5;
```

### Load Testing

Use Apache Bench or similar tools:
```bash
# Install Apache Bench
sudo apt install apache2-utils

# Test API endpoint (100 requests, 10 concurrent)
ab -n 100 -c 10 http://localhost:3000/api/measurements
```

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/AmazingFeature`)
3. **Commit your changes** (`git commit -m 'Add some AmazingFeature'`)
4. **Push to the branch** (`git push origin feature/AmazingFeature`)
5. **Open a Pull Request**

### Development Guidelines

- Follow existing code style and conventions
- Add tests for new features
- Update documentation for significant changes
- Test deployment locally before submitting PR
- Include clear commit messages

## 🆘 Support

For issues, questions, or contributions:
- Review the [Troubleshooting](#troubleshooting) section
- Check [AGENT.md](AGENT.md) for complete technical details
- Review GitHub Actions workflow logs for deployment issues
- Check log files for error details:
  - Backend: `pm2 logs bmi-backend`
  - Nginx: `sudo tail -f /var/log/nginx/bmi-error.log`
  - Database: `sudo tail -f /var/log/postgresql/postgresql-*-main.log`
- Create an issue in the GitHub repository with:
  - Clear description of the problem
  - Steps to reproduce
  - Expected vs actual behavior
  - Environment details (OS, Node version, etc.)

## 🎯 Project Summary

This BMI & Health Tracker demonstrates:

✅ **Full-Stack Development**: React frontend + Express backend + PostgreSQL database  
✅ **Modern DevOps**: GitHub Actions CI/CD, automated deployment, rollback support  
✅ **Production Ready**: PM2 process management, Nginx reverse proxy, health checks  
✅ **Database Design**: Proper schema, indexes, migrations, connection pooling  
✅ **Security**: Environment variables, CORS, input validation, localhost database  
✅ **Best Practices**: Error handling, logging, backups, documentation  
✅ **Cloud Deployment**: AWS EC2, security groups, scalable architecture  
✅ **Monitoring**: Health endpoints, PM2 monitoring, log management  

## 🚀 Quick Start Summary

### For Fresh EC2 Instance (Fully Automated)

```bash
# 1. Launch EC2 (Ubuntu 22.04, ports 22/80/443)
# 2. SSH and run minimal setup
ssh ubuntu@YOUR_EC2_IP
sudo apt update && sudo apt install -y git
git clone https://github.com/md-sarowar-alam/single-server-3tier-webapp-github-actions.git temp && cd temp
chmod +x scripts/initial-ec2-setup.sh && ./scripts/initial-ec2-setup.sh

# 3. Generate SSH key locally, add to EC2
# 4. Configure 4 GitHub Secrets (EC2_HOST, EC2_USER, EC2_SSH_KEY, DB_PASSWORD)
# 5. Push to main → automatic deployment!
git push origin main
```

### For Existing Deployment (Updates Only)

```bash
# 1. Generate SSH key, add to EC2
# 2. Configure 3 GitHub Secrets (DB_PASSWORD optional)
# 3. Push to main → automatic update with backup!
git push origin main
```

### Local Development

```bash
# Backend
cd backend && npm install && npm run dev

# Frontend (separate terminal)
cd frontend && npm install && npm run dev
```

---

**Repository**: https://github.com/md-sarowar-alam/single-server-3tier-webapp-github-actions.git  
**Last Updated**: December 18, 2025  
**Version**: 2.0.0 (with GitHub Actions CI/CD)  
**Status**: Production Ready ✅

---

🧑‍💻 **Author**  
**Md. Sarowar Alam**  
Lead DevOps Engineer, Hogarth Worldwide  
📧 Email: sarowar@hotmail.com  
🔗 LinkedIn: linkedin.com/in/sarowar
