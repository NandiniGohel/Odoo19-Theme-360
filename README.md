<p align="center">
  <img src="static/description/banner.png" alt="BlueNova Backend Theme" width="100%">
</p>

# BlueNova Backend Theme

[![Odoo](https://img.shields.io/badge/Odoo-19.0-714B67.svg)](https://www.odoo.com/documentation/19.0/)
[![License](https://img.shields.io/badge/license-OPL--1-blue.svg)](https://www.odoo.com/documentation/19.0/legal/licenses.html)
[![Version](https://img.shields.io/badge/version-19.0.1.0.0-informational.svg)](__manifest__.py)

A minimalist, glassmorphic reskin of the Odoo 19 backend — deep royal blue and
electric blue accents, silver/chrome metallic gradients, soft multi-layer
shadows and glass panels — plus the pieces a visual theme usually leaves out: a
persistent app sidebar, an instant light/dark switch, themed
login/signup/reset-password screens, a KPI landing dashboard with a live chat
bubble, an optional public home page, and an in-app settings screen that
recolours the entire theme without touching SCSS or rebuilding assets.

Uninstall the module and Odoo returns to its default look. No business data is
modified.

---

## Table of contents

- [Highlights](#highlights)
- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration — Settings › Theme Settings](#configuration--settings--theme-settings)
- [Presets: export, import, reset](#presets-export-import-reset)
- [The landing dashboard](#the-landing-dashboard)
- [The chat bubble](#the-chat-bubble)
- [Authentication pages](#authentication-pages)
- [The optional public home page](#the-optional-public-home-page)
- [How the theming works](#how-the-theming-works)
- [Module layout](#module-layout)
- [Customising & extending](#customising--extending)
- [Technical reference](#technical-reference)
- [Uninstalling](#uninstalling)
- [License & credits](#license--credits)

---

## Highlights

**Visual**

- Deep Indigo / Royal Blue brand (`#3959b0`) with an Ocean Blue accent, silver
  chrome gradients and glossy metallic fills.
- Glassmorphic panels, cards, dropdowns and popovers; clean paper content area
  with subtle depth.
- Bundled **Inter** (UI/display) and **Poppins** (labels, numbers) latin-subset
  webfonts — shipped in-module, so nothing is fetched from Google Fonts.
- Restyles the whole web client: navbar, control panel, breadcrumbs, list, form,
  kanban, pivot, calendar, activity, dialogs, chatter, Discuss, notifications
  and the settings screen itself.
- Bundled single-colour app-icon set (~80 icons) painted through a CSS mask, so
  icon colour follows hover/active state.

**Behaviour**

- **Persistent apps sidebar** replacing Odoo's apps dropdown — every app the
  user can access, the current one highlighted, real `href`s so ctrl/middle
  click opens a new tab. Open/closed state remembered per browser.
- **Instant light/dark mode** from a navbar toggle. This is the theme's own
  scheme (one attribute on `<html>`), not Odoo's `color_scheme` cookie — no
  second asset bundle, no page reload.
- **Fully responsive**: width, height *and* input-type aware — hover language is
  fenced off from touch devices and re-expressed as `:active`.

**Configurable**

- ~50 colour pickers (light and dark), hero typography controls, uploadable
  login background images per scheme, a login tagline, and two feature toggles —
  all under **Settings › Theme Settings**, applied on save with **no asset
  rebuild and no server restart**.
- Pick *one* Primary and the whole brand ramp is re-derived — hover fills, focus
  rings, active nav rows, kanban washes, the dashboard hero gradient, plus
  Bootstrap's own `:root` brand variables. Same for Background: one pick
  produces the entire neutral ramp (surfaces, borders, scrollbars, ink).
- JSON preset **export / import / reset**.

---

## Requirements

| | |
|---|---|
| **Odoo** | 19.0 **Community** |
| **Depends on** | `web`, `base_setup` |
| **Enterprise** | Not tested — no Enterprise compatibility is claimed |
| **License** | OPL-1 (Odoo Proprietary License v1.0) |
| **Optional integrations** | `auth_signup` (signup/reset pages), `mail`/Discuss (chat bubble), `crm`, `sale`, `purchase`, `account`, `project`, `stock`, `hr` (dashboard tiles), `website` (the theme stands down on `/`) |

Nothing in the optional list is a dependency. Every feature that touches an
optional module is guarded twice — *is it installed* and *may this user read it*
— and degrades silently when either answer is no.

---

## Installation

1. Copy the `bluenova_backend_theme` folder into your addons path.
2. Restart the Odoo service.
3. **Apps → Update Apps List**, then search for *BlueNova Backend Theme* and
   click **Activate**.
4. Hard-refresh the browser once (the backend asset bundle is rebuilt on
   install).

The module is listed as an application (`'application': True`) so it gets its
own card under Apps, and `auto_install` is `False` — it never activates itself
just for sitting in the addons path.

> **Developer mode / asset issues:** if the theme looks half-applied after an
> upgrade, regenerate the bundles with
> `Settings → Technical → User Interface → Clear Assets`, or restart with
> `-u bluenova_backend_theme --dev=assets`.

---

## Configuration — Settings › Theme Settings

Go to **Settings** and open the **Theme Settings** entry in the left rail. The
screen is organised into five blocks:

| Block | Contains |
|---|---|
| **Presets & Assets** | Import Settings, Export Settings, Reset All Theme Settings |
| **Home Page** | Themed Public Home Page, Open Dashboard After Login, Hero Typography (title/lead size & weight) |
| **Authentication Pages** | Login panels & actions (light + dark), Semantic colours, Login branding (brand-image logo, tagline), Login background images (light + dark) |
| **Light Mode Colors** | Core colours, Top Bar, Sidebar Base, Active Menu, Home Page colours |
| **Dark Mode Colors** | The same five groups, for the dark scheme |

### What each colour group covers

**Core colours** — Primary, Background, Text, Muted Text, Text Hover, Button,
Button Text.
**Top Bar** — background, text, and the search box (text, placeholder, border,
background).
**Sidebar Base** — sidebar background.
**Active Menu** — the highlight on the app currently open.
**Home Page colours** — the public hero's headline and standfirst colours.
**Semantic colours** — Info, Success, Warning, Danger. Deliberately
scheme-agnostic (a danger red stays a danger red), so these have no dark twin
and are emitted into *both* schemes.

### Feature toggles

| Setting | Default | Effect |
|---|---|---|
| **Themed Public Home Page** | Off | Serves the theme's own page at `/` for anonymous visitors. Stands down automatically when `website` is installed. |
| **Open Dashboard After Login** | Off | Lands internal users on the themed dashboard instead of `/odoo`. Only rewrites the *default* landing URL — deep links, portal redirects and 2FA interstitials pass through untouched. |
| **Use Brand Image As Login Logo** | Off | Serves the stored brand image as the login logo instead of the company logo. |

Every boolean on this screen defaults to **off** by design:
`ir.config_parameter.set_param` deletes the row when handed `False`, so a
`config_parameter` boolean that defaulted to `True` could never be switched off.

### Hero typography bounds

| Field | Range |
|---|---|
| Title Size / Lead Size | 8–200 px |
| Title Weight / Lead Weight | 100–900 |

An emptied input (`0`) means *unset* — the shipped default takes over. Anything
outside the range is rejected on save with a clear error, so a fat-fingered
600px headline never reaches a stylesheet.

### Images

Three images are stored as `ir.attachment` records (filestore), **not** as
config parameters — a base64 PNG in `ir.config_parameter` would be dragged into
nearly every request:

| Attachment name | Used for |
|---|---|
| `bluenova_backend_theme.sidebar_brand_image` | Login logo, when *Use Brand Image As Login Logo* is ticked |
| `bluenova_backend_theme.login_background_image` | Login background (light) |
| `bluenova_backend_theme.login_background_image_dark` | Login background (dark) |

They are served through `/web/image/<id>?unique=<timestamp>`, so replacing an
image busts the browser cache automatically. The attachments are created with
`public: True` — the login page has no authenticated user to read them
otherwise.

---

## Presets: export, import, reset

**Export Settings** downloads `bluenova_theme_settings.json`: a small, diffable,
committable file describing the *whole* palette — both schemes plus the hero
type scale. Images are deliberately excluded.

```json
{
  "_module": "bluenova_backend_theme",
  "_version": 1,
  "colors": {
    "theme_color_primary": "#3959b0",
    "theme_color_primary_dark": "#7c9aff",
    "theme_color_background": "#f8f9fa",
    "...": "..."
  },
  "metrics": {
    "theme_home_title_size": "59",
    "theme_home_title_weight": "800",
    "theme_home_lead_size": "15",
    "theme_home_lead_weight": "500"
  }
}
```

**Import Settings** opens a file-picker wizard (`bluenova.theme.import.wizard`)
that validates everything *before* writing anything — unreadable JSON, unknown
keys, malformed colours and out-of-range numbers are each reported with a
specific message, and a rejected preset leaves the live theme untouched. A key
that is absent or empty is imported as *unset*, so a preset fully describes a
theme rather than patching one.

**Reset All Theme Settings** deletes every stored colour, metric, flag and
uploaded image, returning the theme to the values compiled into the stylesheets.

Both import and reset finish with Odoo's `reload` client action, so the new look
is on screen immediately.

---

## The landing dashboard

An app in its own right (**Dashboard**, `sequence="1"`, so it sits first in the
app rail) and optionally the post-login landing page.

Data comes from `bluenova.theme.dashboard` — an `AbstractModel`, because the
dashboard stores nothing, so there is no table and no ACL row to grant. **Every
figure is counted as the requesting user, never `sudo`**, so record rules apply
exactly as they do in the list view each tile links to: a salesperson's numbers
and an accountant's numbers are each their own.

One RPC (`get_dashboard_data`) builds six independent regions:

| Region | Contents |
|---|---|
| `heroes` | The two largest figures, on gradient cards, with a 30-day-vs-previous-30-days trend line |
| `tiles` | The remaining figures as metric cards, with a 7-day sparkline or a pair of status pills |
| `chart` | A 30-day, one-bar-per-day column chart, up to 4 selectable series (one per app) |
| `activity` | The newest records across everything readable ("New quotation: S00042 · 2h ago") |
| `quick_actions` | "New …" shortcuts, one per creatable model |
| `preview` | A short list of the user's *own* open work |

Each region degrades to empty on its own. A database with only `web` installed
gets the time-of-day greeting and an empty state.

### Tiles shipped

| App | Model | Figure |
|---|---|---|
| CRM | `crm.lead` | Open pipeline (opportunities) |
| Sales | `sale.order` | Quotations awaiting confirmation |
| Purchase | `purchase.order` | RFQs not yet ordered |
| Invoicing | `account.move` | Posted customer invoices |
| Project | `project.task` | Tasks still open (via `stage_id.fold`, so it follows the manager's own configuration) |
| Inventory | `stock.picking` | Transfers to process, split IN / OUT |
| Employees | `hr.employee` | Currently active |

Preview panel candidates, in preference order: **my open tasks → my pipeline →
my quotations** — first one that is installed, readable and non-empty wins.

Every domain is the *needs-attention* slice rather than a grand total (a number
that never changes is not worth a card). Historical series use a separate,
state-free `history` domain — counting "still draft" backwards through time
would drop older records in proportion to their age and bend every chart the
same way.

---

## The chat bubble

A floating button on the dashboard opens a two-view card:

- **list** — the user's Discuss conversations, in Discuss's own order, with the
  last message under each name (8 rows).
- **thread** — one conversation: its last 30 messages and a working composer.

Messages are posted as real `message_type="comment"` comments, which means
**OdooBot answers** (`mail_bot._apply_logic` runs in the same transaction) and
new messages arrive live over the bus. "Open Discuss" hands over to the real
application, on the open conversation when there is one.

What it is not: attachments, mentions, reactions, editing, sub-threads or typing
indicators. Those are Discuss, one click away.

The bubble renders **only where Discuss is installed** — and `mail` is still not
a dependency. Nothing imports from `@mail/…` (an import of a module absent from
the bundle would blank the *entire* backend, not just hide a feature).
Availability is answered by asking the actions registry whether Discuss's client
action is registered; live updates come from `bus_service` looked up in
`env.services`, simply absent where it is not installed. Posted bodies are
capped at 4000 characters as a guard on the RPC entry point.

---

## Authentication pages

Login, signup and reset-password are all themed from a single inherit of
`web.login_layout` (the one template all three `t-call`), which is why
`auth_signup` is not a dependency — if it happens to be installed, its pages
pick the styling up for free.

The inherit **replaces the layout's `t-call` child at priority 30**,
deliberately mirroring what `website` does at priority 20. Without that, an
instance with `website` installed silently lost the theme on the login screen:
website's `replace` takes out core's entire card subtree, `o_bluenova_auth_page`
never reaches `<body>`, and every rule in `auth_pages.scss` is scoped to a class
that is no longer on the page. Both structures now end in the same place, and
the login screen looks identical with or without `website`.

Also on these pages:

- Dark mode honoured **before first paint**, from the same `localStorage` key
  the backend writes — set by a tiny inline script, because the frontend JS
  bundle is deferred and would land after the page was already painted light.
- Optional per-scheme background image, optional brand-image logo, optional
  tagline.
- `no_header` / `no_footer` stay `True`, so a website's chrome does not appear
  around them.

---

## The optional public home page

A themed page at `/` for anonymous visitors, built on `web.frontend_layout` —
which is why it costs no new dependency and no new asset bundle.

It takes over only when **all four** conditions hold, in order:

1. a database is resolved (`/` is reachable before `ensure_db()` has ever run);
2. nobody is logged in (a signed-in user hitting `/` still wants the web
   client);
3. the admin switched it on (silently changing what `/` serves on a running
   instance is not a theming decision);
4. `website` is **not** installed — that module owns `/` properly, with an
   editable page behind it, so the theme stands down.

The honest trade-off: there is no drag-and-drop editing here. The copy is the
company's own data plus the tagline from Theme Settings. Anything richer is what
`website` is for.

---

## How the theming works

Four layers, each solving a problem the one before it cannot reach.

### 1. Sass brand variables — build time

`static/src/scss/primary_variables.scss` is **prepended** to
`web._assets_primary_variables`, not appended. Core's own
`primary_variables.scss` declares `$o-brand-primary` and friends with
`!default` and then derives a dozen variables from them on the spot; loading
after it would win the assignment and lose every derivation.

### 2. Design tokens — the `--cmt-*` custom properties

`static/src/scss/variables.scss` declares the whole palette on `:root`, and
every other stylesheet in the module reads tokens rather than literals. The dark
scheme is one attribute on `<html>` (`data-cmt-theme="dark"`) that
`dark_mode.scss` keys on — set on the *document* element so dialogs, popovers
and tooltips appended to `<body>` are covered too.

### 3. Runtime CSS — the settings screen

Saved colours are rendered as a `<style>` block into the page head:

- backend — `views/theme_styles.xml`, appended to `head_web` in
  `web.webclient_bootstrap`, so it lands **after** every compiled bundle and
  wins the cascade at equal specificity;
- login/signup/reset — `views/auth_theme.xml`;
- public home — `views/public_home.xml`.

Rendered on every page load straight from `ir.config_parameter`, so a save is on
screen the moment the page comes back: **no bundle rebuild, no restart, nothing
cached to invalidate.** When nothing has been customised the methods return `""`
and an untouched install carries no extra markup at all.

The three blocks are scoped so they can never fight:

| Block | Selector |
|---|---|
| Light colours | `:root:not([data-cmt-theme="dark"])` |
| Dark colours | `:root[data-cmt-theme="dark"]` |
| Hero type scale | bare `:root` (a type scale is not part of a palette and must survive the dark switch) |

**Security:** every colour is matched against
`^(#[0-9A-Fa-f]{3,8}|rgba?\([\d\s.,%]+\))$` before it is written *and* the
metrics are range-checked, at both entry points (settings save and preset
import). Without that, an admin-only CSS injection is one hop away — `#fff; }
html { display: none } :root {` is a defacement, and a `url()` is an outbound
request carrying the user's referrer. Values are returned as
`markupsafe.Markup` so `t-out` emits them unescaped, which is safe precisely
because of the validation above.

### 4. Colour derivation — `models/theme_color.py`

A brand colour is not one value, it is a *family*. Picking a Primary re-derives:

- the theme's own ramp — `--cmt-primary-dark` (hover/pressed),
  `--cmt-primary-light` (active rows, list hover), `--cmt-primary-soft` (focus
  rings), `--cmt-on-primary-container`, `--cmt-primary-rgb`;
- the **accent** family — held to the brand's own hue, moving only lightness and
  saturation, because rotating the hue is exactly how a violet brand ends up
  with a blue dashboard hero;
- the **sidebar icon rail's** hover/active tints;
- **Bootstrap's `:root` brand variables** — `--primary`, `--link-color-rgb`,
  `--primary-bg-subtle` and friends, emitted in *both* the prefixed (`--bs-*`)
  and unprefixed spellings, because Odoo compiles Bootstrap with
  `$variable-prefix: ''` and emitting one spelling would make the whole block
  inert, silently, on half the versions this theme supports.

Picking a Background derives the full neutral ramp — surface, dim, highest,
border, scrollbar thumb, outline, muted text, ink, the glass fills and
Bootstrap's neutrals — anchored on *paper* (the lightest surface) rather than on
the canvas, so a mid-green canvas still gets a near-white sheet, a hairline a
shade under it and readable ink. The ramp direction follows the *picked colour*,
not which block is being emitted, so a dark colour chosen as the light scheme's
Background correctly flips the ink and lifts the surfaces.

Two design commitments worth knowing:

- **Work in HSL, not channel mixing.** Mixing toward black desaturates as it
  goes, turning a vivid green into muddy olive at the hover state.
- **An untouched install must be byte-identical to what it was.** The
  multipliers are *fitted*: fed `#3959b0` they reproduce `variables.scss`
  exactly; fed `#7c9aff` and `#0b1120` they reproduce `dark_mode.scss` exactly.
  Where a family cannot be fitted (the accent's shipped 24° hue rotation), the
  derivation is **skipped entirely** while the picker still holds its shipped
  value.

Two consequences of the same principle, both intentional:

- A picker still holding the value it shipped with is treated as *the absence of
  a choice*, not a choice — it is emitted as the alias `variables.scss` declares
  for it, so picking a green Primary also moves the Save button, the active app
  and the login action instead of leaving them frozen indigo.
- The light scheme gets a conditional **tint layer** (mirroring
  `dark_mode.scss`'s "Layer 3") rendered from Python only when a Background has
  actually been picked. Odoo compiles many surfaces from Sass literals no custom
  property can reach; a permanent rule in the bundle would repaint an untouched
  install, so the block only exists once someone has picked a canvas.

---

## Module layout

```
bluenova_backend_theme/
├── __manifest__.py                     # deps, data, asset bundles (heavily annotated)
├── controllers/
│   └── main.py                         # BlueNovaHome: post-login landing + public `/`
├── models/
│   ├── res_config_settings.py          # the settings screen, runtime CSS, presets
│   ├── theme_color.py                  # HSL colour derivation (brand, accent, neutrals)
│   └── theme_dashboard.py              # bluenova.theme.dashboard + chat RPCs
├── wizard/
│   ├── theme_import_wizard.py          # validating JSON preset importer
│   └── theme_import_wizard_views.xml
├── security/
│   └── ir.model.access.csv             # import wizard, base.group_system only
├── views/
│   ├── theme_styles.xml                # runtime <style> into the backend head
│   ├── auth_theme.xml                  # login / signup / reset layout (priority 30)
│   ├── public_home.xml                 # the optional page at `/`
│   ├── home_dashboard_actions.xml      # client action + app menu
│   └── res_config_settings_views.xml   # Settings › Theme Settings
└── static/
    ├── description/                    # Apps Store icon, banner, index.html, screenshots, videos
    └── src/
        ├── fonts/                       # Inter + Poppins (woff2, latin subset) + OFL.txt
        ├── image/icons/                 # ~80 single-colour app icons
        ├── js/
        │   ├── theme_mode.js            # light/dark reactive store + <html> attribute
        │   ├── apps_sidebar_state.js    # open/closed reactive store
        │   ├── apps_sidebar.js          # the AppsSidebar component
        │   ├── apps_sidebar_patch.js    # mounts it on WebClient, patches NavBar
        │   ├── home_dashboard.js        # the HomeDashboard client action
        │   └── chat_launcher.js         # the floating chat panel
        ├── scss/                        # 18 stylesheets — see the load-order note below
        └── xml/                         # OWL templates for the three components
```

### SCSS load order (from `__manifest__.py`)

Order is load-bearing and the manifest documents why for each entry:

```
fonts → variables → base → navbar → apps_sidebar → control_panel → kanban
  → stats_banner → home_dashboard → chat_launcher → settings_page
  → buttons_misc → brand_bridge → responsive → dark_mode
```

- `home_dashboard.scss` must follow `apps_sidebar.scss` and `base.scss` — it
  uses mixins they define, and a bundle compiles as one SCSS document.
- `brand_bridge.scss` comes after every component stylesheet. It is what makes
  the Primary picker work in *light* mode: dark mode already restates core's
  surfaces in `--cmt-*` tokens, but core's light surfaces are compiled literals
  baked from `$o-brand-primary` at build time, which no runtime custom property
  can reach. Drop it and the picker silently works in one scheme only.
- `responsive.scss` is second-to-last (every file above it states the desktop
  case), and `dark_mode.scss` is last (it only swaps colour and must keep
  winning at every size).

Frontend bundle (`web.assets_frontend`) gets only four files — `fonts`,
`variables`, `auth_pages`, `public_home`. `dark_mode.scss` is deliberately
excluded: it is written against backend DOM that does not exist there, so the
two auth stylesheets carry their own `:root[data-cmt-theme="dark"]` blocks for
the surfaces they own.

---

## Customising & extending

### Change a shipped default colour

Edit `static/src/scss/variables.scss` (light) and/or
`static/src/scss/dark_mode.scss` (dark), then upgrade the module.

⚠️ **The shipped ramp and the derived ramp have to agree.** The multipliers in
`models/theme_color.py` are fitted to reproduce `variables.scss` when fed
`#3959b0`. Change a default by hand and it belongs in *both* places, or in
neither.

### Add a new colour picker

Three places, in this order:

1. `static/src/scss/variables.scss` — declare the token under *Settings-driven
   tokens* (and its dark counterpart in `dark_mode.scss`).
2. `models/res_config_settings.py` — add the `fields.Char(..., widget="color")`
   with a `config_parameter`, then map it in `_THEME_CSS_VARS` (and
   `_THEME_CSS_VARS_DARK` for the dark twin). The map is explicit rather than
   derived from field names on purpose: the tokens predate the settings screen
   and do not follow one naming rule, and silently mapping to a token nothing
   consumes produces a picker that appears to work and changes nothing.
3. `views/res_config_settings_views.xml` — add the row to the right `<block>`.

Adding it to `_THEME_CSS_VARS` is also what gets it validated, exported, imported
and reset — `_all_color_fields()` is the single source for all four.

### Add an app icon to the sidebar

Drop a single-colour PNG/SVG in `static/src/image/icons/` and add an entry to
`ICON_BY_MODULE` in `static/src/js/apps_sidebar.js`, keyed on the **module** part
of the app's xmlid (`crm.crm_menu_root` → `crm`) — matching on the module rather
than the displayed name keeps the mapping working in every language.
`ICON_BY_XMLID` handles the case where one module owns several app roots
(`base` owns both Apps and Settings); `ICON_BY_NAME_FIRST` exists for the one
case where only the display name distinguishes two apps (Invoicing vs
Accounting share `account.menu_finance`).

Apps with no entry fall back to their own Odoo icon, then to `custom.png` — a
row never borrows another app's artwork, and an icon that fails to decode is
re-rendered onto the placeholder rather than showing a broken-image glyph.

### Add a dashboard tile

Append a spec to `_TILES` in `models/theme_dashboard.py`:

```python
{
    "key": "helpdesk",
    "model": "helpdesk.ticket",
    "label": "Tickets",
    "singular": "ticket",
    "sub": "Open",
    "icon": "help desk.png",
    "domain": [("stage_id.is_close", "=", False)],
    "history": [],                       # state-free: what the record *is*
    "action": "helpdesk.helpdesk_ticket_action_main_tree",
    "pills": [...],                      # optional
}
```

`history` matters: keep only the clauses saying what *kind* of record this is and
drop the ones saying what state it is in today, or the sparkline and trend will
sag to the left on a database where nothing changed.

---

## Technical reference

### Models

| Name | Type | Purpose |
|---|---|---|
| `res.config.settings` | inherit | ~50 colour fields, 4 metrics, 3 flags, 3 images; runtime CSS; export/import/reset |
| `bluenova.theme.dashboard` | `AbstractModel` | `get_dashboard_data`, `get_chat_threads`, `get_chat_messages`, `post_chat_message` |
| `bluenova.theme.import.wizard` | `TransientModel` | Validating JSON preset importer (`base.group_system` only) |

### Controller overrides (`web.Home`)

| Method | Behaviour |
|---|---|
| `_login_redirect` | Rewrites **only** the default `/odoo` landing to `/odoo/action-<id>` when *Open Dashboard After Login* is on. Runs after 2FA and after the session is established. A missing action logs a warning and falls back rather than opening an "Undefined action" dialog. |
| `index` | Serves the themed public page at `/` under the four conditions listed above; otherwise `super()`. |

### Config parameters

All keys are prefixed `bluenova_backend_theme.` — e.g. `color_primary`,
`color_background_dark`, `home_title_size`, `landing_dashboard`,
`public_home_enabled`, `login_use_brand_image`, `login_tagline`.

### Browser storage

| Key | Values | Meaning |
|---|---|---|
| `cmt_color_scheme` | `light` \| `dark` | Colour scheme, per browser |
| `cmt_apps_sidebar_open` | `true` \| `false` | Sidebar state, per browser (open unless explicitly closed) |

Both reads and writes are wrapped in `try/catch` — private browsing with storage
disabled falls back to the default and the toggle still applies for that page.

### DOM hooks

| Hook | Where |
|---|---|
| `data-cmt-theme="dark"` | `<html>` — the entire dark palette hangs off this one attribute |
| `body.cmt-has-sidebar` | Both sides of the sidebar layout (the rail is fixed-positioned; restructuring Odoo's `.o_action_manager > .o_action > .o_content { overflow: auto }` scroll chain is what stops views scrolling) |
| `body.o_bluenova_auth_page` | Login / signup / reset |
| `body.o_bluenova_public_home` | The public page |

### Tunables

| Constant | Value | File |
|---|---|---|
| `SPARK_DAYS` | 7 | `theme_dashboard.py` |
| `TREND_WINDOW` / `CHART_WINDOW` | 30 / 30 | `theme_dashboard.py` |
| `CHART_SERIES` | 4 | `theme_dashboard.py` |
| `CHAT_THREADS` / `CHAT_MESSAGES` / `CHAT_BODY_MAX` | 8 / 30 / 4000 | `theme_dashboard.py` |
| `COLOR_RE`, `METRIC_BOUNDS` | — | `res_config_settings.py` |

`CHART_WINDOW` is `TREND_WINDOW` deliberately: the chart and the "+12% vs
previous 30 days" line then describe the same period, so a reader comparing them
is comparing like with like.

### Responsive breakpoints (`responsive.scss`)

Bootstrap's, so the theme changes shape exactly where core does
(`ui.isSmall` is `size <= SM`, the same 768px as `d-md-*`):

| Range | Target |
|---|---|
| ≥1600 | Wide / ultrawide desktop |
| ≥1200 | Desktop |
| 992–1199 | Small desktop, large tablet landscape |
| 768–991 | Tablet |
| 576–767 | Large phone |
| <576 | Phone |

Plus two axes width alone cannot catch: **height** (a phone held landscape is a
*wide* viewport, so anything eating vertical space stands down under ~500px) and
**input** (`hover: none` / `pointer: coarse` — a `:hover` style on a touchscreen
latches after the tap and has to be tapped away, so hover language is re-expressed
as `:active`).

---

## Uninstalling

**Apps → BlueNova Backend Theme → Uninstall.** Odoo returns to its default look
immediately. The theme's config parameters and its three attachments are the
only things it ever wrote — no business record is touched, and nothing needs
migrating.

---

## License & credits

**Odoo Proprietary License v1.0 (OPL-1)** —
<https://www.odoo.com/documentation/19.0/legal/licenses.html>.
OPL-1 rather than LGPL-3 because Odoo requires paid Apps Store modules to carry
it.

The bundled **Inter** and **Poppins** webfonts remain under the **SIL Open Font
License 1.1** (see [`static/src/fonts/OFL.txt`](static/src/fonts/OFL.txt)), which
permits redistribution of the font files inside a proprietary work.

| | |
|---|---|
| **Author / Maintainer** | Strats360 Technolabs-LLP |
| **Website** | <https://strats360.com/> |
| **Support** | <nirav@wewant360.com> — include your Odoo version, the module version and a description of the problem |

Further reading: [`README.rst`](README.rst) ·
[`static/description/index.html`](static/description/index.html) (Apps Store
listing, with screenshots and demo videos).
