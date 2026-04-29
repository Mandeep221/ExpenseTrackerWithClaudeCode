# Spec: Login and Logout

## Overview
Implement working login and logout so registered users can authenticate with Spendly. The `/login` route currently renders a static form; this step wires up the POST handler to validate credentials, start a Flask session on success, and protect routes that require authentication. `/logout` clears the session and redirects to the landing page. This step unlocks all future logged-in features.

## Depends on
- Step 01 — Database Setup (users table, `get_db()` functional)
- Step 02 — Registration (users exist in the database with hashed passwords)

## Routes
- `GET /login` — render login form — public (already exists, needs POST support added)
- `POST /login` — validate credentials, set session, redirect to dashboard or expenses — public
- `GET /logout` — clear session, redirect to `/` — public (no auth guard needed)

## Database changes
No new tables or columns. Reads from the existing `users` table:
- `email`, `password_hash` used for credential verification
- `id`, `name` stored in session on success

## Templates
- **Modify:** `templates/login.html`
  - Add `method="POST"` and `action="/login"` to the `<form>` tag
  - Add `{% if error %}` error block to display server-side validation errors
  - Fields needed: `email`, `password`
  - Add link to `/register` for users without an account
- **Modify:** `templates/base.html`
  - Update navbar links to conditionally show Login/Register when logged out, and user name + Logout when logged in (use `session.get('user_name')`)

## Files to change
- `app.py`
  - Update `/login` route to handle GET and POST
  - Implement `/logout` route (currently a stub)
  - Add a `get_user_by_email` helper call (or inline query) to look up the user
  - Import `check_password_hash` from `werkzeug.security`
- `database/db.py`
  - Add `get_user_by_email(email)` function — returns a user Row or None
- `templates/login.html` — wire up the form (see Templates above)
- `templates/base.html` — conditional navbar (see Templates above)

## Files to create
None.

## New dependencies
No new dependencies. Uses:
- `werkzeug.security.check_password_hash` (already installed)
- `flask.session` (already imported)

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only — never use string formatting in SQL
- Verify passwords with `werkzeug.security.check_password_hash`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Session keys: `user_id` (int), `user_name` (str), `user_email` (str)
- On bad credentials render the form again with a generic error: "Invalid email or password." (do not reveal which field was wrong)
- On success: redirect to `/` for now (dashboard/expenses page comes in a later step)
- Strip whitespace from email before lookup; do not strip password
- `/logout` must call `session.clear()` then redirect to `/`
- Do not implement route guards (`@login_required`) in this step — that comes with profile/expenses steps

## Definition of done
- [ ] `GET /login` renders the login form
- [ ] Submitting valid credentials sets `session['user_id']`, `session['user_name']`, `session['user_email']` and redirects to `/`
- [ ] Submitting an unknown email renders the form with "Invalid email or password."
- [ ] Submitting a correct email but wrong password renders the form with "Invalid email or password."
- [ ] Submitting an empty email or password renders the form with an appropriate error
- [ ] `GET /logout` clears the session and redirects to `/`
- [ ] After login the navbar shows the user's name and a Logout link
- [ ] After logout the navbar reverts to showing Login and Register links
- [ ] App starts without errors after changes
