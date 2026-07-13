# Update Plan

Date: 2026-07-13

## Confirmed Paths

| Environment | Host | Web root | WordPress path |
|---|---|---|---|
| Staging | ai119.secure.ne.jp | `/html` | `/html/cms` |
| Production | rikusan.sakura.ne.jp | `/home/rikusan/www` | `/home/rikusan/www/cms` |

## PHP Constraint

Production is currently intended to remain on PHP 7.4.33.

WordPress 7.0 supports PHP 7.4, but some current plugin versions require PHP 8.0+. MW WP Form is the main compatibility point to verify before production changes.

## Required Before Production Work

- Full file backup.
- Full database backup.
- WordPress administrator account for staging and production.
- Confirmation of the staging site URL and whether it should be overwritten with a production clone.
- Maintenance window and rollback decision point.

## Staging Test Checklist

- Home page renders correctly.
- Company/profile/office/product/news pages render correctly.
- Order/contact form loads, validates, submits, and sends mail.
- Form file-upload behavior is tested if any upload fields exist.
- Admin login works.
- Post/page edit screen works.
- MetaSlider output renders.
- No PHP fatal errors in logs.
- Mobile viewport layout is acceptable.

## Production Rollback Plan

1. Put the site in maintenance mode if needed.
2. Restore previous files from backup.
3. Restore previous database backup only if a database migration/update was applied.
4. Clear cache and verify public pages.
