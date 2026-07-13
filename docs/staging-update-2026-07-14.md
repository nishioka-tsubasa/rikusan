# Staging Update

Date: 2026-07-14 JST
Target: http://rikusan.3d-showcase.net/

## Important Constraint

The staging server is running PHP `7.0.32`.

WordPress `7.0.1` requires PHP `7.4`, so staging could not be updated to WordPress 7.x without first changing the staging PHP version. I updated staging only to the highest practical range that works on PHP 7.0.

This means staging is still not equivalent to production if production remains PHP `7.4.33`.

## Changes Applied

| Component | Before | After | Result |
|---|---:|---:|---|
| WordPress core | 5.2.21 | 6.5.8 | Updated; DB upgrade completed |
| Contact Form 7 | 5.0.4 | 5.7.7 | Updated |
| What's New Generator | 2.0.2 | disabled/removed from active path | Replaced with compatibility MU shortcode |
| MetaSlider | 3.10.2 | attempted 3.110.0, rolled back to 3.10.2 | Latest failed on PHP 7.0 |
| Tracks theme | 1.60 | attempted 1.81, rolled back to customized 1.60 | Official theme removed site-specific assets |

## Backups Created On Staging

Backups are under `/usr/home/ai119lrhtx/html/_codex_update_backups/`.

- `20260714-050150-whats-new-genarator`
- `20260714-050205-contact-form-7`
- `20260714-050214-ml-slider`
- `20260714-050218-tracks`
- `20260714-050233-wp-admin`
- `20260714-050233-wp-includes`
- `20260714-rollback-official-tracks`
- `20260714-rollback-ml-slider-3.110.0`

## Issues Found And Resolved

### MetaSlider 3.110.0 failed on PHP 7.0

After updating MetaSlider to `3.110.0`, the server logged PHP parse errors:

`ml-slider/inc/slider/metaslider.flex.class.php on line 1152`

This caused REST page rendering failures. MetaSlider was rolled back to `3.10.2`, after which the REST page endpoint returned 200 again.

### Tracks 1.81 removed customized site assets

Updating to the official Tracks `1.81` caused theme image 404s for customized assets. The customized Tracks `1.60` theme was restored.

### What's New Generator removal removed NEWS output

The top page uses `[showwhatsnew]`. Since the original plugin was disabled, a small MU plugin was added at:

`/html/cms/wp-content/mu-plugins/codex-showwhatsnew.php`

It restores the shortcode output using a simple latest-posts query while keeping the vulnerable plugin disabled.

## Final Public Checks

All checked endpoints returned 200:

- `/`
- `/profile/`
- `/office/`
- `/light/`
- `/design/`
- `/etc/`
- `/guide/`
- `/links/`
- `/privacy/`
- `/information/?cat=1`
- `/recruit/`
- `/cms/login_z76c9dmu`
- `/wp-json/wp/v2/pages/58`

`/order-form/` redirects to `/`, matching the pre-update broad behavior on staging.

## Remaining Recommendations

1. Change staging PHP to `7.4` if it is intended to validate production updates to WordPress 7.x.
2. Do not update the customized Tracks theme directly without first moving customizations into a child theme or custom plugin.
3. Do not update MetaSlider beyond `3.10.2` on PHP 7.0; it failed in this environment.
4. For production, test against an environment matching production PHP `7.4.33` before applying WordPress 7.x.

## Follow-up: PHP 7.4 Switch

Date: 2026-07-14 JST

The staging root and WordPress `.htaccess` files were updated with:

`AddHandler x-httpd-php74 .php`

Backups created before the change:

- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-php74-root-htaccess-before`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-php74-cms-htaccess-before`

Confirmed versions:

- Staging before switch: PHP `7.0.32`
- Staging after switch: PHP `7.4.28`
- Production observed version: PHP `7.4.33`

Post-switch smoke checks returned 200:

- `/`
- `/profile/`
- `/office/`
- `/light/`
- `/design/`
- `/etc/`
- `/guide/`
- `/links/`
- `/privacy/`
- `/information/?cat=1`
- `/recruit/`
- `/cms/login_z76c9dmu`
- `/wp-json/wp/v2/pages/58`

The temporary PHP 7.4 probe directory was removed after verification.
