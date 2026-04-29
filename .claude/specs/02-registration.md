# Spec: Registration

## Overview
Implement working user registration so visitors can create a Spendly account. The `/register` route currently only renders a static HTML form; this step wires up the POST handler to validate input, hash the password, insert a new user row, and redirect on success. This is the first step in the authentication flow and unlocks the login step that follows.

## Depends on
- Step 01 — Database Setup (users table must exist, `get_db()` must be functional)

## Routes
- `GET /register` — render registration form — public (already exists, no change needed)
- `POST /register` — process form submission, create user, redirect to login — public

## Database changes
No new tables or columns. Uses the existing `users` table:
- `id`, `name`, `email`, `password_hash`, `created_at`

## Templates
- **Modify:** `templates/register.html`
  - Add `{% if error %}` flash block (already present — ensure it renders server-set `error` variable)
  - Preserve existing form fields: `name`, `email`, `password`
  - No structural changes needed; template is already wired for POST

## Files to change
- `app.py` — update `/register` route to handle GET and POST; add `secret_key` to app config; add imports (`request`, `redirect`, `url_for`, `session`)
- `database/db.py` — no changes required

## Files to create
None.

## New dependencies
No new dependencies. Uses:
- `werkzeug.security.generate_password_hash` (already installed)
- `sqlite3` UNIQUE constraint for duplicate email detection

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only — never use string formatting in SQL
- Hash passwords with `werkzeug.security.generate_password_hash` using `pbkdf2:sha256`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- `app.secret_key` must be set (use a hard-coded dev string for now, e.g. `"spendly-dev-secret"`)
- Validate server-side: name non-empty, valid email format is not required (browser handles it), password ≥ 8 characters
- On duplicate email (sqlite3.IntegrityError) render the form again with `error="An account with that email already exists."`
- On success: redirect to `/login` (do NOT auto-login the user — that is Step 3)
- Strip whitespace from `name` and `email` before inserting

## Definition of done
- [ ] `GET /register` renders the registration form
- [ ] Submitting the form with valid data inserts a new user row in the database with a hashed password
- [ ] Submitting with a duplicate email re-renders the form with the error message "An account with that email already exists."
- [ ] Submitting with a password shorter than 8 characters re-renders the form with an appropriate error message
- [ ] Submitting with an empty name re-renders the form with an appropriate error message
- [ ] Successful registration redirects to `/login`
- [ ] Passwords are stored as hashes (never plain text) — verifiable by inspecting the database
- [ ] App starts without errors after changes
