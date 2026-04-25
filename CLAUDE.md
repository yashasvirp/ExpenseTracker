# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Setup

This project uses a local virtualenv at `.claude_env/`. Activate it before running anything:

```bash
source .claude_env/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

## Running the App

```bash
python app.py
```

Runs on `http://localhost:5001` with debug mode enabled.

## Running Tests

```bash
pytest
# Run a single test file
pytest tests/test_foo.py
# Run a single test
pytest tests/test_foo.py::test_name
```

## Architecture

This is a **Flask + SQLite** expense tracking web app called **Spendly**. It is a student project being built incrementally — many routes and features are stubs that students fill in during numbered steps.

### Key Files

- `app.py` — all Flask routes live here; stub routes are marked with their step number
- `database/db.py` — SQLite helpers: `get_db()`, `init_db()`, `seed_db()` (students implement these in Step 1)
- `static/js/main.js` — vanilla JS only, no frameworks; students add features here incrementally
- `static/css/style.css` — global styles
- `templates/base.html` — shared layout with navbar and footer; all other templates extend it

### Template Structure

All templates extend `base.html` via `{% extends "base.html" %}`. Page-specific CSS or JS is injected via `{% block head %}` and `{% block scripts %}` respectively.

### Planned Route Structure

| Route | Status |
|---|---|
| `GET /` | Landing page (done) |
| `GET /register`, `POST /register` | Auth (GET done, POST stub) |
| `GET /login`, `POST /login` | Auth (GET done, POST stub) |
| `GET /logout` | Step 3 stub |
| `GET /profile` | Step 4 stub |
| `GET /expenses/add`, `POST /expenses/add` | Step 7 stub |
| `GET /expenses/<id>/edit` | Step 8 stub |
| `GET /expenses/<id>/delete` | Step 9 stub |

### Database

SQLite via `database/db.py`. Connection uses `row_factory` for dict-style row access and enables foreign keys. Tables are created with `CREATE TABLE IF NOT EXISTS`. No ORM — raw SQL queries throughout.
