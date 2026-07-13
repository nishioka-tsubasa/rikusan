# Staging QA

Date: 2026-07-13
Target: http://rikusan.3d-showcase.net/

## Summary

The staging site is reachable over HTTP. Major public pages return 200 and the checked CSS/JS/image/PDF assets return 200. No obvious PHP fatal/error/warning text was found in the checked HTML.

This was a public, unauthenticated check. Admin editing, plugin update screens, and real form submission were not tested.

## Environment Observed From Public HTML

- WordPress: `5.2.21`
- Theme: Tracks `1.60`
- Contact Form 7 asset version: `5.0.4`
- MetaSlider asset version: `3.10.2`
- What's New Generator asset version: `2.0.2`

## Checked Pages

| URL | Result |
|---|---|
| `/` | 200 |
| `/profile/` | 200 |
| `/office/` | 200 |
| `/light/` | 200 |
| `/design/` | 200 |
| `/etc/` | 200 |
| `/guide/` | 200 |
| `/links/` | 200 |
| `/privacy/` | 200 |
| `/information/?cat=1` | 200 |
| `/recruit/` | 200 |
| `/cms/login_z76c9dmu` | 200 |
| `/cms/wp-login.php` | 404, expected because SiteGuard hides the default login |

## Findings

### 1. `/order-form/` redirects to home

`http://rikusan.3d-showcase.net/order-form/` returns a 302 and ends at the home page. This matches the broad behavior seen on production, where `/order-form/` redirects through `/contact` and ends at home, but it means the order form is not directly visible for testing from this URL.

### 2. HTTPS certificate mismatch

`https://rikusan.3d-showcase.net/` fails certificate validation because the certificate does not match `rikusan.3d-showcase.net`. The provided HTTP URL works. Use HTTP for staging checks unless a matching staging certificate is installed.

### 3. Staging is not fully production-equivalent

The staging public HTML shows WordPress `5.2.21`, while production public HTML shows WordPress `5.0.22`. Plugin versions and installed plugin sets also differ. This is acceptable for a staging smoke test, but it weakens confidence that staging update results will exactly predict production behavior.

## Asset Check

Checked public resources linked by the major pages, including:

- Theme CSS/JS/images
- Contact Form 7 CSS/JS
- MetaSlider CSS/JS
- What's New Generator CSS
- WordPress bundled jQuery and block library CSS
- Upload images and PDFs referenced on checked pages

All checked same-host resources returned 200.

## Before Updating Staging

- Confirm whether `/order-form/` should be visible in staging.
- Confirm whether staging has been restored from the latest production DB/files.
- Provide WordPress admin credentials if admin screens, plugin update status, or form submission must be tested.
