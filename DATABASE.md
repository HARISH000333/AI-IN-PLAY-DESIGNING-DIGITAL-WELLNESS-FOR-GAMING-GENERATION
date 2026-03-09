# Database Schema Documentation

## Tables Overview

### 1. Parents Table
Stores parent account information and credentials.

```sql
CREATE TABLE parents (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

**Columns:**
- `id`: Unique identifier
- `email`: Parent email (unique, used for login)
- `password`: Hashed password (bcryptjs)
- `first_name`: Parent first name
- `last_name`: Parent last name
- `phone`: Contact phone number
- `created_at`: Account creation timestamp
- `updated_at`: Last update timestamp

### 2. Children Table
Stores child account information linked to parents.

```sql
CREATE TABLE children (
    id INT PRIMARY KEY AUTO_INCREMENT,
    parent_id INT NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    age INT,
    interests VARCHAR(500),
    gaming_limit_minutes INT DEFAULT 120,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (parent_id) REFERENCES parents(id) ON DELETE CASCADE
);
```

**Columns:**
- `id`: Unique identifier
- `parent_id`: Reference to parent (foreign key)
- `email`: Child email (unique, used for login)
- `password`: Hashed password
- `first_name`: Child first name
- `last_name`: Child last name
- `age`: Child age
- `interests`: Child gaming interests
- `gaming_limit_minutes`: Daily gaming limit in minutes (default 120)
- `created_at`: Account creation timestamp
- `updated_at`: Last update timestamp

### 3. Gaming Sessions Table
Stores individual gaming session records.

```sql
CREATE TABLE gaming_sessions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    child_id INT NOT NULL,
    game_type VARCHAR(100),
    duration_minutes INT DEFAULT 0,
    score INT DEFAULT 0,
    start_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    end_time TIMESTAMP NULL,
    wellness_score FLOAT DEFAULT 0,
    alert_triggered BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (child_id) REFERENCES children(id) ON DELETE CASCADE
);
```

**Columns:**
- `id`: Unique session identifier
- `child_id`: Reference to child (foreign key)
- `game_type`: Type of game (memory, puzzle, typing, etc.)
- `duration_minutes`: Session duration in minutes
- `score`: Game score achieved
- `start_time`: Session start timestamp
- `end_time`: Session end timestamp (NULL if ongoing)
- `wellness_score`: AI-calculated wellness score (0-1)
- `alert_triggered`: Whether alert was sent
- `created_at`: Record creation timestamp

### 4. Wellness Alerts Table
Stores system alerts triggered by gaming activities.

```sql
CREATE TABLE wellness_alerts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    child_id INT NOT NULL,
    parent_id INT NOT NULL,
    session_id INT,
    alert_type VARCHAR(100),
    message TEXT,
    severity ENUM('LOW', 'MEDIUM', 'HIGH') DEFAULT 'MEDIUM',
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (child_id) REFERENCES children(id) ON DELETE CASCADE,
    FOREIGN KEY (parent_id) REFERENCES parents(id) ON DELETE CASCADE,
    FOREIGN KEY (session_id) REFERENCES gaming_sessions(id) ON DELETE SET NULL
);
```

**Columns:**
- `id`: Unique alert identifier
- `child_id`: Child associated with alert
- `parent_id`: Parent receiving alert
- `session_id`: Related gaming session
- `alert_type`: Type of alert (HIGH_DURATION, LOW_WELLNESS, etc.)
- `message`: Alert message
- `severity`: Alert severity level
- `is_read`: Whether parent has read the alert
- `created_at`: Alert creation timestamp

### 5. Gaming Analytics Table
Aggregated gaming statistics for each child.

```sql
CREATE TABLE gaming_analytics (
    id INT PRIMARY KEY AUTO_INCREMENT,
    child_id INT NOT NULL,
    total_sessions INT DEFAULT 0,
    total_gaming_minutes INT DEFAULT 0,
    average_session_duration INT DEFAULT 0,
    total_score INT DEFAULT 0,
    wellness_trend FLOAT DEFAULT 0,
    last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (child_id) REFERENCES children(id) ON DELETE CASCADE
);
```

**Columns:**
- `id`: Unique analytics record
- `child_id`: Reference to child
- `total_sessions`: Total gaming sessions count
- `total_gaming_minutes`: Total minutes spent gaming
- `average_session_duration`: Average session duration
- `total_score`: Total score across all games
- `wellness_trend`: Trend indicator (-1 to 1)
- `last_updated`: Last update timestamp

## Relationships

```
Parents (1) ──→ (Many) Children
         ├─────→ (Many) Wellness Alerts
         └─────→ (Via Children) Gaming Sessions

Children (1) ──→ (Many) Gaming Sessions
          ├─────→ (Many) Wellness Alerts
          └─────→ (One) Gaming Analytics

Gaming Sessions (1) ──→ (0..1) Wellness Alerts
```

## Indexes

For optimal performance, the following indexes are recommended:

```sql
-- Authentication indexes
CREATE INDEX idx_parent_email ON parents(email);
CREATE INDEX idx_child_email ON children(email);

-- Foreign key indexes
CREATE INDEX idx_child_parent ON children(parent_id);
CREATE INDEX idx_session_child ON gaming_sessions(child_id);
CREATE INDEX idx_alert_child ON wellness_alerts(child_id);
CREATE INDEX idx_alert_parent ON wellness_alerts(parent_id);
CREATE INDEX idx_analytics_child ON gaming_analytics(child_id);

-- Query optimization indexes
CREATE INDEX idx_session_date ON gaming_sessions(start_time);
CREATE INDEX idx_alert_date ON wellness_alerts(created_at);
CREATE INDEX idx_alert_read ON wellness_alerts(is_read);
```

## Sample Queries

### Get parent's children
```sql
SELECT * FROM children WHERE parent_id = ?;
```

### Get child's gaming sessions today
```sql
SELECT * FROM gaming_sessions 
WHERE child_id = ? AND DATE(start_time) = CURDATE();
```

### Calculate daily gaming time
```sql
SELECT SUM(duration_minutes) as total_minutes  
FROM gaming_sessions 
WHERE child_id = ? AND DATE(start_time) = CURDATE();
```

### Get unread alerts
```sql
SELECT * FROM wellness_alerts 
WHERE parent_id = ? AND is_read = FALSE
ORDER BY created_at DESC;
```

### Get child analytics
```sql
SELECT * FROM gaming_analytics WHERE child_id = ?;
```

### Weekly gaming breakdown
```sql
SELECT DATE(start_time) as date, 
       SUM(duration_minutes) as minutes,
       COUNT(*) as sessions
FROM gaming_sessions
WHERE child_id = ? AND start_time >= DATE_SUB(NOW(), INTERVAL 7 DAY)
GROUP BY DATE(start_time)
ORDER BY date DESC;
```

## Data Integrity

### Constraints
- Primary keys on all tables
- Foreign keys with CASCADE delete for orphan prevention
- Unique constraints on email fields
- Check constraints on numeric ranges (0-1 for scores)
- NOT NULL constraints on critical fields

### Backup Strategy
- Daily automated backups
- Point-in-time recovery capability
- Retention: 30 days minimum

## Performance Considerations

- Index frequently queried columns
- Archive old session data (>1 year)
- Partition large tables by date
- Regular ANALYZE TABLE for query optimization
- Connection pooling with max 10 connections

## Data Retention Policy

- **Gaming Sessions**: Keep 2 years
- **Alerts**: Keep 1 year
- **Analytics**: Keep permanently (aggregated)
- **User Accounts**: Keep indefinitely

---

For migration from development to production, use:
```bash
mysqldump -u root -p gaming_wellness_db > backup.sql
```
