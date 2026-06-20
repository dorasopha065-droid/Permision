# School Attendance Management System

A Web-based School Attendance Management System with a FastAPI python backend, Google Sheets database, and an automated Telegram notification alert when a student reaches 5 or more absences.

## Project Structure
```
Web_Permission/
├── backend/
│   ├── app.py           # FastAPI application
│   ├── config.py        # Settings loader and environment config
│   └── sheets_service.py # Google Sheets connection & Telegram bot trigger
├── frontend/
│   ├── login.html       # Sign-in portal
│   ├── teacher_dashboard.html # Attendance logging interface
│   ├── css/
│   │   └── style.css    # Premium CSS design
│   └── js/
│       ├── login.js     # Login handler
│       └── teacher.js   # Teacher client controller
├── requirements.txt     # Python dependencies
└── README.md            # This manual
```

---

## 1. Quick Start (Mock Development Mode)
The application has a built-in **Mock Development Mode** enabled by default. This allows you to test the complete flow (UI, API calls, role redirection, and simulated Telegram alerts) immediately without configuring Google Sheets credentials or a live Telegram Bot.

1. **Start the FastAPI Server**:
   ```bash
   # On Windows PowerShell:
   .venv\Scripts\python.exe -m uvicorn backend.app:app --reload

   # On macOS/Linux:
   .venv/bin/python -m uvicorn backend.app:app --reload
   ```
2. **Access the Portal**:
   - Open your browser and navigate to: [http://127.0.0.1:8000](http://127.0.0.1:8000)
3. **Log In with Mock Credentials**:
   - **Teacher Roster access**:
     - Username: `teacher`
     - Password: `password123`
   - **Other roles placeholder**:
     - Username: `principal` / `parent` / `student`
     - Password: `password123`
4. **Trigger a Alert Test**:
   - Log in as the teacher.
   - You will see "Alice Johnson" starts with **4 absences**.
   - Change Alice Johnson's status to **Absent** and click **Submit Attendance**.
   - Alice's total absences will update to **5** in the UI, triggering a mock Telegram alert warning printed directly in the backend terminal logs!

---

## 2. Production Database Setup (Google Sheets API)

To hook up your real Google Sheets database:

1. **Enable Google Drive & Sheets APIs**:
   - Go to the [Google Cloud Console](https://console.cloud.google.com/).
   - Create or select a project.
   - Go to APIs & Services > Library, search for **Google Drive API** and **Google Sheets API**, and enable both.

2. **Generate Service Account Credentials JSON**:
   - Go to APIs & Services > Credentials.
   - Click **Create Credentials** > **Service Account**.
   - Follow prompts and click on the newly created Service Account.
   - Go to the **Keys** tab, click **Add Key** > **Create new key** > Select **JSON**.
   - A credentials file will download. Rename this file to `credentials.json` and place it in the root of the project (`c:\Users\sopha\OneDrive\Desktop\Web_Permission\credentials.json`).

3. **Share Your Spreadsheet**:
   - Open your Google Sheet in a browser.
   - Click **Share** in the top right.
   - Copy the email address of your Service Account (found in `client_email` inside `credentials.json`) and share the sheet with it as an **Editor**.

4. **Spreadsheet Columns Structure**:
   Ensure you create three tabs (worksheets) in the spreadsheet with the exact headers (first row) as follows:

   - **Tab 1: `Users`**
     | username | password | role | linked_id | telegram_chat_id |
     | :--- | :--- | :--- | :--- | :--- |
     | teacher_user | secret123 | Teacher | T01 | 123456789 |
     | principal_user | admin123 | Principal | P01 | 987654321 |

   - **Tab 2: `Attendance`**
     | date | student_id | status | notes |
     | :--- | :--- | :--- | :--- |

   - **Tab 3: `Student_Summary`**
     | student_id | student_name | total_absences | parent_chat_id | teacher_chat_id | principal_chat_id |
     | :--- | :--- | :--- | :--- | :--- | :--- |
     | STU001 | Alice Johnson | 4 | 111222333 | 123456789 | 987654321 |
     | STU002 | Bob Smith | 1 | 222333444 | 123456789 | 987654321 |

---

## 3. Telegram Bot Notification Setup

1. **Create a Bot via BotFather**:
   - Open Telegram and search for `@BotFather`.
   - Send `/newbot` and follow the instructions to choose a name and username.
   - Copy the **HTTP API Token** provided.

2. **Retrieve Stakeholder Chat IDs**:
   - Parents, teachers, and principals must open a chat with your bot and press `/start`.
   - They can retrieve their unique Telegram Chat ID by messaging `@userinfobot` or checking the bot's updates.
   - Put these numerical Chat IDs in the corresponding fields in the Google Sheets.

---

## 4. Environment Configuration (`.env`)
Create a file named `.env` in the root folder and configure:

```env
# Disable mock mode to use live APIs
MOCK_MODE=false

# Google Sheet Details
GOOGLE_SHEET_NAME="School Attendance Management"
GOOGLE_CREDENTIALS_FILE="credentials.json"

# Telegram API Token
TELEGRAM_BOT_TOKEN="1234567890:ABCdefGhIJKlmNoPQRsTUVwxyZ"

# Server details
HOST=127.0.0.1
PORT=8000
```
