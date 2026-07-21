# Rhino Snooker Club — Tech Stack

A single-file web app for running a snooker/pool club: table timers, billing, staff accounts, and reporting.

## Overview

The entire app is one static HTML file — no build step, no server to run, no framework. It's structured as a single-page app (SPA): one HTML document with multiple `<div class="screen">` sections that are shown/hidden with JavaScript, rather than separate pages.

## Frontend

| Piece | What it is |
|---|---|
| **HTML/CSS/JavaScript** | Plain, no framework (no React/Vue/etc.), no build tools, no npm install needed to run it. Everything — markup, styles, and logic — lives in `index.html`. |
| **JavaScript style** | Vanilla ES5/ES6, wrapped in a single IIFE (immediately-invoked function expression) so nothing leaks into the global scope except what's needed. |
| **CSS** | Hand-written, using CSS custom properties (`--felt-dark`, `--brass`, etc.) for theming, flexbox for layout, and `@media print` rules for the printable table-sign feature. |
| **Fonts** | Google Fonts — **Bebas Neue** (headings/display), **Inter** (body text), **JetBrains Mono** (numbers/timers), loaded via `<link>` from `fonts.googleapis.com`. |

## Backend / Data

| Piece | What it is |
|---|---|
| **Firebase Firestore** | The database. Loaded via the Firebase JS SDK (compat build, v10.12.2) directly from Google's CDN — no npm, no bundler. |
| **Real-time sync** | The app uses Firestore's `onSnapshot` listeners (a persistent streaming connection, not classic polling) so every open tab/device — Home, Active, Reports, Settings — updates within a fraction of a second when data changes anywhere else. |
| **Data model** | A handful of documents under a single `club` collection: `config` (rates, table names, toggles), `sessions` (live games), `history` (completed games), `accounts` (admin/supervisor logins), `changes` (audit log). |
| **Auth** | Custom — not Firebase Authentication. Accounts (username/password/role) are stored as plain documents in Firestore and checked client-side. This keeps things simple for a single-club deployment, but it's worth knowing it isn't using Firebase's real auth system. |

## Third-party services (all client-side, no API keys required)

| Service | Used for |
|---|---|
| **api.qrserver.com** | Generates the QR codes for each table (both the in-app QR grid and the printable wall signs) — just an image URL, no account needed. |
| **cdn.simpleicons.org** | Small brand icons (Google Pay / PhonePe / Paytm logos) shown next to the UPI deep-link buttons. |
| **jsPDF + jsPDF-AutoTable** (cdnjs.cloudflare.com, v2.5.1 / v3.8.2) | Powers the "Export to PDF" button in Reports — builds a formatted PDF report client-side, entirely in the browser. |

## Notable features built on this stack

- **Live multi-device sync** — Firestore listeners keep every screen current across devices without any polling code.
- **Role-based accounts** — admin vs. supervisor, with an audit log ("Recent Changes") of who changed what and when.
- **Pause/resume billing** — elapsed-time math excludes paused duration from the bill.
- **Printable table signs** — a hidden in-page view (`#printSignsArea`) with `@media print` rules that turns each table into a full-page wall sign, using the browser's native print dialog — no PDF generation needed for that particular feature.
- **UPI deep links** — `upi://`, `gpay://`, `phonepe://`, `paytmmp://` URI schemes to hand off to whichever payment app is installed, with iOS/Android differences handled explicitly.

## What's *not* in here (yet)

- No payment gateway / server-side payment verification — the UPI buttons are deep links only, with no automated confirmation that a payment succeeded.
- No backend server of your own — everything server-side is Firestore directly, accessed from the browser.
- No build pipeline, package manager, or framework — intentionally kept as a single portable HTML file.
