# CCRMS — Community Clinic Record Management System

A web-based electronic health record (EHR) system developed to replace paper-based patient file management at CCMDD (Central Chronic Medicines Dispensing and Distribution) community clinics in South Africa.

Built as a final-year IT Project (IT Project 700D) for the Bachelor of Science in Information Technology at Richfield Graduate Institute of Technology — awarded **Cum Laude**.

---

## Project Overview

Over 2 million South Africans collect chronic medication through community-based CCMDD pick-up points (PuPs). These volunteer-run clinics have historically relied entirely on paper-based record systems, leading to:

- Poor patient data security and confidentiality
- Lost, duplicated, and illegible patient files
- Long patient wait times due to manual file retrieval
- No backup or recovery mechanisms for patient records

**CCRMS** digitises the full patient management workflow — from registration and medical consultations to chronic medication collection tracking — through a secure, role-based web application backed by a MySQL database.

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | HTML5, CSS3 |
| Backend | PHP |
| Database | MySQL |
| Local Server | XAMPP (Apache + MySQL) |
| Security | MD5 password hashing, AES-128 log encryption, prepared statements |
| Testing | Manual functional, security, UI, and performance testing |

---

## Key Features

- **Role-based access control (RBAC)** — Three user privilege levels: Receptionist (Level 1), Medical Staff (Level 2), Administrator (Level 3)
- **Patient registration** — Create, search, update, and delete patient records with SA ID number validation (auto-extracts date of birth and gender)
- **Medical record management** — Record vital signs (blood pressure, pulse, respiratory rate, blood sugar) and medical notes per visit
- **Chronic medication collection tracking** — Track CCMDD medication collection dates with 3-week grace period compliance
- **Audit logging** — All system actions are logged with timestamps and user IDs; logs are AES-128 encrypted
- **Database backup and restore** — Administrator-initiated `mysqldump` backups with scheduled automation support
- **Input validation and SQL injection protection** — All inputs sanitised and executed via prepared statements; XSS defence implemented
- **Multi-browser support** — Tested on Google Chrome and Mozilla Firefox

---

## System Architecture

```
Client (Browser)
      │
      ▼
  Apache (XAMPP)
      │
   PHP Scripts ──── Session Management ──── RBAC Enforcement
      │
  MySQL Database
      │
  ┌───────────────────────────────────────┐
  │  Tables: Patient · Address · Employee │
  │  Medical_Record · Collection · Visit  │
  │  User_Log                             │
  └───────────────────────────────────────┘
```

---

## Database Schema (Summary)

| Table | Primary Key | Description |
|-------|-------------|-------------|
| `patient` | `patient_id` (auto) | Patient demographics, SA ID number, contact details |
| `address` | `address_id` (auto) | Patient address linked to patient record |
| `employee` | `emp_id` (auto) | Staff accounts with access level (1/2/3) |
| `medical_record` | `med_id` (auto) | Vital signs and medical notes per visit |
| `collection` | `collect_id` (auto) | Chronic medication collection dates |
| `visit` | `visit_id` (auto) | Patient visit log with reason (PH/MD/BT) |
| `user_log` | `log_id` (auto) | Full audit trail of all system actions |

---

## Installation

### Prerequisites

- Windows 10 (recommended)
- [XAMPP](https://www.apachefriends.org/) with Apache and MySQL modules
- A web browser (Chrome or Firefox recommended)
- A configured LAN (for multi-client clinic deployment)

### Steps

1. **Download and install XAMPP** — select Apache and MySQL during installation. Install to `C:\xampp`.

2. **Start Apache and MySQL** from the XAMPP Control Panel. Confirm by navigating to `localhost` in your browser — the XAMPP welcome page should appear.
   > If MySQL port 3306 is in conflict with an existing MySQL installation, see [Port Conflict Resolution](#port-conflict-resolution) below.

3. **Copy the project** — place the `CCRMS` folder into `C:\xampp\htdocs\`.

4. **Run setup** — open a browser and navigate to:
   ```
   localhost/CCRMS/setup.php
   ```
   Enter your administrator username and password (minimum 8 characters, at least one uppercase, one lowercase, one number). This creates the database and initial admin account.

5. **Secure the database** — open phpMyAdmin from the XAMPP Control Panel. Navigate to **User Accounts** and change the password for all three `root` accounts to a strong password (10+ alphanumeric characters, mixed case). **Save this password** — it is required to log in to the system.

6. **Access the system** — navigate to:
   ```
   localhost/CCRMS/
   ```
   - **Index login**: hostname = `localhost`, username = `root`, password = *(the root password you set)*
   - **System login**: username and password = *(the admin credentials from the setup step)*

7. **Add clinic staff** — log in as Administrator, go to **User Accounts**, and create accounts for all clinic staff. Assign the correct access level per role.

8. **Clinic staff access** — staff connect to the same LAN and access the system using the server's IP address:
   ```
   http://<server-ip>/CCRMS/
   ```

### Port Conflict Resolution

If XAMPP reports MySQL port 3306 is occupied:

1. Stop the existing MySQL service in Task Manager
2. Navigate to `C:\ProgramData\MySQL\MySQL Server 8.xx\` (enable hidden items in Explorer)
3. Open `my.ini` and change both `port=3306` entries to a free port (e.g. `3307`)
4. Save as a new file, delete the original `my.ini`, rename your new file to `my.ini`
5. Restart the MySQL service — the conflict should be resolved

---

## Usage

### Access Levels

| Role | Access Level | Permissions |
|------|-------------|-------------|
| Receptionist | Level 1 | Create/search/update patient records, log visits |
| Medical Staff | Level 2 | All Level 1 + add/view medical records and vital signs |
| Administrator | Level 3 | All Level 2 + manage users, delete records, generate reports, backup/restore database |

### Navigating the System

1. Log in at the index page with database credentials
2. Log in at the system login page with your staff credentials
3. From the **Dashboard**, select your function (Patient Files, User Accounts, Reports, Backup)
4. Use **Search** to find patients by name, ID number, or date of birth before updating or deleting
5. Use **Logout** button and confirm to end your session securely

---

## Testing Summary

Testing was performed across 13 categories:

| Test Category | Result |
|---------------|--------|
| User Authentication & Authorisation | ✅ Pass — invalid credentials blocked; role-based redirects enforced |
| Patient Registration | ✅ Pass — mandatory fields validated; duplicate ID numbers rejected |
| Record CRUD Operations | ✅ Pass — create, update, delete all functional with confirmation prompts |
| Search & Retrieval | ✅ Pass — partial-term search works across all searchable fields |
| Security (SQL Injection / XSS) | ✅ Pass — all attack vectors defended via sanitisation and prepared statements |
| User Interface | ✅ Pass — tested on Chrome and Firefox; all buttons and forms functional |
| Error Handling | ✅ Pass — invalid inputs produce clear error messages; no crashes observed |
| Performance (10 concurrent clients) | ✅ Pass — no degradation in response times under multi-client load |
| Backup & Recovery | ✅ Pass — database backup and restore verified |
| Session Management | ✅ Pass — back-navigation after logout redirects to index; no session leakage |

---

## Team

| Name | Student Number |
|------|---------------|
| Kashveer Ramgulam | 401801498 |
| **Saien Chettiar** | **402105926** |
| Sabina Lucy | 402006140 |
| Sheldon Martins | 402106383 |
| Njabulo Khohliso | 402203166 |
| Jenita Govender | 402101228 |

**Institution:** Richfield Graduate Institute of Technology  
**Module:** IT Project 700D — Bachelor of Science in Information Technology (Year 3)  
**Year:** 2023  
**Result:** Distinction (85%) — Graduated Cum Laude

---

## Acknowledgements

This project was developed in response to real-world challenges observed at a PSVDS community clinic operating as a CCMDD pick-up point, serving approximately 300 patients weekly. The solution was designed specifically for volunteer-run, resource-constrained clinic environments where ease of use and minimal setup cost are critical requirements.

---

## Licence

This project was developed as open-source software for use by non-profit community CCMDD clinics. It is free to use, modify, and distribute for non-commercial healthcare purposes.
