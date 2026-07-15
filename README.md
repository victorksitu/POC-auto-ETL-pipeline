# POC Auto-ETL Pipeline

A proof-of-concept ETL pipeline that extracts records from the GitHub Issues API and mock CRM data, normalizes them with pandas, loads the results into Google Sheets, exports a weekly PDF summary, and sends the report through email.

## Features

- Extracts live issue data from the GitHub REST API
- Simulates CRM opportunity data with mock Salesforce-style records
- Uses pandas to normalize fields, clean text values, merge sources, sort rows, and remove duplicates
- Loads transformed records into a Google Sheets worksheet
- Appends a weekly summary row for reporting
- Exports the summary sheet as a PDF
- Sends the PDF report through SMTP using environment-based configuration

## Tech Stack

```text
Python
pandas
requests
gspread
Google Sheets API
SMTP
python-dotenv
```

## Pipeline Flow

```text
GitHub Issues API
        \
         -> pandas normalization -> Google Sheets -> PDF export -> email report
        /
Mock CRM records
```

## Setup

Install dependencies:

```bash
pip install -r requirements.txt
```

Create a Google service account with Google Sheets API access. Download the service account JSON key and save it in the project root as:

```text
credentials.json
```

Share the target Google Sheet with the `client_email` from `credentials.json`.

Copy `.env.example` to `.env`:

```bash
cp .env.example .env
```

Fill in:

```text
SENDER_EMAIL=your_email@gmail.com
SENDER_APP_PASSWORD=your_app_password
RECEIVER_EMAIL=recipient@example.com
```

The `.env` file and `credentials.json` are ignored by Git and should not be committed.

## Google Sheet Layout

The script expects a spreadsheet named:

```text
Automated_Status_DB
```

With worksheets named:

```text
Raw_Data
Weekly_Summary
```

Rows loaded into `Raw_Data` use this schema:

```text
record_id
source
title
status
priority
```

## Run

```bash
python main.py
```

## Scheduling

The pipeline can be scheduled locally with Windows Task Scheduler or on Linux/macOS with cron.

Example weekly cron schedule:

```text
0 16 * * 5 /usr/bin/python3 /absolute/path/to/POC-auto-ETL-pipeline/main.py
```

## Limitations

- CRM data is mocked for proof-of-concept purposes.
- Google Sheets is used as a lightweight reporting store, not a production database.
- SMTP delivery uses a Gmail app password stored in local environment variables.
