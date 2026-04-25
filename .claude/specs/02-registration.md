# Spec: Registration

## Overview
Implement the `POST /register` route so new users can create a Spendly account. The form in `register.html` already exists and posts to `/register`; this step wires up the server-side handler: validates input, checks for duplicate emails, hashes the password, inserts the user into the database, starts a Flask session, and redirects to the profile page. This is the first step that writes user data and introduces Flask sessions — both are required by every authenticated feature that follows.

## Depends on
- Step 1 — database setup (`get_db()`, `init_db()`, `users` table must exist)

## Routes
- `POST /register` — validate form data, create user, start session, redirect to `/profile` — public

## Database changes
No database changes. The `users` table created in Step 1 is sufficient.

## Templates
- **Create:** none
- **Modify:**
  - `templates/register.html` — already renders `{{ error }}`; no structural changes needed. Optionally re-populate `name` and `email` fields with submitted values on validation failure so the user does not have to retype them (add `{{ name }}` and `{{ email }}` as `value` attributes).

## Files to change
- `app.py`
  - Add imports: `request`, `session`, `redirect`, `url_for` from `flask`; `generate_password_hash` from `werkzeug.security`
  - Set `app.secret_key` (use `os.urandom(24)` or a fixed dev string — must be set before sessions work)
  - Change `@app.route("/register")` to `@app.route("/register", methods=["GET", "POST"])`
  - Implement POST handler inside the existing `register()` view

## Files to create
None.

## New dependencies
No new dependencies.

## Rules for implementation
- No SQLAlchemy or ORMs — raw SQL only
- Parameterised queries only — never use string formatting in SQL
- Passwords hashed with `werkzeug.security.generate_password_hash` — never store plaintext
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- `app.secret_key` must be set before any `session` usage
- On validation failure: re-render `register.html` with an `error` message and the submitted `name`/`email` so the user doesn't lose their input
- On duplicate email: catch the `sqlite3.IntegrityError` (UNIQUE constraint) — do not pre-check with a SELECT then INSERT (race condition)
- On success: set `session['user_id']` and `session['user_name']`, then `redirect(url_for('profile'))`

## Validation rules
In this order — stop at the first failure and return its error message:
1. All three fields (`name`, `email`, `password`) must be non-empty
2. Password must be at least 8 characters
3. Email must not already exist in the database (caught via `IntegrityError`)

## Definition of done
- [ ] Submitting the register form with valid data creates a new row in `users` with a hashed password
- [ ] After successful registration, the browser is redirected to `/profile`
- [ ] Submitting with any field empty shows an error message on the register page
- [ ] Submitting with a password shorter than 8 characters shows an error message
- [ ] Submitting with an email that already exists shows "An account with that email already exists."
- [ ] The `name` and `email` fields are repopulated after a validation failure
- [ ] Passwords are stored as a werkzeug hash — never as plaintext (verify with `sqlite3 spendly.db "SELECT password_hash FROM users LIMIT 3"`)
- [ ] Flask session contains `user_id` and `user_name` after successful registration
- [ ] App starts without errors (`python app.py`)
