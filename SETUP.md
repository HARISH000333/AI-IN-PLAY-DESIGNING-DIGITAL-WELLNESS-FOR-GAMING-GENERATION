# Detailed Setup Guide

## System Requirements

### Minimum
- RAM: 4GB
- Storage: 5GB
- OS: Windows 10+, macOS 10.14+, Linux (Ubuntu 18.04+)

### Recommended
- RAM: 8GB+
- Storage: 10GB SSD
- Node.js: 16.x or 18.x LTS
- Python: 3.9+
- MySQL: 8.0+

## Step-by-Step Installation

### Windows Setup

#### 1. Install Node.js
```bash
# Download from https://nodejs.org/
# Select LTS version
# Run installer with default settings
node --version  # Verify installation
npm --version
```

#### 2. Install Python
```bash
# Download from https://www.python.org/downloads/
# During installation: Check "Add Python to PATH"
python --version
pip --version
```

#### 3. Install MySQL
```bash
# Download MySQL Community Server from https://dev.mysql.com/downloads/mysql/
# Run installer
# Choose "MySQL Server" and "MySQL Workbench"
# Remember root password!
mysql --version
```

#### 4. Setup Environment

```bash
# Open Command Prompt as Administrator

# Create project directory
mkdir C:\Projects
cd C:\Projects
mkdir gaming-wellness
cd gaming-wellness

# Clone or extract project files
# ... copy files to this directory ...

# Verify Node.js & Python
node --version
python --version
mysql --version
```

### macOS Setup

```bash
# Install Homebrew first
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Node.js
brew install node

# Install Python
brew install python

# Install MySQL
brew install mysql

# Start MySQL service
brew services start mysql

# Verify installations
node --version
python --version
mysql --version
```

### Linux Setup (Ubuntu)

```bash
# Update package manager
sudo apt-get update
sudo apt-get upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/nodesource-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/nodesource-keyring.gpg] https://deb.nodesource.com/node_18.x nodist main" | sudo tee /etc/apt/sources.list.d/nodesource.list
sudo apt-get update
sudo apt-get install -y nodejs

# Install Python
sudo apt-get install -y python3 python3-pip python3-venv

# Install MySQL
sudo apt-get install -y mysql-server

# Start MySQL
sudo systemctl start mysql
sudo systemctl enable mysql

# Verify
node --version
python3 --version
mysql --version
```

## Backend Installation

### Step 1: Navigate to Backend
```bash
cd backend
```

### Step 2: Install Dependencies
```bash
npm install
```

This installs:
- express - Web framework
- mysql2 - Database driver
- bcryptjs - Password hashing
- jsonwebtoken - JWT authentication
- dotenv - Environment variables
- cors - Cross-origin support
- body-parser - Request parsing
- nodemon - Development auto-reload

### Step 3: Configure Environment

```bash
# Copy example file
cp .env.example .env

# Edit .env with your settings
# Windows: notepad .env
# macOS/Linux: nano .env
```

**Default .env values:**
```
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=
DB_NAME=gaming_wellness_db
DB_PORT=3306
JWT_SECRET=gaming_wellness_secret_2024
PORT=5000
NODE_ENV=development
```

### Step 4: Test Database Connection

```bash
# Open MySQL client
mysql -u root -p

# Create database
CREATE DATABASE gaming_wellness_db;
SHOW DATABASES;
EXIT;
```

### Step 5: Start Backend Server

```bash
# Development mode (with auto-reload)
npm run dev

# Or production mode
npm start

# Expected output:
# 🚀 Server running on http://localhost:5000
# ✅ Database tables initialized successfully
```

## Frontend Setup

The frontend is served by the Express backend, so no separate build is needed for development.

### Optional: Minify for Production

```bash
# Install build tools
npm install -g terser

# Minify JavaScript
terser frontend/js/*.js -o frontend/js/app.min.js

# Minify CSS
npm install -g csso-cli
csso frontend/css/*.css -o frontend/css/style.min.css
```

## ML Models Setup

### Step 1: Navigate to ML Directory
```bash
cd ml_models
```

### Step 2: Create Python Virtual Environment

**Windows:**
```bash
python -m venv venv
venv\Scripts\activate
```

**macOS/Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

### Step 3: Install Dependencies
```bash
pip install -r requirements.txt

# This installs:
# numpy - Numerical computing
# pandas - Data analysis
# scikit-learn - Machine learning
# Flask - ML API server
# joblib - Model serialization
```

### Step 4: Train Models
```bash
python train_models.py

# Expected output:
# ✅ Dataset loaded or generated
# 📊 Linear Regression Model trained
# 📊 Logistic Regression Model trained
# 📊 KNN Model trained
# ✅ All models trained successfully!
# 💾 Models saved to disk
```

**Generated files:**
- `model_linear_regression.pkl` - Wellness prediction
- `model_logistic_regression.pkl` - Risk classification
- `model_knn.pkl` - Pattern recognition
- `model_scaler.pkl` - Feature scaling
- `model_features.pkl` - Feature names

### Step 5: Start ML API (Optional)

For production predictions:
```bash
python ml_api.py
# ML API runs on http://localhost:5001
```

For development, fallback predictions are used.

## Verification Checklist

### Backend
```bash
# Check server is running
curl http://localhost:5000/api/health

# Expected response:
# {"status":"Server is running","timestamp":"..."}
```

### Database
```bash
# Check tables created
mysql -u root -p gaming_wellness_db -e "SHOW TABLES;"

# Should show:
# parents
# children
# gaming_sessions
# wellness_alerts
# gaming_analytics
```

### Frontend
```bash
# Open in browser
http://localhost:5000

# Should see:
# - Gaming Wellness landing page
# - Login button
# - Features section
```

### ML Models
```bash
# Check model files exist
ls -la ml_models/model_*.pkl

# Should show 5 files
```

## Running the Application

### Development Mode (All Services)

**Terminal 1 - Backend:**
```bash
cd backend
npm run dev
```

**Terminal 2 - ML API (Optional):**
```bash
cd ml_models
source venv/bin/activate  # or venv\Scripts\activate on Windows
python ml_api.py
```

**Terminal 3 - Browser:**
```bash
open http://localhost:5000
# or navigate manually to localhost:5000
```

### Production Mode

#### Build Frontend
```bash
cd frontend
# Minify assets (optional)
# Update index.html to use .min.js/.min.css
```

#### Set Production Environment
```bash
cd backend
# Edit .env
# Set NODE_ENV=production
# Set secure JWT_SECRET
```

#### Start Server
```bash
npm start
# Runs on port 5000 without auto-reload
```

#### Use Process Manager
```bash
# Install PM2
npm install -g pm2

# Start with PM2
pm2 start server.js --name "gaming-wellness"
pm2 startup
pm2 save

# Check status
pm2 status
```

## Database Management

### Backup Database
```bash
mysqldump -u root -p gaming_wellness_db > backup.sql
```

### Restore Database
```bash
mysql -u root -p gaming_wellness_db < backup.sql
```

### Reset Tables
```bash
# Connect to MySQL
mysql -u root -p gaming_wellness_db

# Drop all tables
SET FOREIGN_KEY_CHECKS = 0;
DROP TABLE IF EXISTS wellness_alerts;
DROP TABLE IF EXISTS gaming_analytics;
DROP TABLE IF EXISTS gaming_sessions;
DROP TABLE IF EXISTS children;
DROP TABLE IF EXISTS parents;
SET FOREIGN_KEY_CHECKS = 1;

# Exit
EXIT;

# Restart server to recreate tables
npm start
```

## Troubleshooting

### Port Already in Use

**Windows:**
```bash
netstat -ano | findstr :5000
taskkill /PID <PID> /F
```

**macOS/Linux:**
```bash
lsof -i :5000
kill -9 <PID>
```

### MySQL Connection Refused

```bash
# Check MySQL is running
# Windows: Services -> MySQL80 (or your version)
# Linux: sudo systemctl status mysql
# macOS: brew services list

# Try connecting directly
mysql -h 127.0.0.1 -u root -p

# If password needed but .env empty, set password
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('new_password');
```

### Node Modules Issues

```bash
# Clear and reinstall
rm -rf node_modules package-lock.json
npm install
```

### Python Module Not Found

```bash
# Ensure virtual environment activated
# Windows: venv\Scripts\activate
# Linux/Mac: source venv/bin/activate

# Reinstall requirements
pip install --upgrade -r requirements.txt
```

### Models Not Found

```bash
cd ml_models
python train_models.py

# Verify pkl files created
ls -la model_*.pkl
```

## Security Configuration

### For Development
```
JWT_SECRET=dev_secret_key
DB_PASSWORD=empty_or_simple
NODE_ENV=development
```

### For Production
```
JWT_SECRET=generate_long_random_string
DB_PASSWORD=strong_random_password
NODE_ENV=production
```

**Generate secure secret:**
```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

## Performance Optimization

### Backend
```bash
# Enable gzip compression
npm install compression
# Add to server.js: app.use(compress());

# Enable caching
npm install redis
```

### Database
```sql
-- Add indexes
CREATE INDEX idx_parent_email ON parents(email);
CREATE INDEX idx_child_parent ON children(parent_id);
CREATE INDEX idx_session_child ON gaming_sessions(child_id);
CREATE INDEX idx_alert_read ON wellness_alerts(is_read);
```

### Frontend
```javascript
// Enable service workers for offline support
// npm install workbox-cli
```

## Monitoring

### Server Health
```bash
# Check running processes
ps aux | grep node  # Linux/Mac
tasklist | findstr node  # Windows

# Monitor logs
tail -f backend/logs/app.log  # if logging enabled
```

### Database Size
```sql
SELECT table_name, ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb
FROM information_schema.tables
WHERE table_schema = 'gaming_wellness_db'
ORDER BY size_mb DESC;
```

## Deployment

### Docker (Optional)

Create `Dockerfile`:
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 5000
CMD ["npm", "start"]
```

Build and run:
```bash
docker build -t gaming-wellness .
docker run -p 5000:5000 gaming-wellness
```

### Cloud Deployment

See `DEPLOYMENT.md` for:
- AWS Deployment
- Azure Deployment
- Heroku Deployment
- DigitalOcean Deployment

---

For support, check the README.md or contact support@gamingwellness.com
