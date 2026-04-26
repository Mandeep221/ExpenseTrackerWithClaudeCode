# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

The system Python does not have Flask installed. Always use the venv interpreter:

```bash
venv/bin/python app.py
```

App runs at `http://127.0.0.1:5001`. If port 5001 is already in use:

```bash
lsof -ti :5001 | xargs kill -9
```

## Architecture

**Spendly** is a Flask expense-tracking app in early development. Most routes are placeholder stubs; students implement them step by step.

### Entry point: `app.py`
Defines all routes and maps them to Jinja2 templates. Routes currently serving full HTML: `/`, `/register`, `/login`, `/terms`, `/privacy`. All other routes (`/logout`, `/profile`, `/expenses/*`) return stub strings.

### Templates
All templates extend `templates/base.html` via `{% extends 'base.html' %}` and fill three blocks:
- `{% block title %}` — page `<title>`
- `{% block head %}` — extra `<link>` tags (e.g., page-specific CSS)
- `{% block content %}` — page body
- `{% block scripts %}` — page-specific JS at bottom of `<body>`

`base.html` includes the sticky navbar, `<main>` wrapper, footer (with Terms/Privacy links), and loads `static/js/main.js` + `static/css/style.css` globally.

### CSS
- `static/css/style.css` — global design system: CSS custom properties (`--ink`, `--accent`, `--paper`, `--font-display`, `--font-body`, radius tokens, etc.), navbar, footer, auth forms, `.terms-page`, buttons, hero, features section. All new components should use these tokens.
- `static/css/landing.css` — landing page only, loaded via `{% block head %}` in `landing.html`. Contains the hero redesign (`.lp-hero`, `.lp-dashboard`, stat cards, category bars) and the YouTube video modal.

### Key design tokens (from `style.css`)
| Token | Value |
|---|---|
| `--ink` | `#0f0f0f` |
| `--accent` | `#1a472a` (dark green) |
| `--paper` | `#f7f6f3` |
| `--font-display` | DM Serif Display |
| `--font-body` | DM Sans |

### Database (`database/db.py`)
Currently a stub. Planned functions: `get_db()` (SQLite connection with row factory + foreign keys), `init_db()` (CREATE TABLE IF NOT EXISTS), `seed_db()` (sample data). Not yet implemented.

## Adding New Pages

1. Add a route in `app.py`
2. Create `templates/<page>.html` extending `base.html`
3. If the page needs its own CSS, create `static/css/<page>.css` and load it via `{% block head %}`
4. Reuse `.terms-page` class for simple content pages (centered, max-width 760px, `font-display` headings)
