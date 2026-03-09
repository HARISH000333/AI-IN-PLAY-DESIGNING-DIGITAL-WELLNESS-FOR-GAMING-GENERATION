# Gaming Wellness Platform - Complete Setup Instructions

## Table of Contents
1. [System Requirements](#system-requirements)
2. [Pre-Installation](#pre-installation)
3. [Installation Steps](#installation-steps)
4. [Configuration](#configuration)
5. [Running the Application](#running-the-application)
6. [Verification](#verification)
7. [Troubleshooting](#troubleshooting)
8. [Optional: ML Model Setup](#optional-ml-model-setup)

---

## System Requirements

### Minimum Requirements
- **OS**: Windows 10/11, macOS 10.14+, or Linux
- **RAM**: 4GB
- **Disk Space**: 500MB
- **Network**: Active internet connection (for initial setup)

### Software Requirements
- **Node.js**: v16.0 or higher
- **npm**: v7.0 or higher (comes with Node.js)
- **Python**: v3.8 or higher (optional, for ML features)
- **Browser**: Chrome, Firefox, Safari, or Edge (latest version)

---

## Pre-Installation

### Step 1: Verify Node.js Installation

**Windows PowerShell:**
```powershell
node --version
npm --version
```

**Expected Output:**
```
v18.x.x (or higher)
9.x.x (or higher)
```

### Step 2: Download/Clone Project

Navigate to your desired project location:
```powershell
cd "c:\Users\haris\OneDrive\New folder"
```

If you haven't already, clone or download the project:
```bash
git clone <repository-url>
cd Project2
```

---

## Installation Steps

### Step 1: Install Backend Dependencies

Navigate to the backend folder:
```powershell
cd backend
```

Install npm packages:
```powershell
npm install
```

**Expected Output:**
```
added 45 packages, and audited 46 packages
found 0 vulnerabilities
```

**Key Packages Installed:**
- express (Web framework)
- bcryptjs (Password hashing)
- jsonwebtoken (JWT authentication)
- cors (Cross-origin requests)
- body-parser (Request parsing)

### Step 2: Install Python Dependencies (Optional)

If you want to use ML features, navigate to the ml_models folder:
```powershell
cd ..\ml_models
```

Create a virtual environment (recommended):
```powershell
python -m venv venv
.\venv\Scripts\activate
```

Install Python packages:
```powershell
pip install -r requirements.txt
```

**Key Packages:**
- scikit-learn (ML algorithms)
- flask (API server)
- pandas, numpy (Data processing)

---

## Configuration

### Step 1: Environment Variables (Optional)

Create `.env` file in backend folder with custom settings:
```
PORT=5000
NODE_ENV=development
JWT_SECRET=your-secret-key-here
DB_FILE=./database.json
```

**Default Configuration** (if .env not created):
- Port: 5000
- Environment: development
- JWT Expiry: 30 days
- Database: database.json (auto-created)

### Step 2: Database Setup

The application automatically creates and initializes `database.json` on first run. No manual setup needed.

**Database Location:**
```
Project2/backend/database.json
```

### Step 3: Verify Project Structure

Ensure all required folders exist:
```
Project2/
├── backend/
│   ├── routes/
│   ├── config/
│   ├── middleware/
│   ├── server.js
│   └── package.json
├── frontend/
│   ├── index.html
│   ├── css/
│   └── js/
├── ml_models/ (optional)
├── docs/
├── database.json (auto-created)
└── README.md
```

---

## Running the Application

### Step 1: Start Backend Server

From backend folder:
```powershell
npm start
```

Or use development mode with auto-reload:
```powershell
npm run dev
```

**Expected Console Output:**
```
🚀 Gaming Wellness API running on http://localhost:5000
📁 Database loaded successfully
✅ Server ready to receive requests
```

### Step 2: Access Frontend

Open your browser and navigate to:
```
http://localhost:5000
```

**Expected:** Gaming Wellness landing page with login/signup options

### Step 3: Verify Server is Running

Test API health endpoint:
```powershell
curl http://localhost:5000/api/health
```

**Expected Response:**
```json
{"status":"Server is running","timestamp":"2026-03-09T12:34:56.789Z"}
```

---

## Verification

### Test 1: Parent Signup

Create a test parent account:

**Using Browser:**
1. Click "Parent Login" button
2. Click "Sign Up" link
3. Fill form with:
   - Contact Name: john
   - Last Name: doe
   - Email: test@example.com
   - Contact Number: +1 234-567-8900
   - Address: 123 Test Street
   - Password: Test@123
4. Click "Create Account"

**Expected Result:**
- ✅ Signup successful
- ✅ Redirected to dashboard
- ✅ JWT token saved locally
- ✅ User profile displays

### Test 2: Parent Login

**Using Browser:**
1. Click "Logout" (if already logged in)
2. Click "Parent Login"
3. Enter credentials
4. Click "Login"

**Expected Result:**
- ✅ Login successful
- ✅ Dashboard loads with parent data
- ✅ Can view profile and children list

### Test 3: API Testing

Test signup endpoint with curl:
```powershell
$body = @{
    email = "test2@example.com"
    password = "Password123"
    contactName = "john"
    lastName = "doe"
    contactNumber = "+1 234-567-8900"
    address = "123 Test Street"
} | ConvertTo-Json

Invoke-WebRequest -Uri http://localhost:5000/api/auth/parent-signup `
  -Method POST `
  -ContentType "application/json" `
  -Body $body
```

**Expected Response:**
```json
{
  "success": true,
  "message": "Parent registered successfully",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1234567890,
    "email": "test2@example.com",
    "role": "parent"
  }
}
```

---

## Troubleshooting

### Issue 1: "Port 5000 already in use"

**Solution Option 1:** Kill process using port 5000
```powershell
Stop-Process -Id (Get-NetTCPConnection -LocalPort 5000).OwningProcess -Force
```

**Solution Option 2:** Change port in server.js
```javascript
const PORT = process.env.PORT || 3000; // Change 5000 to 3000
```

### Issue 2: "Node not recognized"

**Solution:**
1. Reinstall Node.js from nodejs.org
2. Restart PowerShell/Command Prompt
3. Verify installation: `node --version`

### Issue 3: "npm install fails"

**Solution:**
```powershell
# Clear npm cache
npm cache clean --force

# Delete node_modules folder
Remove-Item -Recurse node_modules

# Reinstall
npm install
```

### Issue 4: "Database.json not found"

**Solution:**
- Server will auto-create it on first run
- If not, manually create: `backend/database.json` with:
```json
{
  "parents": [],
  "children": [],
  "gaming_sessions": [],
  "wellness_alerts": [],
  "gaming_analytics": []
}
```

### Issue 5: "Cannot POST /api/auth/parent-signup"

**Solution:**
1. Verify backend server is running
2. Check CORS is enabled in server.js
3. Ensure request Content-Type is `application/json`

---

## Optional: ML Model Setup

### Step 1: Activate Python Environment

```powershell
cd ml_models
.\venv\Scripts\activate
```

### Step 2: Train Models (Optional)

```powershell
python train_models.py
```

**Expected Output:**
```
🚀 Starting ML Model Training...
📊 Loading dataset...
🤖 Training Linear Regression...
🤖 Training Logistic Regression...
🤖 Training KNN Classifier...
✅ All models trained successfully!
```

### Step 3: Start ML API Server (Optional)

```powershell
python ml_api.py
```

**Expected Output:**
```
 * Running on http://127.0.0.1:5001
 * WARNING: This is a development server. Do not use it in production.
```

---

## Production Deployment

### Pre-Deployment Checklist

- [ ] Change `NODE_ENV` to `production`
- [ ] Update `JWT_SECRET` to a strong random key
- [ ] Disable console logging
- [ ] Enable HTTPS
- [ ] Set up proper error handling
- [ ] Configure database backups
- [ ] Update dependencies to latest stable versions

### Recommended Hosting

- **Heroku**: `git push heroku main`
- **AWS**: EC2 with Node.js AMI
- **DigitalOcean**: Droplet with Node.js
- **Azure**: App Service for Node.js
- **Vercel**: For frontend deployment

---

## Maintenance

### Regular Tasks

**Weekly:**
- Check server logs
- Monitor database size
- Review user feedback

**Monthly:**
- Update npm packages: `npm update`
- Review security patches
- Backup database.json

**Quarterly:**
- Update Node.js version
- Update all dependencies
- Review performance metrics

### Database Maintenance

View current data:
```powershell
# Open database.json in your editor
cat database.json | ConvertFrom-Json | ConvertTo-Json -Depth 10
```

Reset database (WARNING - deletes all data):
```powershell
Remove-Item database.json
# Restart server to recreate
```

---

## Getting Help

### Documentation
- 📖 [README.md](README.md) - Project overview
- 📚 [API Documentation](docs/API.md) - Endpoint reference
- 🗄️ [Database Schema](docs/DATABASE.md) - Data structure
- ✨ [Features Guide](docs/FEATURES.md) - Feature documentation

### Support Resources
- Check existing GitHub issues
- Review troubleshooting section above
- Check server console for error messages
- Verify network connectivity

---

## Quick Reference

### Start Server
```powershell
cd backend
npm start
```

### Access Application
```
http://localhost:5000
```

### Check Server Health
```powershell
curl http://localhost:5000/api/health
```

### Stop Server
```
Press Ctrl + C in terminal
```

### View Database
```powershell
cat database.json
```

---

**Setup Complete!** 🎉

Your Gaming Wellness Platform is ready to use. Start by signing up as a parent and exploring all the features in the dashboard.

For questions or issues, refer to the documentation or troubleshooting section above.

**Happy Gaming!** 🎮
