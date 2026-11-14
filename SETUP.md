# Local Setup Guide

This guide describes how to run the existing academic project locally on Windows. It does not alter or modernize the application. Do not use real patient, identity, bank, or payment information in a development database.

## 1. Prerequisites

Install the following software:

- Python 3.10 or 3.11 (64-bit recommended)
- MySQL Server 8.x or a compatible local MySQL/MariaDB installation
- MySQL command-line client or MySQL Workbench
- Tesseract OCR for Windows

The application also imports Spire.Doc. Review that package's license before distributing or deploying the project.

Confirm Python is available:

```powershell
py --version
```

## 2. Create and activate a virtual environment

From the project root in PowerShell:

```powershell
py -3.11 -m venv .venv
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

If Python 3.11 is not installed through the Python launcher, use an installed compatible version instead.

## 3. Install Python dependencies

With the virtual environment active:

```powershell
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Tesseract and MySQL Server are external programs and are not installed by `requirements.txt`.

## 4. Configure MySQL

The existing application expects:

- A MySQL server on `localhost`
- A database named `medical_fundraising`
- Application tables matching the queries in `main.py`
- A local MySQL account with permission to read and update that database

### Database initialization safety

The original project export contains seeded data and may include credentials and personal, medical, financial, identity, or transaction information. It is intentionally excluded by `.gitignore` and must not be committed or shared.

No sanitized schema-only file is currently available in this repository. Before initialization, an authorized project maintainer should create a sanitized schema-only export that:

- contains table definitions only;
- contains no `INSERT` statements or real records;
- contains no account passwords or credentials; and
- has been reviewed for personal, medical, financial, and transaction information.

After such a file has been prepared outside the public repository, initialize a local development database using MySQL Workbench or the MySQL client. For example:

```powershell
mysql -u YOUR_LOCAL_MYSQL_USER -p -e "CREATE DATABASE medical_fundraising;"
mysql -u YOUR_LOCAL_MYSQL_USER -p medical_fundraising < PATH_TO_SANITIZED_SCHEMA.sql
```

These commands are examples only. Do not run them against a production database, and do not substitute the original seeded dump unless it has first been sanitized.

## 5. Configure environment variables

The application requires the following environment variables:

| Variable | Purpose |
|---|---|
| `MYSQL_HOST` | MySQL server hostname, such as a local server |
| `MYSQL_USER` | MySQL application account name |
| `MYSQL_PASSWORD` | Password for the MySQL application account |
| `MYSQL_DATABASE` | Name of the initialized application database |
| `FLASK_SECRET_KEY` | Private random value used to sign Flask sessions |

Set them for the current PowerShell session before starting the application. Replace every placeholder with a local value and do not commit or share the resulting secrets:

```powershell
$env:MYSQL_HOST = "YOUR_MYSQL_HOST"
$env:MYSQL_USER = "YOUR_MYSQL_USER"
$env:MYSQL_PASSWORD = "YOUR_MYSQL_PASSWORD"
$env:MYSQL_DATABASE = "YOUR_DATABASE_NAME"
$env:FLASK_SECRET_KEY = "YOUR_PRIVATE_RANDOM_SECRET"
```

These assignments last only for the current PowerShell process. Start the application from the same terminal session. A `.env` file is ignored for safety but is not loaded automatically by the existing application.

If any required variable is absent, Python reports the missing variable when `main.py` starts. The application establishes its MySQL connection during module import, so MySQL must already be running and reachable.

## 6. Configure Tesseract OCR

Install Tesseract OCR for Windows and confirm the executable exists at:

```text
C:\Program Files\Tesseract-OCR\tesseract.exe
```

The current source explicitly uses that location. Installing Tesseract elsewhere will cause OCR operations to fail unless the source is changed; source changes are outside the scope of this setup document.

Confirm the installation from PowerShell:

```powershell
& "C:\Program Files\Tesseract-OCR\tesseract.exe" --version
```

## 7. Prepare local runtime directories and data

The source tree already includes these directories:

```text
static/upload/
static/test/
static/data/
```

Their contents are ignored because uploads, generated comparison images, and reference medical documents must not be published. Keep the directories available locally when running the application.

Some verification workflows expect reference records in the MySQL tables and matching reference documents under `static/data/`. Use only synthetic, non-sensitive development fixtures. The application may open successfully without complete fixtures, but document-comparison workflows will not be fully testable.

The optional object-detection section of the source expects model configuration, weight, and class files that are not included. That experimental function cannot be considered configured by this guide.

## 8. Run the Flask application

Ensure MySQL is running, activate the virtual environment, set all variables from section 5 in the current PowerShell session, and then run:

```powershell
python main.py
```

The current source starts Flask on port 5000. Open:

```text
http://127.0.0.1:5000/
```

The existing entry point enables Flask debug mode and listens on all interfaces. Use it only in a trusted local development environment. It is not a production server configuration.

## 9. Troubleshooting

### `ModuleNotFoundError`

Verify that the virtual environment is active and reinstall the declared dependencies:

```powershell
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Import names and package names sometimes differ: `cv2` comes from `opencv-python`, `PIL` from `Pillow`, `fitz` from `PyMuPDF`, `docx` from `python-docx`, and `mysql.connector` from `mysql-connector-python`.

### MySQL connection errors

- Confirm MySQL Server is running.
- Confirm the `medical_fundraising` database exists.
- Confirm `MYSQL_HOST`, `MYSQL_USER`, `MYSQL_PASSWORD`, and `MYSQL_DATABASE` are set in the current PowerShell session.
- Confirm the configured local account can connect to the configured database.
- Confirm that account has access to the database.
- Confirm the required tables were created from a sanitized schema.

Do not solve connection failures by committing credentials to the repository.

### Missing-table errors

The database exists but does not contain the schema expected by `main.py`. Obtain a reviewed schema-only export from the project maintainer. Do not use or publish an unsanitized seeded dump.

### Tesseract errors

- Confirm Tesseract is installed at the exact path shown above.
- Confirm the executable runs from PowerShell.
- Restart the application after installing Tesseract.

### OpenCV or NumPy installation errors

Use a supported 64-bit CPython release and an up-to-date `pip`. Recreate the virtual environment with Python 3.10 or 3.11 if binary wheels are unavailable for the selected interpreter.

### Spire.Doc errors

Confirm `Spire.Doc` was installed from `requirements.txt`. Consult its vendor documentation for platform requirements and licensing. Document conversion features may remain unavailable if the package cannot initialize in the local environment.

### Upload or comparison errors

- Confirm `static/upload`, `static/test`, and `static/data` exist and are writable locally.
- Use synthetic files in supported formats.
- Ensure matching sanitized reference rows and reference files exist for workflows that compare uploads.

### Session or authentication behavior

The current authentication, authorization, password storage, and logout behavior are prototype limitations. Do not expose the development server to untrusted users or use real credentials.
