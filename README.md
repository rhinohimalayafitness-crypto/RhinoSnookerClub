# 🦏 Rhino Snooker Club — Table Billing App

A single-file, mobile-first web app for running a snooker/pool club: start and bill
table sessions by the minute, collect UPI payments, and give the owner a live
dashboard of history, reports, and settings. Built as one self-contained
`index.html` — no build step, no backend server, just Firebase Firestore for
storage and live sync.

---

## Features

- **Table billing** — start a game with player name & phone, live running
  timer and cost, pause/resume, end game and print a receipt.
- **UPI payments on the receipt** — customer taps "Pay via UPI" and picks
  their app (Google Pay, PhonePe, Paytm, or any other UPI app / BHIM),
  each shown with its real brand logo.
- **Live Dashboard** — read-only board showing every table's status at a glance.
- **History** — active sessions right now, recent completed games, and a
  "Kick Out" tool to force-end a stuck session.
- **Reports** —
  - **Today** panel: today's revenue and games played, always current
    regardless of the date range picked below it.
  - **Revenue report**: pick any date range, see revenue/games/running
    tables/occupancy for that range, and export it to a PDF.
  - **Revenue chart**: hourly revenue bars for the selected range.
  - **Table popularity**: which table gets played the most (👑 marks the
    winner), plus a **peak hours** chart showing what time of day is busiest.
  - **Revenue by table**: how much each table has earned in the range.
  - **Table status**: live donut of occupied vs. free tables.
- **Settings** — UPI ID, table names, per-table rates, customer self-service
  toggles (end/pause games themselves), max pause duration, sound reminder
  interval, admin/supervisor accounts, a change log, your own password, and
  printable QR codes per table.
- **Roles** — `admin` (full access, can manage rates/payments/tables/accounts)
  and `supervisor` (can run day-to-day operations but can't touch payments,
  rates, table names, or accounts).
- **Works fully on mobile** — bottom nav (Home · Tables · History · Reports ·
  Settings), designed for a phone screen, installable as a PWA-style shortcut.

---

## Tech stack

- Plain HTML/CSS/JavaScript — no framework, no build tools.
- [Firebase Firestore](https://firebase.google.com/docs/firestore) for data
  storage and real-time sync across devices (so a customer's phone, the
  counter tablet, and the owner's phone all stay in sync).
- [jsPDF](https://github.com/parallax/jsPDF) + `jspdf-autotable` for the
  "Export to PDF" revenue report.
- [QR Server API](https://goqr.me/api/) to generate the per-table QR codes.
- Brand logos for Google Pay / PhonePe / Paytm served from the
  [Simple Icons CDN](https://simpleicons.org/) (falls back to plain lettered
  badges automatically if the CDN is unreachable).

---

## Getting started

1. **Create a Firebase project** (free "Spark" plan is enough) and enable
   **Firestore Database**.
2. In `index.html`, replace the `firebaseConfig` object with your own
   project's config (find it in Firebase console → Project settings →
   your web app):
   ```js
   var firebaseConfig = {
     apiKey: "...",
     authDomain: "...",
     projectId: "...",
     storageBucket: "...",
     messagingSenderId: "...",
     appId: "..."
   };
   ```
3. **Set Firestore security rules.** For a quick start you can allow
   read/write on the `club` collection (tighten this before going live with
   real customer data):
   ```
   match /club/{doc} {
     allow read, write: if true;
   }
   ```
4. **Host the file.** Any static host works — Firebase Hosting, GitHub
   Pages, Netlify, Vercel, or just open `index.html` directly in a browser
   for testing. For the "scan QR to open this table" feature to work, it
   needs to be hosted at a public URL.
5. **First login.** The app seeds a default admin account on first run:
   - Username: `admin`
   - Password: `snooker123`

   Log in from **Settings** and change the password immediately
   (Settings → Your password).

---

## Configuring your club

Once logged in as admin, everything is editable from **Settings**:

| Setting | Where |
|---|---|
| Number of tables | `TABLES` array at the top of the `<script>` (default: `["1","2"]`) |
| Table names | Settings → Tables |
| Rate per minute | Settings → Rates per minute |
| UPI ID for payments | Settings → Payments |
| Let customers end/pause their own game | Settings → Customer Controls |
| Max pause before "Kill Session" shows up | Settings → Customer Controls |
| Sound reminder interval | Settings → Customer Controls |
| Supervisor accounts | Settings → Accounts (admin only) |

To add more than 2 tables, extend `TABLES`, `DEFAULT_RATES`, and
`DEFAULT_TABLE_NAMES` near the top of the script.

---

## App structure (bottom nav)

- **Home** — pick a table, start a game.
- **Tables** — live read-only dashboard of every table.
- **History** *(login required)* — active sessions, recent games, kick out.
- **Reports** *(login required)* — revenue, charts, table popularity, peak
  hours, occupancy, PDF export.
- **Settings** *(login required)* — payments, tables, rates, controls,
  accounts, QR codes.

---

## Notes

- All data lives in four Firestore documents inside a `club` collection:
  `sessions`, `history`, `config`, `accounts`, `changes`. There's no
  pagination — history keeps growing over time, so periodically archive or
  trim it if the club runs for years.
- The app has no server-side auth — usernames/passwords are checked against
  Firestore data on the client. Fine for a single-location club dashboard on
  trusted devices; don't reuse these credentials elsewhere.
- Built and maintained by **Sumit Dhyani**.
