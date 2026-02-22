# Bank Management System

This repository currently contains a C++ console project (`bankmanagement.cpp`).
If you want to **develop a website** for a small banking system with admin-controlled member management and monthly deposits, use the following requirements as your implementation guide.

## 1) Core Functional Requirements

### Authentication and Roles
- **Admin login** (single or multiple admins).
- **Member login** using generated **User ID + password**.
- Only admins can:
  - Add members.
  - Remove/deactivate members.
  - Reset member passwords.
- Members can:
  - View their profile.
  - View deposit history.
  - View current balance and monthly due status.

### Member Lifecycle
- Admin creates member profile with required details (name, phone, address, join date, monthly contribution amount).
- System auto-generates a unique **User ID**.
- System issues an initial password (temporary/random), and forces password change on first login.
- Admin can deactivate members instead of hard-delete (recommended for audit trail).

### Deposit Management
- Each member has a fixed or configurable **monthly deposit target**.
- Admin can record deposits (cash/manual entry) or members can submit proof if online payment is used.
- Track per-month status:
  - Paid
  - Partially paid
  - Unpaid
- Keep complete transaction history (date, amount, method, entered by, reference).

### Basic Reports
- Member list with status (active/inactive).
- Monthly collection report.
- Defaulters/unpaid member report.
- Individual member ledger.

## 2) Non-Functional Requirements

- **Security:** password hashing, secure session handling, role-based authorization, CSRF protection.
- **Data integrity:** transactional writes for money operations.
- **Auditability:** store `created_by`, `updated_by`, and action logs for admin operations.
- **Usability:** responsive UI (mobile + desktop).
- **Maintainability:** modular backend (auth, members, deposits, reports).

## 3) Suggested Tech Stack (Simple and Practical)

### Option A (Recommended for fast development)
- **Frontend:** React + Vite + Tailwind CSS
- **Backend:** Node.js + Express
- **Database:** PostgreSQL
- **ORM:** Prisma
- **Auth:** JWT (HTTP-only cookies)
- **Deployment:** Docker + Nginx + Render/Railway/EC2

### Option B (Also strong)
- **Frontend:** Server-rendered templates (Django or Laravel Blade)
- **Backend:** Django (Python) or Laravel (PHP)
- **Database:** PostgreSQL/MySQL

## 4) Database Schema (Minimum)

### `users`
- `id` (PK)
- `user_id` (unique, auto-generated, member-facing login ID)
- `password_hash`
- `role` (`ADMIN`, `MEMBER`)
- `is_active`
- `must_change_password`
- `created_at`, `updated_at`

### `members`
- `id` (PK)
- `user_id_fk` (FK -> users.id)
- `full_name`
- `phone`
- `address`
- `join_date`
- `monthly_target`
- `status` (`ACTIVE`, `INACTIVE`)

### `deposits`
- `id` (PK)
- `member_id_fk` (FK -> members.id)
- `year`
- `month`
- `amount`
- `deposit_date`
- `payment_method`
- `reference_note`
- `recorded_by` (FK -> users.id)
- `created_at`

### `audit_logs`
- `id` (PK)
- `actor_user_id`
- `action` (e.g., `CREATE_MEMBER`, `DELETE_MEMBER`, `ADD_DEPOSIT`)
- `target_type`
- `target_id`
- `metadata_json`
- `created_at`

## 5) User ID Generation Rules

A safe pattern:
- Prefix + year + zero-padded sequence.
- Example: `MBR-2026-0001`, `MBR-2026-0002`.

Requirements:
- Uniqueness enforced at DB level.
- Generated only on member creation.
- Never reused, even if member is removed.

## 6) Security Checklist

- Hash passwords with **bcrypt/argon2**.
- Enforce strong password policy for admins.
- Use HTTPS in production.
- Use rate limiting on login routes.
- Store secrets in environment variables.
- Validate/sanitize all inputs.
- Add authorization middleware for every protected route.

## 7) Development Environment Setup

- Install:
  - Git
  - Node.js LTS (or Python/PHP runtime depending on stack)
  - PostgreSQL
  - Docker (optional but recommended)
- Create environments:
  - `local`
  - `staging`
  - `production`
- Create `.env` with keys like:
  - `DATABASE_URL`
  - `JWT_SECRET`
  - `APP_ENV`
  - `PORT`

## 8) MVP Development Plan

1. Setup project structure + database.
2. Implement auth (admin/member roles).
3. Implement admin member CRUD + auto user ID generation.
4. Implement monthly deposit entry + history.
5. Build dashboard and reports.
6. Add validation, logs, and basic automated tests.
7. Deploy to staging and run UAT.

## 9) Clarifications You Should Finalize Before Coding

- Can members self-register, or strictly admin-created only?
- Is deposit amount fixed for all members or member-specific?
- Can members pay multiple partial deposits in a month?
- Are late fees/penalties required?
- Is online payment integration needed now or later?
- Should deletion be hard delete or deactivate only?

---
If you want, the next step can be a complete API contract (`/auth`, `/members`, `/deposits`, `/reports`) and an implementation-ready folder structure.
