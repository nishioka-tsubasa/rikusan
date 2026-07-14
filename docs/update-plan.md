# Update Plan

Date: 2026-07-13

## Confirmed Paths

| Environment | Host | Web root | WordPress path |
|---|---|---|---|
| Staging | ai119.secure.ne.jp | `/html` | `/html/cms` |
| Production | rikusan.sakura.ne.jp | `/home/rikusan/www` | `/home/rikusan/www/cms` |

## Current Staging Check

- FTP/FTPS access works.
- Staging WordPress core was updated to `7.0.1` after switching staging PHP to `7.4.28`.
- Production WordPress core is `5.0.22`.
- Staging currently uses the customized Tracks `1.60`-era theme. Official Tracks `1.81` was tested and rolled back because it removed site-specific files and caused theme image 404s.
- Staging is not currently a full match for production. Production has plugins such as `mw-wp-form`, `autoptimize`, `invisible-recaptcha`, `protect-uploads`, and others that were not present in the first staging plugin list.
- `https://ai119.secure.ne.jp/`, `/index.php`, `/cms/`, and `/cms/readme.html` do not expose the WordPress site for browser verification. The staging public URL or host mapping is still required.
- Staging SiteGuard login rewrite found: `/cms/login_z76c9dmu`.

## PHP Constraint

Production is currently intended to remain on PHP 7.4.33.

WordPress 7.0 supports PHP 7.4. Staging has been validated on PHP `7.4.28` with WordPress `7.0.1`, Contact Form 7 `6.1.6`, MetaSlider `3.110.0`, and the production-only plugin set added for parity testing.

Some current plugin versions require PHP 8.0+. MW WP Form latest requires PHP `8.0`, so the production candidate while staying on PHP `7.4` is MW WP Form `5.0.4`.

The Japanese WordPress package API currently offers `wordpress-7.0.1.zip` with a PHP minimum of `7.4`.

## Required Before Production Work

- Full file backup.
- Full database backup.
- WordPress administrator account for staging and production.
- Maintenance window and rollback decision point.
- Decision on Tracks theme handling: keep the customized theme as-is, or first migrate customizations into a child theme/custom plugin before updating Tracks.
- Do not reintroduce What's New Generator in production; keep the MU shortcode replacement approach used on staging.
- Keep `sakura-rs-wp-ssl` environment-specific and verify SSL behavior on production after file updates.

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
