# Automated ETL Status Pipeline

## Overview
This Python-based ETL pipeline extracts issue data from GitHub and mock CRM data from Salesforce, normalizes the structures, and loads the data into a Google Sheets relational database. It then automatically exports a PDF snapshot of the weekly summary and delivers it to a target executive via an SMTP email payload.

## 1. Installation & Environment Setup
Clone this repository to your local machine or server. Ensure you have Python 3.8+ installed.

Install the required dependencies:
`pip install gspread requests python-dotenv`

## 2. Authentication & Secrets Store (Crucial)
This script uses a decentralized secrets approach to ensure no credentials are hardcoded into the executable. 

**A. Google Service Account (`credentials.json`)**
1. Create a Google Cloud Service Account with Google Sheets API access.
2. Generate a JSON key and save it directly in the project root directory as `credentials.json`.
3. Share your target Google Sheet with the `client_email` found inside that JSON file to grant Editor permissions.

**B. Environment Variables (`.env`)**
Create a hidden `.env` file in the root directory. This acts as the secure secrets store for the SMTP delivery. Populate it with the following keys:
SENDER_EMAIL="your_email@gmail.com"
SENDER_APP_PASSWORD="your_16_character_app_password"
RECEIVER_EMAIL="target_executive@company.com"

## 3. Token Refresh Guidance
* **OAuth (Google):** Token refresh operations for the Google Sheets API are handled entirely under the hood by the `gspread` client using the static Service Account JSON. No manual OAuth token rotation is required for standard operation.
* **SMTP (Gmail):** The pipeline uses a static 16-character App Password. If the pipeline fails with an `smtplib.SMTPAuthenticationError`, the App Password has likely been manually revoked by the user or flagged by Google. **Resolution:** Generate a new App Password in the Google Account Security portal and update the `SENDER_APP_PASSWORD` in the `.env` file.

## 4. Scheduled Runner Handoff (Deployment)
This script is designed for low-friction execution on standard cloud VMs or local scheduled task runners.

**For Linux/macOS Environments:**
Make the script executable using `chmod`, then open the crontab using a text editor like `nano`:
`chmod +x main.py`
`nano ~/crontab`

Add the following cron expression to run the pipeline automatically every Friday at 4:00 PM:
`0 16 * * 5 /usr/bin/python3 /absolute/path/to/status_automator/main.py`

**For Windows Environments:**
Create a Basic Task in Windows Task Scheduler, or use the `Register-ScheduledTask` cmdlet in PowerShell to trigger the Python executable against the `main.py` file path on a weekly trigger.