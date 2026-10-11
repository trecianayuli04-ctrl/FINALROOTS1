# RLW — Outlet & Cashier Management (PRD)

## Original Problem Statement
Import project from GitHub `trecianayuli04-ctrl/rlwapp` (branch main), set up & install dependencies, then revise/develop the RLW cashier + outlet management app: rebrand ROLLOW→RLW with white + dark leafy green (remove orange, remove cafe photo); external orders without price; add-item from menu + manual with stock linking; real-time Kas Outlet dashboard; separate Kas Outlet vs Finance expenses; attendance with live camera photo. References: ESB Dashboard (layout/report inspiration, not visual copy).

## Architecture
- **Frontend**: Expo (expo-router) React Native **Web**, served by Metro dev on port 3000. Theme in `src/theme.ts` (centralized tokens). API base via `EXPO_PUBLIC_BACKEND_URL`.
- **Backend**: FastAPI + Motor/MongoDB, all routes under `/api`. JWT auth + Emergent Google session. Single outlet (`OUTLET="rollow-main"`).
- Supervisor runs `yarn start` (→ `expo start --port 3000`) and uvicorn 8001.

## Users / Roles
Owner, Manager, Supervisor, Finance, Marketing, Crew (role-based PERM map in server.py + src/auth.tsx).

## Core Requirements (static)
Sales/POS, inventory & recipes (auto stock deduct), register/shift cash, finance ledger, expenses, attendance+payroll, purchasing, waste, promos, customers/loyalty, reports.

## Implemented (2026-06)
- **Import & setup**: cloned rlwapp, installed backend (pip) + frontend (yarn) deps; added backend env (JWT_SECRET, OWNER/DEMO creds); seeds owner + 5 demo users (password `Rollow@2026`, PIN 1234).
- **Rebrand + theme**: app name RLW; white + dark leafy green (`#1B5E20`); removed orange; removed cafe hero photo on login (clean logo layout).
- **External orders without price**: `ExternalOrderSheet` — order no. + items only (menu chips reduce stock, manual items optionally linked to a menu); backend `/external-sales` accepts price 0, skips zero income line.
- **Menu + manual item with stock linking** (POS): manual sheet has "Hubungkan ke menu" chips; linked item added as menu line (stock auto-decrements), free item has no stock link.
- **Kas Outlet real-time** (`/m/cash`, backend `/api/cash/outlet`): balance = opening + cash-in − cash-out (cash/session-tied only), live history; Finance-sourced expenses do NOT reduce it.
- **Expense separation** (`/m/finance`): source = Kas Outlet (`kasir`, reduces drawer) vs Finance (`finance`, does not); tabs Semua|Kas Outlet|Finance, search filter, per-source + combined totals, green/neutral badges. Backend ties session cash to `source=="kasir" && method=="Cash"`.
- **Camera attendance**: `src/camera.tsx` (native image-picker) + `src/camera.web.tsx` (getUserMedia live capture, graceful error on denial). Clock in/out on dashboard + Absensi open camera; photo stored with record and shown in history. `AttendanceIn.photo` added backend.

## Verification
testing_agent iteration_1: frontend 100% — all 6 features verified with concrete values (Kas Outlet Rp40.000, Finance split 10k/25k/35k, external sheet zero price inputs, manual menu-link chips, camera overlay). Backend verified via curl.

## Changelog (2026-10)
- **Login logo rename**: brand text on login page changed "RLW" → "ROOTS" (`app/login.tsx` only, per user request — all other UI text, receipt, backend prefix `RLW-`, and DB seed values intentionally kept as RLW). Verified via screenshot.

## Backlog / Next (P1/P2)
- P1: Apply camera to POS kiosk attendance (`KioskAttendance`).
- P1: Kas Outlet manual "uang masuk/tambah kas" entry UI.
- P2: Finance date-range & category dropdown filters.
- P2: Export Kas Outlet / expense reports to PDF/print.
