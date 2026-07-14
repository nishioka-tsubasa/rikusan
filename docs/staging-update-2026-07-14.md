# Staging Update

Date: 2026-07-14 JST
Target: http://rikusan.3d-showcase.net/

## Initial Constraint

The staging server was initially running PHP `7.0.32`.

WordPress `7.0.1` requires PHP `7.4`, so staging could not be updated to WordPress 7.x without first changing the staging PHP version. I updated staging only to the highest practical range that works on PHP 7.0.

Staging was later switched to PHP `7.4.28`, which is close enough to production PHP `7.4.33` for WordPress 7.x compatibility testing.

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

## Follow-up: WordPress 7.0.1 And PHP 7.4-Compatible Updates

Date: 2026-07-14 JST

The following updates were tested after confirming staging was running PHP `7.4.28`.

| Component | Before follow-up | Attempted/After | Result |
|---|---:|---:|---|
| WordPress core | 6.5.8 | 7.0.1 | Updated; DB upgrade completed |
| Contact Form 7 | 5.7.7 | 6.1.6 | Updated |
| MetaSlider | 3.10.2 | 3.110.0 | Updated successfully on PHP 7.4 |
| Tracks theme | customized 1.60 | attempted official 1.81, rolled back | Official update removed site-specific assets |
| MW WP Form | not present on staging | not updated | Current MW WP Form requires PHP 8.0 |

Additional backups created on staging:

- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-062204-wp-admin`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-062205-wp-includes`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-062300-contact-form-7`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-062500-ml-slider`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-062612-tracks`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-062612-tracks-181-failed`

### WordPress 7.0.1

Core files were updated to WordPress `7.0.1`, and the WordPress database upgrade screen completed successfully.

Confirmed after update:

- `$wp_version = '7.0.1'`
- `$wp_db_version = 61833`
- Public generator tag reports `WordPress 7.0.1`

### Contact Form 7 6.1.6

Contact Form 7 was updated to `6.1.6`.

Confirmed after update:

- Plugin readme stable tag: `6.1.6`
- Requires WordPress: `6.7`
- Requires PHP: `7.4`
- Public assets load with `ver=6.1.6`
- `/wp-json/contact-form-7/v1` returns 200

### MetaSlider 3.110.0

MetaSlider was updated to `3.110.0`. The PHP 7.0 parse error seen in the earlier attempt did not recur under PHP `7.4.28`.

Confirmed after update:

- Plugin readme stable tag: `3.110.0`
- Public assets load with `ver=3.110.0`
- Top page renders the MetaSlider markup
- Public page and REST smoke tests returned 200

### Tracks 1.81

Tracks official `1.81` was tested, but it removed the customized theme files and directories used by the site, including the custom `images/` directory and page templates.

Observed breakage:

- Theme images such as `rikusan-logo.png`, `lighting_bnr.png`, `design_bnr.png`, and `news-arrow.png` returned 404 after the official update.
- Site-specific files such as `page-rikusan.php`, `page-light.php`, and `page-design.php` disappeared from the active theme directory.

Action taken:

- Official Tracks `1.81` was moved to the backup area as `20260714-062612-tracks-181-failed`.
- The customized Tracks theme was restored from `20260714-062612-tracks`.
- Theme image URLs returned 200 again after rollback.

Tracks should not be updated in production until the site-specific customizations are moved into a child theme or otherwise preserved.

## Final State After Follow-up

| Component | Final staging state |
|---|---:|
| PHP | 7.4.28 |
| WordPress core | 7.0.1 |
| Contact Form 7 | 6.1.6 |
| MetaSlider | 3.110.0 |
| Tracks theme | customized 1.60-era theme restored |
| What's New Generator | disabled; MU shortcode replacement active |

Final public checks returned 200:

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
- `/wp-json/contact-form-7/v1`

`/order-form/` still redirects to `/`, matching earlier staging behavior.

The temporary WordPress update maintenance PHP file was removed after verification.

## Follow-up: PHP Warning Fixes

Date: 2026-07-14 JST

The PHP warnings found after the WordPress 7.0.1/PHP 7.4 staging update were fixed on staging.

Backups created before the changes:

- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-warning-fix-page-light.php`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-warning-fix-page-design.php`
- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-warning-fix-class-ai1wm-updater.php`

### Customized Tracks Theme

Fixed undefined constant warnings caused by unquoted array keys:

- `/html/cms/wp-content/themes/tracks/page-light.php`
  - `$light_item1[id]` to `$light_item1['id']`
  - `$light_item2[id]` to `$light_item2['id']`
- `/html/cms/wp-content/themes/tracks/page-design.php`
  - `$design_item[id]` to `$design_item['id']`

### All-in-One WP Migration

Fixed the updater warning by initializing the plugin update transient before assigning to `$transient->response`.

File changed:

- `/html/cms/wp-content/plugins/all-in-one-wp-migration/lib/model/class-ai1wm-updater.php`

Note: this is a small compatibility patch to an old plugin version. It may be overwritten if All-in-One WP Migration is updated later.

### Warning Fix QA

Syntax checks passed for all changed PHP files before upload:

- `page-light.php`
- `page-design.php`
- `class-ai1wm-updater.php`

Post-fix checks returned 200:

- `/`
- `/light/`
- `/design/`
- `/wp-json/wp/v2/pages/58`
- `/wp-json/contact-form-7/v1`
- `/cms/wp-cron.php`

After the post-fix checks, the staging error log had no new `Tue Jul 14 07:` entries, so the previous theme and All-in-One WP Migration warnings did not recur during QA.

## Follow-up: PHP 7.4-Compatible Plugin Set

Date: 2026-07-14 JST

Production-only plugins were added to staging where an official WordPress.org package was available, then old staging plugins were updated to the highest selected PHP `7.4`-compatible versions.

### Version Selection Notes

- MW WP Form latest `5.1.4` requires PHP `8.0`.
- MW WP Form `5.0.4` was selected because its package metadata explicitly supports PHP `7.4`.
- `whats-new-genarator` was not reintroduced. The vulnerable plugin remains disabled and the MU shortcode replacement remains active.
- `sakura-rs-wp-ssl` is present at `1.4.0`, which is already the current package. It was not activated on staging because the staging site is HTTP and the plugin is SSL-environment-specific.
- `remove-query-strings-from-static-resources` no longer appeared through the plugin information API, but its WordPress.org ZIP package for `1.4` was still available and was installed for parity testing.

### Final Plugin Versions On Staging

| Plugin | Final staging version | Active on staging |
|---|---:|---|
| Advanced Custom Fields | 6.8.5 | yes |
| Akismet | 5.7 | no |
| All In One WP Security | 5.4.9 | yes |
| All-in-One WP Migration | 7.106 | yes |
| All-in-One WP Migration File Extension | 1.5 | yes |
| Autoptimize | 3.1.15.1 | yes |
| Classic Editor | 1.7.0 | yes |
| Contact Form 7 | 6.1.6 | yes |
| Disable Google Fonts | 2.0 | yes |
| Duplicate Post | 4.7 | yes |
| Easy FancyBox | 2.3.20 | no |
| ImageMagick Engine | 1.8.0 | yes |
| Invisible reCaptcha | 1.2.3 | yes |
| Limit Login Attempts | 1.7.2 | yes |
| MetaSlider | 3.110.0 | yes |
| MW WP Form | 5.0.4 | yes |
| Protect Uploads | 0.7.0 | yes |
| Raw HTML | 1.6.4 | yes |
| Remove Query Strings From Static Resources | 1.4 | yes |
| SAKURA RS WP SSL | 1.4.0 | no |
| SiteGuard WP Plugin | 1.8.7 | yes |
| TS Webfonts for SAKURA RS | 3.1.4 | yes |
| WP Multibyte Patch | 2.9.3 | no |
| zipaddr-jp | 1.43 | no |

### Backups Created

Updated plugins were backed up under `/usr/home/ai119lrhtx/html/_codex_update_backups/` before replacement. New backup names created during this pass include:

- `20260714-090812-advanced-custom-fields`
- `20260714-090822-akismet`
- `20260714-090835-all-in-one-wp-migration`
- `20260714-090848-classic-editor`
- `20260714-090857-duplicate-post`
- `20260714-090906-easy-fancybox`
- `20260714-090915-limit-login-attempts`
- `20260714-090923-raw-html`
- `20260714-090933-siteguard`
- `20260714-090941-wp-multibyte-patch`
- `20260714-090950-zipaddr-jp`

Plugins newly added to staging did not have previous staging directories to back up.

### Plugin Update QA

Each plugin install/update was followed by public smoke checks. Final checks returned 200:

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
- `/wp-json/contact-form-7/v1`

After the final checks, the staging error log had no new `Tue Jul 14 09:1` entries.

Note: an earlier activation attempt logged temporary `copy_dir()` redeclare fatals from the Codex maintenance script itself. The script was fixed by renaming the helper to `codex_copy_dir()`, the failed activations were retried, and the plugins activated successfully.

## Follow-up: Top Page NEWS Layout Fix

Date: 2026-07-14 JST

After replacing the old What's New Generator plugin with the MU shortcode replacement, the top page NEWS list rendered without the original plugin stylesheet. The shortcode output was present, but the date/title rows lost the original compact centered layout.

File changed on staging:

- `/html/cms/wp-content/mu-plugins/codex-showwhatsnew.php`

Backup created before the change:

- `/usr/home/ai119lrhtx/html/_codex_update_backups/20260714-news-layout-codex-showwhatsnew-before.php`

### Fix

The MU shortcode plugin now enqueues a small inline stylesheet matching the original `whats-new.css` layout rules:

- center the NEWS rows at `60%` width on desktop
- float the date column on desktop
- hide the separator `hr` elements
- keep white text/link behavior consistent with the original plugin

### NEWS Layout QA

Browser rendering on the staging top page confirmed:

- `.pc-top .whatsnew dl` computed width: `768px` at the default `1280px` viewport
- `.pc-top .whatsnew dl` margins: `256px` left and right
- `.pc-top .whatsnew dt` computed float: `left`
- date and title rows render horizontally in the compact centered layout again

Additional checks:

- `codex-showwhatsnew.php` passed PHP syntax check
- the staging top page includes `codex-showwhatsnew-inline-css`
- public smoke checks returned 200 for `/`, `/light/`, `/design/`, `/etc/`, `/recruit/`, `/profile/`, `/office/`, and `/information?cat=1`
- no PHP `Fatal error`, `Parse error`, `Warning:`, `Notice:`, or `Deprecated:` text appeared in the checked page bodies

The existing staging error log still contains older entries from before the prior warning fixes and temporary maintenance-script activation attempts, but the current theme files contain the fixed quoted array keys and the NEWS layout change did not introduce visible PHP errors during QA.

## Production Deployment

Date: 2026-07-14 JST
Target: https://rikusan.com/

The staging-tested update set was applied to production after the user confirmed production deployment.

### Production State Before

- PHP: `7.4.33`
- WordPress core: `5.0.22`
- Contact Form 7: `5.1.9`
- MetaSlider: `3.10.2`
- MW WP Form: `5.0.3`
- What's New Generator: active

### Production Changes Applied

- Updated WordPress core to `7.0.1`
- Ran the WordPress database upgrade
- Updated plugins to the selected PHP `7.4`-compatible versions used during staging validation
- Uploaded the NEWS MU shortcode compatibility plugin:
  - `/home/rikusan/www/cms/wp-content/mu-plugins/codex-showwhatsnew.php`
- Deactivated and file-disabled the old What's New Generator plugin
- Preserved production active-plugin state except for removing What's New Generator
- Confirmed the customized Tracks theme already had the quoted `['id']` PHP warning fix, so no additional theme upload was needed
- Removed the temporary production maintenance script after the work

### Production Backups Created

Backups were created under:

- `/home/rikusan/www/_codex_update_backups/`

Key backup names created during the production deployment include:

- `20260714-025621-wp-admin`
- `20260714-025621-wp-includes`
- `20260714-025734-advanced-custom-fields`
- `20260714-025737-akismet`
- `20260714-025740-all-in-one-wp-migration`
- `20260714-025745-all-in-one-wp-security-and-firewall`
- `20260714-025748-autoptimize`
- `20260714-025749-classic-editor`
- `20260714-025752-contact-form-7`
- `20260714-025753-disable-google-fonts`
- `20260714-025755-duplicate-post`
- `20260714-025758-easy-fancybox`
- `20260714-025800-imagemagick-engine`
- `20260714-025802-invisible-recaptcha`
- `20260714-025804-limit-login-attempts`
- `20260714-025814-ml-slider`
- `20260714-025828-mw-wp-form`
- `20260714-025831-protect-uploads`
- `20260714-025832-raw-html`
- `20260714-025834-remove-query-strings-from-static-resources`
- `20260714-025836-sakura-rs-wp-ssl`
- `20260714-025840-siteguard`
- `20260714-025842-ts-webfonts-for-sakura`
- `20260714-025844-wp-multibyte-patch`
- `20260714-025846-zipaddr-jp`
- `20260714-025908-whats-new-genarator`

The All-in-One WP Migration `storage` directory was restored into the updated plugin directory after replacement.

### Production State After

| Component | Final production state |
|---|---:|
| PHP | 7.4.33 |
| WordPress core | 7.0.1 |
| Contact Form 7 | 6.1.6 |
| MetaSlider | 3.110.0 |
| MW WP Form | 5.0.4 |
| What's New Generator | disabled and moved to backup |
| NEWS compatibility MU plugin | active |

Final active plugins on production:

- Advanced Custom Fields
- All-in-One WP Migration
- Classic Editor
- Contact Form 7
- Duplicate Post
- Invisible reCaptcha
- Limit Login Attempts
- MetaSlider
- MW WP Form
- Raw HTML
- SiteGuard WP Plugin

### Production QA

Final public checks returned 200 and did not show PHP `Fatal error`, `Parse error`, `Warning:`, `Notice:`, or `Deprecated:` text:

- `/`
- `/light/`
- `/design/`
- `/etc/`
- `/recruit/`
- `/profile/`
- `/office/`
- `/information?cat=1`
- `/wp-json/wp/v2/pages/58`
- `/wp-json/contact-form-7/v1`

The production top page confirmed:

- generator tag reports `WordPress 7.0.1`
- Contact Form 7 assets load with `ver=6.1.6`
- MetaSlider markup reports `ml-slider-3-110-0`
- `codex-showwhatsnew-inline-css` is present
- the old `whats-new-genarator` plugin no longer appears in the top page output

A 1280px-wide browser screenshot confirmed the NEWS layout renders as the compact centered date/title list. The direct FTP existence check confirmed the temporary production maintenance script was deleted after use.

The latest available production error log inspected after deployment did not show new PHP fatal or warning entries related to the update.

## Customer Harassment Policy: Staging Only

Date: 2026-07-14 JST
Target: http://rikusan.3d-showcase.net/customer-harassment-policy/

The supplied Word document was converted into a normal WordPress fixed page on staging. No custom page template was created.

### Production Rollback

An initial production content update was rolled back after confirming that production should not be changed before staging approval.

Rolled back on production:

- page ID `1331` was changed back to `draft`
- `footer.php` was restored from `/home/rikusan/www/_codex_update_backups/20260714-customer-harassment-footer-before.php`
- `style.css` was restored from `/home/rikusan/www/_codex_update_backups/20260714-customer-harassment-style-before.css`
- the temporary production page-creation script was removed

After rollback, production no longer shows the `customer-harassment-policy` footer link.

### Staging Page

- Title: `カスタマーハラスメントに対する基本方針`
- Slug: `customer-harassment-policy`
- URL: `http://rikusan.3d-showcase.net/customer-harassment-policy/`
- WordPress page ID: `1003`

The staging page was rebuilt using the privacy policy page layout as the base:

- `rikusan-guide privacy` content wrapper
- green underlined page heading
- section headings for items 1 through 5
- list styling aligned with the privacy policy page
- page-scoped responsive CSS in the fixed-page content to prevent mobile text clipping

### Staging Footer Link

The staging active theme footer is hardcoded in `footer.php`, so it is not controlled by a WordPress menu. A minimal footer link was added on staging only:

- Link text: `カスタマーハラスメント基本方針`
- Link URL: `/customer-harassment-policy/`

Staging files changed:

- `/html/cms/wp-content/themes/tracks/footer.php`
- `/html/cms/wp-content/themes/tracks/style.css`

Staging backups created before changes:

- `/html/_codex_update_backups/20260714-staging-customer-footer-before.php`
- `/html/_codex_update_backups/20260714-staging-customer-style-before.css`

The temporary staging page-creation script was removed after use.

### Staging QA

Final public checks returned 200 and did not show PHP `Fatal error`, `Parse error`, `Warning:`, `Notice:`, or `Deprecated:` text:

- `/`
- `/privacy/`
- `/customer-harassment-policy/`

Browser layout checks:

- 375px mobile: no document or text horizontal overflow
- 390px mobile: no document or text horizontal overflow
- 1280px desktop: no document or text horizontal overflow

Screenshots were saved under `work/screenshots/` for the staging customer harassment policy page.
