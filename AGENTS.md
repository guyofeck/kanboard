# Agent notes (Base44 dev environment)

- Run: `docker compose -f docker-compose.base44.yml up -d --build`. App on port 3000, Postgres 16 in `db`.
- The app service is a plain Alpine + php84 image; the repo is bind-mounted and served by `php -S` (no opcache), so PHP/template/asset edits are live — just refresh. No restart needed.
- `vendor/` is committed; no composer install step. Config comes from env vars read in `app/constants.php` (DB_*, DATA_DIR, DEBUG, LOG_DRIVER). No `config.php` is needed.
- `DATA_DIR=/data` is a named volume (`app-data`) so uploads/cache never land in the repo's `data/`.
- Schema migrations run automatically on first request (`DB_RUN_MIGRATIONS` defaults true).
- Default login: `admin` / `admin`.
- JS/CSS served are the committed minified bundles (`assets/js/app.min.js`, `assets/css/*.min.css`); editing files under `assets/*/src` or `assets/js/components` does NOT change what's served until the bundles are rebuilt.
- Health: `curl localhost:3000/healthcheck.php` → `{"status":200,...}` (checks DB connectivity).
- Tests: `docker compose -f docker-compose.base44.yml exec app ./vendor/bin/phpunit -c tests/units.sqlite.xml` (phpunit is a dev dependency — may not be in the committed vendor).
