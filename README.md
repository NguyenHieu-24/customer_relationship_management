<h1 align="center">Customer Relationship Management</h1>
<p align="center">
  A Django web app for managing customer records with a MySQL database.
</p>
<p align="center">
  <img src="https://img.shields.io/badge/Language-Python-3776AB?style=flat-square" alt="Language: Python">
  <img src="https://img.shields.io/badge/Framework-Django-092E20?style=flat-square" alt="Framework: Django">
  <img src="https://img.shields.io/badge/Database-MySQL-4479A1?style=flat-square" alt="Database: MySQL">
  <img src="https://img.shields.io/badge/Interface-Bootstrap-7952B3?style=flat-square" alt="Interface: Bootstrap">
  <img src="https://img.shields.io/badge/Status-Educational%20Prototype-F2C94C?style=flat-square" alt="Status: Educational prototype">
</p>
<p align="center">
  <a href="#overview">Overview</a> ·
  <a href="#features">Features</a> ·
  <a href="#quick-start">Quick Start</a> ·
  <a href="#how-to-use">How to Use</a> ·
  <a href="#architecture">Architecture</a> ·
  <a href="#known-issues">Known Issues</a>
</p>

---

# Overview
This course project provides a browser interface to store customer contact details. Registered users can sign in, view the record list, open individual profiles, and add, update, or delete customer records. Django's ORM manages a `Record` model backed by **MySQL**.
> **Scope:** The supplied code manages customer profiles. It does not implement interaction tracking, transactions, marketing campaigns, sales analytics, or search filters described in the previous README.

[Watch the included demo video](demo.mp4).

---

# Features
| | Feature | Implementation |
| :---: | --- | --- |
| 👤 | Accounts | Registration, login, and logout using Django authentication |
| 📋 | Customer list | Table of saved customers for signed in users |
| ➕ | Add record | Django model form for contact information |
| 🔎 | View profile | Detail page selected by record ID |
| ✏️ | Update record | Prefilled form for editing a customer |
| 🗑️ | Delete record | Delete link on the detail page |
| 🎨 | Interface | Bootstrap templates, animated assets, and a world clock panel |

---

# Quick Start
## 1. Prepare your environment
- Install **Python 3.10 or newer**, **MySQL**, and optionally VS Code.
- Ensure MySQL is running and you have a database account permitted to create a database and apply migrations.
- Download or clone the repository. Run commands from its root, alongside `manage.py`.

The archive has no `requirements.txt` or lockfile. The following environment uses **Django 5.2** and **mysqlclient** as a reproducible setup starting point; this combination has not been tested with the supplied project in this review.

### Windows PowerShell
```powershell
py -3.10 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install "Django>=5.2,<5.3" mysqlclient
```

If your installed Python has a different supported version, replace `-3.10` accordingly.

### macOS / Linux
```sh
python3 -m venv .venv
source .venv/bin/activate
python -m pip install 'Django>=5.2,<5.3' mysqlclient
```

The project setting uses Django's MySQL backend. Its standalone `mydb.py` script imports `mysql.connector`, but that script is **not needed** for the startup steps below.

## 2. Create the MySQL database
In MySQL Workbench or a MySQL shell, run:

```sql
CREATE DATABASE CRM CHARACTER SET utf8mb4;
```

If the database already exists, use it rather than creating another. You do not need to create the customer table by hand; the included Django migration defines it.

## 3. Configure the database connection
Edit `dcrm/settings.py` → `DATABASES['default']`. Keep `ENGINE` as `django.db.backends.mysql` and set `NAME`, `USER`, `PASSWORD`, `HOST`, and `PORT` for **your own local MySQL account**. The archive contains hardcoded credentials in `dcrm/settings.py` and `mydb.py`; remove them from the repository and change those credentials wherever else they are used before sharing the code.

Also keep the Django secret key out of a public repository. This setup is for local development; `DEBUG = True` is present in the supplied settings.

## 4. Apply migrations and start the app
```sh
python manage.py migrate
python manage.py runserver
```

Open [http://127.0.0.1:8000/](http://127.0.0.1:8000/). Register an account using the navigation link, then sign in to view and edit records.

<details>
<summary><strong>Using Visual Studio Code</strong></summary>
<ol>
<li>Open the folder containing <code>`manage.py`</code>.</li>
<li>Create and activate the virtual environment using the commands above.</li>
<li>Select the <code>`.venv`</code> Python interpreter in VS Code.</li>
<li>Configure MySQL, run <code>`python manage.py migrate`</code>, and then run <code>`python manage.py runserver`</code> in the integrated terminal.</li>
</ol>
</details>

<details>
<summary><strong>Troubleshooting</strong></summary>

| Symptom | Check |
| --- | --- |
| `No module named django` | Activate `.venv` and install the packages in Step 1. |
| `Error loading MySQLdb module` | Install `mysqlclient` into the active environment. |
| `Access denied for user` | Verify the MySQL account and values in `dcrm/settings.py`. |
| `Unknown database 'CRM'` | Create `CRM` in MySQL before running migrations. |
| `Table 'CRM.website_record' doesn't exist` | Run `python manage.py migrate`. |
| Page is not available | Keep `runserver` running and use `http://127.0.0.1:8000/`. |

**Validation:** These instructions were checked against the supplied settings, model, migration, views, and routes. Django and MySQL were unavailable in the review environment, so installation and interactive behavior were not verified here.

</details>

---

# How to Use
| Step | Action |
| --- | --- |
| **1** | Open the home page and select **Register**, or sign in with an existing account. |
| **2** | View customer records in the home-page table. |
| **3** | Select **Add Record** to enter a customer's details. |
| **4** | Select a record ID to view its profile, then choose **Update Record** or **Delete**. |
| **5** | Use **Logout** when finished. |

The site shows a world-clock panel alongside the table. Its displayed city offsets are fixed in the page script and do not automatically handle daylight-saving changes.

---

# Architecture
| File or folder | Responsibility |
| --- | --- |
| `manage.py` | Django management entry point |
| `dcrm/settings.py` | Installed apps, MySQL connection, static settings, and project configuration |
| `dcrm/urls.py`, `website/urls.py` | Root and CRM URL routing |
| `website/models.py` | `Record` customer data model |
| `website/migrations/` | Schema migration for the customer table |
| `website/forms.py` | Registration and customer record forms |
| `website/views.py` | Authentication and customer record actions |
| `website/templates/` | Login, registration, list, detail, add, and update pages |
| `static/images/` | Interface images and animations |

<details>
<summary><strong>Customer model and routes</strong></summary>

| Field group | Stored attributes |
| --- | --- |
| Identity | `first_name`, `last_name` |
| Contact | `email`, `phone`, `address`, `city`, `state`, `zipcode` |
| Metadata | Auto-generated `id` and `created_at` |

| Route | Purpose |
| --- | --- |
| `/` | Login page for guests; record table for signed-in users |
| `/register/` | Create an account |
| `/add_record/` | Add a customer |
| `/record/<id>` | View a customer |
| `/update_record/<id>` | Edit a customer |
| `/delete_record/<id>` | Delete a customer |
| `/logout/` | Sign out |

The record model has no relationship to the user account. All authenticated users interact with the same customer collection in the supplied views.

</details>

---

# Project Files
| Path | Contents |
| --- | --- |
| `README.md` | Project documentation |
| `Group6_Demo.mp4` | Included project demonstration |
| `dcrm/` | Django project settings and application entry points |
| `website/` | CRM app, templates, migrations, and forms |
| `static/` | Images and animations |
| `mydb.py` | Standalone database-creation script with local credentials; not part of standard startup |

---

# Known Issues
**Educational prototype:** the current authentication checks whether a user is signed in, but records are not assigned to specific accounts.

<details>
<summary><strong>View source-review findings</strong></summary>

| Area | Finding |
| --- | --- |
| Credential handling | Settings and a helper script contain hardcoded secrets; use private configuration before sharing or deployment. |
| Authorization | Any signed-in user can view, edit, and delete any customer record. |
| Delete behavior | The delete action is reached through a link and has no confirmation or POST-only check. |
| Data validation | The model stores `email` in a plain `CharField`; it has no uniqueness constraint. |
| Search and reports | No customer search, marketing campaign, interaction history, or analytics implementation is present. |
| Dependencies | No dependency manifest is included; Python bytecode files were committed to the archive. |
| Deployment | `DEBUG = True` and empty `ALLOWED_HOSTS` are configured for local development. |
| Tests | `website/tests.py` contains no test cases. |

</details>

---

# Roadmap
- Move credentials and the Django secret key into private configuration.
- Add per-record permissions and require POST confirmation for deletion.
- Add email validation, search, and useful customer filters.
- Define and pin project dependencies.
- Add model, view, and permission tests.
- Document a separate deployment configuration if hosting the app.

---

# Team
| Contributor | Role |
| --- | --- |
| Nguyen Hieu Nguyen | Backend Developer |
| Hoang Thanh Ngo | Backend Developer |
| Ngoc Sang Nguyen | Frontend Developer |
| Thi Hau Le | Frontend Developer |
| Mai Anh Nam Nguyen | Database Designer |

# Contributing
Open an issue or submit a focused pull request. Include setup details, reproduction steps, and how you checked the change. Remove real customer data and secrets from examples.

# License
The supplied archive has no `LICENSE` file. Maintainers should document the intended terms before others reuse or redistribute the project.

---

<p align="center"><a href="#customer-relationship-management">Back to top ↑</a></p>
