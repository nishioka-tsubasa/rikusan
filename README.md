# rikusan WordPress maintenance

Repository for managing rikusan.com WordPress maintenance work.

## Branches

- `main`: documentation and operational notes.
- `production`: production code snapshot and deployable production changes.
- `staging`: staging/test-environment code snapshot and validation changes.

## Safety Rules

- Do not commit `wp-config.php`, database dumps, FTP credentials, backups, logs, or uploaded media.
- Keep production changes behind a tested staging branch.
- Verify plugin/theme updates on staging before applying them to production.
- Take a full file and database backup before changing production.

## Current Plan

1. Create/verify a staging clone matching production PHP 7.4.
2. Back up production files and database.
3. Remove or replace What's New Generator.
4. Update high-risk plugins on staging.
5. Update WordPress core to the latest 7.0 patch release on staging.
6. Test public pages, forms, email delivery, admin editing, and mobile layout.
7. Apply the same sequence to production during a maintenance window.
