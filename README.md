# Payroll Project

A small **employee payroll and leave-management** web app built with **Python / Flask**
and **MySQL** (via Flask-SQLAlchemy). It provides two roles — **Admin** and
**Employee** — each with its own dashboard, covering payroll records, leave requests,
and employee listings.

> A compact, single-file Flask application intended as a learning/demo project.

## Features

Derived directly from the routes in [`main.py`](main.py):

**Admin**
- Log in and land on an admin dashboard.
- **Manage leave requests** — view all requests and approve/reject them.
- **Manage payroll** — view all payroll records and delete records.
- **View employees** — list all employees.

**Employee**
- Log in (email as username, phone number as password) and land on an employee dashboard.
- **View payroll** — see their own payroll records (basic salary, bonuses, deductions, net salary).
- **Submit leave requests** — choose a leave type and start/end dates.
- **Download payslip** — download a payslip PDF for a payroll record (authorized to their own records).

Shared: session-based login/logout with role routing, and flash messages for feedback.

## Tech stack

- **Backend:** Python, Flask, Flask-SQLAlchemy (SQLAlchemy ORM)
- **Database:** MySQL (via PyMySQL)
- **Frontend:** Jinja2 server-rendered templates + CSS (`static/style.css`)
- **Sessions:** Flask server-side session with a `SECRET_KEY`

See [`requirements.txt`](requirements.txt) for pinned versions (Flask 1.1.2,
Flask-SQLAlchemy 2.5.1, SQLAlchemy 1.4.x, PyMySQL 1.0.2).

## Data model

The Flask app defines four models in [`main.py`](main.py):

- **Admin** — `admin_id`, `username`, `password`
- **Employee** — `id`, `first_name`, `last_name`, `email`, `phone`, `position`
- **LeaveRequest** — `leave_id`, `employee_id` → Employee, `leave_type`, `start_date`, `end_date`, `status` (default `Pending`)
- **Payroll** — `payroll_id`, `employee_id` → Employee, `basic_salary`, `bonuses`, `deductions`, `net_salary`

[`enhanced_schema.sql`](enhanced_schema.sql) additionally defines and seeds
`Department`, `Employee_Department`, `Attendance`, `Project`, and `Employee_Project`
tables. These exist in the schema for a fuller HR data model but are **not yet wired up**
to routes or views in the current app.

## Project structure

```
Payroll-Project/
├─ main.py                  Flask app: config, models, and all routes
├─ enhanced_schema.sql      MySQL schema + sample seed data
├─ requirements.txt         Python dependencies
├─ templates/               Jinja2 templates
│  ├─ login.html
│  ├─ admin_dashboard.html      / employee_dashboard.html
│  ├─ manage_leaves.html        / leave_request.html
│  ├─ manage_payroll.html       / payroll.html
│  └─ view_employees.html
└─ static/
   ├─ style.css
   └─ images/main-bg.jpg
```

### Routes

| Route | Method | Role | Purpose |
|-------|--------|------|---------|
| `/` , `/login` | GET/POST | public | Login (admin or employee) |
| `/logout` | GET | any | Clear session |
| `/admin_dashboard` | GET | Admin | Admin home |
| `/employee_dashboard` | GET | Employee | Employee home |
| `/manage_leaves` | GET/POST | Admin | Approve/reject leave requests |
| `/manage_payroll` | GET/POST | Admin | View / delete payroll records |
| `/view_employees` | GET | Admin | List employees |
| `/payroll` | GET | Employee | View own payroll |
| `/leave_request` | GET/POST | Employee | Submit a leave request |
| `/download_payslip/<payroll_id>` | GET | logged-in | Download a payslip PDF |

## Local setup

**Prerequisites:** Python 3, a MySQL server, and `pip`.

1. **Clone**
   ```bash
   git clone https://github.com/Shannu1922/Payroll-Project.git
   cd Payroll-Project
   ```

2. **Create a virtual environment and install dependencies**
   ```bash
   python -m venv .venv
   # Windows: .venv\Scripts\activate   |   macOS/Linux: source .venv/bin/activate
   pip install -r requirements.txt
   ```

3. **Create the database** (this drops and recreates a `payroll` database and seeds
   sample data):
   ```bash
   mysql -u root -p < enhanced_schema.sql
   ```

4. **Configure the database connection.** The connection string and `SECRET_KEY` are set
   near the top of [`main.py`](main.py) (`SQLALCHEMY_DATABASE_URI`). Update the MySQL
   user/password/host to match your environment before running.

5. **Run**
   ```bash
   python main.py
   ```
   The app starts on Flask's default dev server at <http://127.0.0.1:5000>.

**Sample logins** (from the seed data in `enhanced_schema.sql`):
- **Admin** — username `Shannu`, password `Shannu1922`
- **Employee** — username = an employee email (e.g. `jane.smith@example.com`),
  password = that employee's phone number.

## Notes

This is a demo-scale project: passwords are stored and compared in plain text, the
employee login uses phone-as-password, and DB credentials/`SECRET_KEY` are hardcoded in
`main.py`. Treat it as a learning artifact rather than a production-ready payroll system —
do not deploy as-is or use real personal/salary data.

## License

Private / proprietary. All rights reserved.
