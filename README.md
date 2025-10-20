# Amazon RDS for PostgreSQL – Foundation Workshop (Hands-on)

This repository contains my step-by-step notes and screenshots from **AWS Workshop Studio: Amazon RDS for PostgreSQL (Foundation)**.  
I deployed a PostgreSQL instance, created a **read replica**, built a **Multi-AZ DB cluster (3 instances)**, configured **monitoring/alarms**, and managed the database with **psql** (VS Code Server) and **pgAdmin** (DB, schema, tables, roles, users, grants, function).


## 🧭 Project Overview

**What I practiced**
- Provisioning with **CloudFormation**
- Creating **RDS for PostgreSQL** (db.t3.medium)
- Creating a **Read Replica**
- Creating a **Multi-AZ DB replica cluster** (writer + 2 readers)
- **Monitoring**: CloudWatch metrics, Enhanced Monitoring, Database Insights, alarms
- **Backups**: Manual DB snapshot
- **Eventing**: RDS event subscription (failover)
- **Database work** in **psql** and **pgAdmin**:
  - Database, schema, tables
  - Queries (INSERT/SELECT)
  - Roles & users and grants
  - Function
  - Querying **pg_catalog**

---

## 🏗️ Implementation Steps


![Workshop title](screenshots/title_screenshot.png)

---

### 1) Provision resources with CloudFormation
Create the stack and nested stacks for VPC, security groups and VS Code server.

![Create stack](screenshots/stack-create.png)  
![Stack complete](screenshots/stack-create-completed.png)

---

### 2) RDS for PostgreSQL – Primary instance
Region **us-east-1**, class **db.t3.medium**.

![RDS instance dashboard](screenshots/AmazonRDS-active.png)

---

### 3) Connect via VS Code Server & psql
Export env vars and connect with `psql`.

![Connected with psql](screenshots/connected_to_database.png)

---

### 4) Create a Read Replica
Create `rds-pg-labs-read` from the primary instance.

![Create read replica wizard](screenshots/create-read-replica.png)  
![Replica created](screenshots/read-replica-created.png)  
![Replication view](screenshots/read-replica-instance.png)

---

### 5) (Optional) Vertical scaling
Check/adjust instance class.

![Vertical scaling](screenshots/perform-vertical-scaling.png)

---

### 6) Multi-AZ DB Cluster (3 instances)
Create a **Multi-AZ DB replica cluster** with 3 instances.

![Create Multi-AZ cluster](screenshots/create-multiAZ-cluster-db.png)  
![Creating](screenshots/creating-multiAZ-cluster-db.png)  
![Created](screenshots/created-multiAZ-cluster-db.png)

---

### 7) RDS Event Subscription (Failover)
Create email subscription for **Failover** events.

![Event subscription](screenshots/failover-event-subscription.png)  
![Creating](screenshots/creating-failover-event.png)  
![Active](screenshots/created-failover-event.png)

---

### 8) Manual DB Snapshot
Take and verify a manual snapshot.

![Take snapshot](screenshots/manuel-snapshot.png)  
![Backing up](screenshots/manuel-snapshot2.png)  
![Snapshot list](screenshots/manuel-snapshot3.png)  
![Created](screenshots/manuel-snapshot-creted.png)

---

### 9) CloudWatch Logs & Metrics
Inspect PostgreSQL logs and metrics.

![CloudWatch logs](screenshots/cloudwatchLogs.png)  
![CPUUtilization](screenshots/cloudwatch-cpuutilication.png)  
![Metrics](screenshots/cloudwatch-metrics.png)

---

### 10) Create a CPU alarm
Email alarm: **Average CPUUtilization > 15% for 1 minute**.

![Create CloudWatch Alarm](screenshots/create-cloudwatch-alarm.png)

---

### 11) Enhanced Monitoring & Database Insights
Review EM charts and DB Insights dashboards.

![Enhanced Monitoring](screenshots/enhanced-monitoring.png)  
![Database Insights](screenshots/database-insights.png)  
![Fleet Health](screenshots/fleet-health.png)  
![Telemetry](screenshots/database-telemetary.png)

---

### 12) pgAdmin – Connect & Create Objects

**a) Configure connection**  
![pgAdmin connection](screenshots/configure-pgadmin.png)

**b) Create database** – `first_database`  
![Create DB](screenshots/create-database-pgadmin.png)

**c) Create schema** – `first_schema`  
![Create schema](screenshots/create-schema-pgadmin.png)

**d) Create tables**  
![Create table](screenshots/create-table-pgadmin.png)  
![Create second table](screenshots/create-second-table-pgadmin.png)

**e) Insert / Select**  
![First query](screenshots/first-query-pgadmin.png)

**f) Roles & Users**  
![Create role](screenshots/create-role-pgadmin.png)  
![Grant privileges](screenshots/grand-privileges-to-role.png)  
![Create user](screenshots/create-user-pgadmin.png)

**g) Function**  
![Create function](screenshots/create-function-pgadmin.png)

**h) Explore pg_catalog**  
![Query pg_catalog](screenshots/query-pg-catalog.png)

---

## 🧪 SQL Snippets

```sql
-- first table
CREATE TABLE first_schema.first_table
(
  col1 text NOT NULL,
  PRIMARY KEY (col1)
);
ALTER TABLE IF EXISTS first_schema.first_table OWNER TO postgres;

-- second table
CREATE TABLE IF NOT EXISTS first_schema.second_table(
  col_pk serial,
  col_money numeric(12,2),
  col_short_str varchar(100),
  col_datetime timestamp(0) without time zone
);

-- role & grants
CREATE ROLE first_role WITH
  NOLOGIN NOSUPERUSER NOCREATEDB NOCREATEROLE INHERIT NOREPLICATION NOBYPASSRLS
  CONNECTION LIMIT -1;

GRANT USAGE ON SCHEMA first_schema TO first_role;
GRANT SELECT ON first_schema.first_table TO first_role;

-- user with role
CREATE ROLE first_user WITH
  LOGIN NOSUPERUSER NOCREATEDB NOCREATEROLE INHERIT NOREPLICATION NOBYPASSRLS
  CONNECTION LIMIT -1
  PASSWORD 'xxxxxx';
GRANT first_role TO first_user;

-- function
CREATE OR REPLACE FUNCTION first_schema.cs_fmt_browser_version(v_name varchar, v_version varchar)
RETURNS varchar AS $$
BEGIN
  IF v_version IS NULL THEN
    RETURN v_name;
  END IF;
  RETURN v_name || '/' || v_version;
END;
$$ LANGUAGE plpgsql;
```

---

## 🧼 Cleanup Checklist

- [ ] Delete **Multi-AZ cluster**  
- [ ] Delete **read replica**  
- [ ] Delete **primary RDS** instance  
- [ ] Delete **manual snapshots** (if not needed)  
- [ ] Delete **event subscription**  
- [ ] Delete **CloudFormation stacks** (parent + nested)  

---

## 📚 Tech

**Amazon RDS for PostgreSQL**, **CloudFormation**, **CloudWatch (Metrics, Logs, Alarms, Database Insights)**, **Enhanced Monitoring**, **pgAdmin**, **psql**

---

## Author

**Bilal Yalcin** — Hands-on notes & screenshots from AWS Workshop Studio: *Amazon RDS for PostgreSQL*.
