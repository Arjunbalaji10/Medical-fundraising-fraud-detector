# Medical Fundraising System

## Overview

Medical Fundraising System is an engineering graduation project built as a Flask web application. It provides separate workflows for people requesting medical funding, donors, and administrators. Users can submit patient and treatment information with supporting documents, administrators can review submissions, and donors can view approved requests and record donations.

The project also contains experimental document-processing routines for extracting text, comparing uploaded files with reference records, performing OCR, and measuring image similarity. These routines depend on local reference documents and external software and should not be interpreted as a production medical-verification service.

## Problem statement

People seeking financial assistance for medical treatment often need a way to present treatment details, supporting bills, and funding requirements to potential donors. Donors, in turn, need a structured view of approved requests and a record of their contributions. This project explores a web-based workflow connecting these participants while allowing an administrator to review submitted cases.

## Project objectives

- Provide registration and sign-in workflows for fundraising users and donors.
- Allow users to enter patient, hospital, treatment, and requested-funding details.
- Accept supporting medical documents for a fundraising request.
- Provide an administrator-facing review workflow.
- Display approved fundraising requests to donors.
- Record donation amounts and transaction references.
- Experiment with OCR, file hashing, text comparison, and image similarity for supporting-document review.

## Existing features

- User, donor, and administrator sign-in pages
- User and donor registration
- Medical fundraising request creation
- Supporting-file upload and display
- Administrator views for users, donors, and verification
- Approval/rejection status handling for requests
- Donor view of approved fundraising posts
- Donation recording and funding-history views
- PDF and DOCX text extraction
- Tesseract-based OCR for images
- SHA-256 file hashing, text-difference highlighting, and structural image comparison

## Technology stack

- **Backend:** Python, Flask, Werkzeug
- **Database:** MySQL through MySQL Connector/Python
- **Frontend:** Jinja templates, HTML, CSS, JavaScript, Bootstrap, jQuery, Font Awesome
- **Image processing:** OpenCV, Pillow, ImageHash, scikit-image, NumPy
- **Document processing:** PyMuPDF, PyPDF2, python-docx, Spire.Doc, diff-match-patch
- **OCR:** pytesseract with a separately installed Tesseract OCR executable
- **Additional imported libraries:** pandas and Matplotlib

## System architecture

```text
Web browser
    |
    v
Flask routes and session handling (main.py)
    |-- Jinja templates (templates/)
    |-- CSS, JavaScript, fonts and images (static/)
    |-- document/OCR/image-processing routines
    `-- MySQL database
```

The current implementation is monolithic: routing, database access, session handling, uploads, and document-processing logic are all contained in `main.py`. Database configuration is also currently embedded in that file.

## Project structure

```text
medical_fundraising/
|-- main.py                 # Flask routes and application logic
|-- requirements.txt        # Python dependencies
|-- README.md               # Project documentation
|-- SETUP.md                # Local setup instructions
|-- .gitignore              # Repository exclusions
|-- database/               # Local database material (SQL dumps are ignored)
|-- sample/                 # Local document samples (contents are ignored)
|-- static/
|   |-- contactform/        # Theme contact-form JavaScript
|   |-- css/                # Public stylesheets
|   |-- fonts/              # Web fonts
|   |-- img/                # Public site artwork
|   |-- js/                 # Public JavaScript
|   |-- web/                # Dashboard theme assets
|   |-- data/               # Local reference documents (ignored)
|   |-- test/               # Runtime-generated comparison files (ignored)
|   `-- upload/             # User-uploaded documents (ignored)
`-- templates/
    |-- index and account templates
    `-- web/                 # User, donor, admin, and verification views
```

## Database overview

The application expects a MySQL database named `medical_fundraising`. Its current code refers to tables for administrators, users, donors, patient reference data, uploaded files, fundraising posts, donations, treatment types, and request statuses.

The original SQL export contains seeded records and must not be published because it may contain credentials and personal, medical, financial, or transaction information. Prepare a sanitized schema-only export before sharing or initializing the repository database. See [SETUP.md](SETUP.md) for the expected setup process and current limitations.

## Installation

Local installation, MySQL preparation, Tesseract configuration, and troubleshooting instructions are provided in [SETUP.md](SETUP.md).

## Project limitations

- This is an academic prototype, not a production fundraising, payment, identity-verification, or medical-record system.
- Configuration is hard-coded in the current application; environment variables are not yet supported by `main.py`.
- The supplied database export is not safe for public distribution and is ignored by Git.
- Uploaded and reference medical documents are local runtime data and are ignored by Git.
- Password storage and route authorization in the existing implementation are not production-grade.
- Donation entry records a transaction reference but does not integrate with a payment gateway.
- Document comparison and OCR results are experimental and do not establish authenticity.
- Tesseract is configured through a Windows-specific path in the source.
- Some object-detection code is incomplete and requires model files that are not included.
- There is no automated test suite or production deployment configuration.
- Some dashboard links and legacy theme templates may refer to unavailable assets or unused pages.

## Academic project disclaimer

This repository represents an engineering graduation project created for academic demonstration and learning. It must not be used to make medical, financial, identity, eligibility, or fraud determinations. Any deployment involving real users would require a security review, privacy and consent controls, protected document storage, credential management, legal compliance, and substantial production hardening.
