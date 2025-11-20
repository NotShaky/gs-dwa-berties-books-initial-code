# Bertie's Books 

Small Express + EJS app with a MySQL backend. Supports books, user registration/login and a login audit.

Prerequisites
- Node.js (14+)
- MySQL server
- dotenv 

Quick setup
1. Install dependencies:
   npm install

2. Create database and tables (run on the VM or locally)

3. Start the app:
   node index.js

Key routes
- Home: /
- Books: /books/list, /books/addbook, /books/search
- Users: /users/register, /users/login, POST /users/loggedin
- Audit: /users/audit

## dotenv

- This app uses the dotenv module to keep DB credentials out of source control.
- The app loads environment variables at startup with:
  require('dotenv').config()
  (see index.js). The DB pool reads: BB_USER, BB_PASSWORD, BB_DATABASE.
- Create a `.env` in the project root (copy `.env.example`) and set real values:
  BB_USER='berties_books_app'
  BB_PASSWORD='qwertyuiop'
  BB_DATABASE='berties_books'

## audit

- Purpose: record successful and failed login attempts for monitoring and troubleshooting.
- Storage: rows are inserted into the `audit_log` table (see `sql/create_audit_table.sql`). Columns include:
  - `id`, `username`, `success` (1/0), `reason` (text), `ip`, `user_agent`, `created_at`.
- How it's recorded: the helper function `recordAudit(username, success, reason, req)` in `routes/users.js` inserts an audit row (non-blocking). It is called from the login flow (`POST /users/loggedin`) for every attempt.
- Viewing: the audit history is rendered at `GET /users/audit` using `views/audit.ejs`. The route queries recent records (most recent first).
- Setup: create the table on the VM or locally:

- Privacy note: IP and user-agent are stored for audit purposes — treat this data appropriately and avoid exposing it publicly.