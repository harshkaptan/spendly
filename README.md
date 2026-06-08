# 💸 Spendly — Personal Expense Tracker

A lightweight, full-stack **personal expense tracker** built with **Python (Flask)** and **SQLite**. Spendly lets users register, log in, and manage their day-to-day spending with a clean dashboard, category-wise breakdowns, and date-range filtering.

> Built as a portfolio project to demonstrate practical **backend engineering, secure authentication, relational data modeling, and clean full-stack architecture** — the core skills expected of a Python / Backend / Full-Stack Developer.

---

## 📋 Table of Contents
- [Overview](#-overview)
- [Key Features](#-key-features)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [Usage](#-usage)
- [Database Schema](#-database-schema)
- [Testing](#-testing)
- [Engineering Highlights](#-engineering-highlights-for-reviewers)
- [Future Improvements](#-future-improvements)

---

## 🧭 Overview

Spendly is a server-rendered web application where each user gets a private dashboard to record and analyze their expenses. It focuses on **correctness, security, and clean code** rather than heavy frameworks — every layer (routing, data access, templates) is intentionally simple and readable.

A demo account is seeded automatically on first run so you can explore the app immediately:

| Field | Value |
|---|---|
| Email | `demo@spendly.com` |
| Password | `demo123` |

---

## ✨ Key Features

- **User Authentication** — register, log in, and log out with **hashed passwords** (PBKDF2-SHA256) and session management.
- **Expense Management (Full CRUD)** — add, edit, and delete expenses with server-side validation.
- **Dashboard & Summary Stats** — total spend, transaction count, and top spending category at a glance.
- **Category Breakdown** — per-category totals with percentage distribution.
- **Date-Range Filtering** — filter by custom dates or quick presets (*This Month*, *Last 3 Months*, *Last 6 Months*).
- **Per-User Data Isolation** — every query is scoped to the logged-in user, so users can only ever see and modify their own data.
- **Input Validation** — positive-amount checks, allowed-category enforcement, and date validation on every form.

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| **Language** | Python 3.10+ (works on 3.9) |
| **Web Framework** | Flask 3.x |
| **Database** | SQLite 3 (via the standard `sqlite3` module — no ORM) |
| **Auth / Security** | Werkzeug password hashing & session handling |
| **Templating** | Jinja2 (server-side rendered HTML) |
| **Frontend** | HTML5, CSS3, **Vanilla JavaScript** (no frameworks) |
| **Testing** | Pytest + pytest-flask |

> **Design choice:** No ORM, no JS framework, no extra dependencies. This keeps the codebase transparent and demonstrates a clear understanding of what each layer actually does — raw parameterized SQL, manual session handling, and hand-written routes.

---

## 📁 Project Structure

```
spendly/
├── app.py                  # All Flask routes (single-file, no blueprints)
├── database/
│   ├── db.py               # Connection, schema init, seeding, user creation
│   └── queries.py          # All expense/analytics SQL queries
├── templates/              # Jinja2 templates (all extend base.html)
│   ├── base.html
│   ├── landing.html
│   ├── register.html
│   ├── login.html
│   ├── profile.html        # Main dashboard
│   ├── add_expense.html
│   ├── edit_expense.html
│   └── ...
├── static/
│   ├── css/                # Page-scoped stylesheets
│   └── js/main.js          # Vanilla JS only
├── tests/                  # Pytest test suite
├── requirements.txt
└── README.md
```

**Separation of concerns:**
- Routes live only in `app.py` — each handles one responsibility (fetch → validate → render).
- **All database logic** is isolated in `database/` — never inline in routes.
- Every SQL query uses **parameterized placeholders** (`?`) to prevent SQL injection.

---

## 🚀 Getting Started

### Prerequisites
- **Python 3.10+** (3.9 also works)
- `pip` and `venv` (bundled with Python)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/spendly.git
cd spendly

# 2. Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate          # On Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the development server
python app.py
```

The database (`spendly.db`) is **created and seeded automatically** on first launch — no manual setup needed.

Now open your browser to:

```
http://localhost:5001
```

> ⚠️ Spendly runs on **port 5001** (not the Flask default 5000).

---

## 🖥 Usage

1. Open `http://localhost:5001`.
2. **Sign in** with the demo account (`demo@spendly.com` / `demo123`) — or register a new account.
3. View your **dashboard**: totals, top category, and recent transactions.
4. **Add / edit / delete** expenses and watch the stats update.
5. Use the **date filters** to analyze spending over different periods.

---

## 🗄 Database Schema

```sql
users
├── id              INTEGER  PRIMARY KEY AUTOINCREMENT
├── name            TEXT     NOT NULL
├── email           TEXT     UNIQUE NOT NULL
├── password_hash   TEXT     NOT NULL          -- PBKDF2-SHA256, never plaintext
└── created_at      TEXT     DEFAULT datetime('now')

expenses
├── id              INTEGER  PRIMARY KEY AUTOINCREMENT
├── user_id         INTEGER  NOT NULL  → REFERENCES users(id)
├── amount          REAL     NOT NULL
├── category        TEXT     NOT NULL
├── date            TEXT     NOT NULL
├── description     TEXT
└── created_at      TEXT     DEFAULT datetime('now')
```

Foreign-key enforcement is enabled explicitly (`PRAGMA foreign_keys = ON`) on every connection, since SQLite has it off by default.

---

## 🧪 Testing

The project uses **Pytest** for automated testing of routes and core logic.

```bash
# Run the full test suite
pytest

# Run a specific test file
pytest tests/test_07-add-expense.py

# Run tests with print output visible
pytest -s
```

---

## 🔍 Engineering Highlights (for reviewers)

This project intentionally demonstrates several practices interviewers look for:

- 🔐 **Secure auth** — passwords hashed with PBKDF2-SHA256; sessions used for state; no credentials stored or logged in plaintext.
- 🛡 **SQL-injection safe** — 100% parameterized queries, zero string-formatted SQL.
- 👤 **Authorization, not just authentication** — every expense query is scoped by `user_id`, so users cannot access or mutate other users' records (verified via `abort(404)` on mismatch).
- 🧱 **Clean architecture** — strict separation between routing, data access, and presentation layers.
- ✅ **Defensive validation** — all user input is validated server-side before touching the database.
- 🧪 **Tested** — automated Pytest coverage for key flows.
- 📦 **Zero-config setup** — DB auto-initializes and seeds on first run for a smooth reviewer experience.

---

## 🔮 Future Improvements

- Move the hardcoded `secret_key` to an environment variable / `.env` file.
- Add charts (e.g., Chart.js) to the analytics page for visual spending trends.
- Add CSV export / import of expenses.
- Add pagination for large transaction histories.
- Containerize with Docker for one-command deployment.
- Add CI (GitHub Actions) to run the test suite on every push.

---

## 👤 Author

**Harsh Kaptan**
Built as a hands-on portfolio project to demonstrate full-stack Python development skills.

---

> 📝 *This is a learning / portfolio project. The development server and default secret key are not intended for production use without the hardening noted in [Future Improvements](#-future-improvements).*
