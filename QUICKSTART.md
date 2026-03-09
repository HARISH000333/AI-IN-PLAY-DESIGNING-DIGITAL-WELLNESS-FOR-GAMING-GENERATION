# Quick Start Guide

## 5-Minute Setup

### 1. Prerequisites Check
```bash
node --version  # Should be 14+
npm --version
python --version  # Should be 3.8+
mysql --version  # Should be 5.7+
```

### 2. Create Directories
```bash
mkdir -p Project2/backend Project2/frontend Project2/ml_models
cd Project2
```

### 3. Backend Quick Start

```bash
cd backend

# Install dependencies
npm install

# Create .env
cp .env.example .env

# Update .env with MySQL credentials
# Default: user=root, password=, database=gaming_wellness_db

# Start server
npm start
```

Server runs on: **http://localhost:5000**

### 4. Database Setup
```bash
# Quick setup
mysql -u root << EOF
CREATE DATABASE gaming_wellness_db;
EOF
```

Tables auto-create on first server run!

### 5. ML Models (Optional)
```bash
cd ml_models

# Install Python dependencies
pip install -r requirements.txt

# Train models
python train_models.py
```

Generates: `model_*.pkl` files

### 6. Access Application

Open browser: **http://localhost:5000**

## Test Accounts

### Try It Out

**Parent Demo:**
- Email: parent@demo.com
- Password: demo123456

**Create Parent Account:**
1. Click "Parent Login"
2. Click "Sign Up"
3. Fill in details
4. Create account

**Create Child Account:**
1. Login as parent
2. Go to "My Children"
3. Click "Add Child"
4. Fill in child details

**Play Games:**
1. Login as child
2. Click "Games"
3. Select and play
4. Submit score

## Common Tasks

### Reset Database
```bash
mysql -u root -p gaming_wellness_db << EOF
DROP TABLE IF EXISTS wellness_alerts;
DROP TABLE IF EXISTS gaming_analytics;
DROP TABLE IF EXISTS gaming_sessions;
DROP TABLE IF EXISTS children;
DROP TABLE IF EXISTS parents;
EOF

# Server recreates tables on next run
```

### View Database
```bash
mysql -u root -p gaming_wellness_db
SHOW TABLES;
SELECT * FROM parents;
EXIT;
```

### Check Server Status
```bash
curl http://localhost:5000/api/health
```

### View Logs
```bash
# Terminal shows server logs
# Check for "Error" messages
```

## Troubleshooting Quick Fix

| Issue | Solution |
|-------|----------|
| Port 5000 in use | `taskkill /PID <pid> /F` (Windows) |
| MySQL connection error | Check `.env` credentials |
| Models not found | Run `python train_models.py` in ml_models |
| CORS error | Ensure backend running on :5000 |
| Blank page | Check browser console (F12) |

## Performance Tips

- Use modern browser (Chrome/Firefox)
- Clear browser cache if issues
- Check network tab for API errors
- Restart server if hung

## Next Steps

1. ✅ Read `README.md` for full documentation
2. ✅ Check `DATABASE.md` for schema details
3. ✅ Review `SETUP.md` for detailed installation
4. ✅ Explore code comments for implementation details

## Need Help?

```bash
# Check all services running
netstat -tuln | grep LISTEN  # Linux/Mac
netstat -ano | findstr LISTEN  # Windows

# Check Node/Express logs for errors
npm start  # Run with verbose output

# Test API endpoints
curl -X GET http://localhost:5000/api/health
```

---

**Ready to build?** Start with the main frontend on `http://localhost:5000` 🚀
