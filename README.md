<p align="center">
  <img src="static/description/banner.png" alt="BlueNova Backend Theme" width="100%">
</p>

<h1 align="center">BlueNova Backend Theme</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Odoo-19.0%20Community-714B67" alt="Odoo 19.0 Community">
  <img src="https://img.shields.io/badge/license-OPL--1-blue" alt="OPL-1">
  <img src="https://img.shields.io/badge/data%20model-no%20new%20tables-success" alt="No new stored data models">
</p>

<p align="center">
  Modernize your Odoo 19 Community backend with a persistent app sidebar, an instant light/dark
  mode, a KPI landing dashboard with a floating Discuss chat panel, themed login and public pages,
  and an in-app Settings screen that recolours the entire web client without an SCSS edit or an
  asset rebuild.<br>
  Technical name: <code>bluenova_backend_theme</code>
</p>

---

## 1. Overview

**BlueNova Backend Theme** is a backend (web client) theme for **Odoo 19 Community Edition**. It
restyles the standard Odoo web client and adds a small number of opt-in backend features on top of
it, without replacing or reimplementing any of Odoo's own views.

**What it solves.** The stock Odoo backend hides every app behind a dropdown, offers no per-instance
branding short of recompiling SCSS, has no landing page of its own, and — on Community, where
`$enable-dark-mode` is compiled off — has no working dark scheme. BlueNova addresses those four
things specifically.

**What it does *not* do.** It does not fork or replace Odoo's list, form, kanban, calendar, pivot or
graph views. Those remain Odoo's own components, rendered by Odoo's own code; the theme supplies
colour, spacing, elevation and typography around them. Every business record you see through
BlueNova is the same record, read through the same ORM, subject to the same access rights and record
rules.

**Reversible by construction.** The module defines **no new stored business data**. Its only
persisted state is a `TransientModel` (the preset-import wizard), a set of `ir.config_parameter`
rows holding the chosen palette, and up to three `ir.attachment` images. Uninstalling removes all of
it and the backend returns to stock Odoo.

| | |
|---|---|
| Target platform | Odoo 19 Community Edition |
| Module type | Backend / web client theme (`category: Themes/Backend`) |
| Odoo dependencies | `web`, `base_setup` |
| Stored business models | none |
| License | OPL-1 (Odoo Proprietary License v1.0) |

---

## 2. Key Features

Everything in this section is present in the source tree and registered in
[`__manifest__.py`](__manifest__.py). Where a capability is narrower than its name suggests, the
limit is stated rather than omitted — see also [Known limitations](#known-limitations).

### 2.1 Modern backend UI

- **Design tokens.** One set of `--cmt-*` CSS custom properties drives every colour, radius, shadow
  and font in the theme, declared in [`static/src/scss/variables.scss`](static/src/scss/variables.scss).
  Nothing in the theme hardcodes a colour twice.
- **Shipped palette.** Deep Indigo `#3959b0` primary with an Ocean Blue `#0284c7` accent family over
  tiered white surfaces. Both are overridable per-instance from Settings (§2.5).
- **Shape & elevation.** Rounded corners, soft multi-layer shadows, and glassmorphic panels, cards
  and dropdowns.
- **Typography.** Inter for UI text, Poppins for labels and numerics — both bundled in-module as
  latin-subset `woff2` files under [`static/src/fonts/`](static/src/fonts/), so the backend makes no
  `fonts.googleapis.com` request and nothing breaks offline or under a strict CSP.
- **Surfaces restyled.** Top navbar (through Odoo's own `--NavBar-*` custom properties), control
  panel as a page header, kanban boards, list and form chrome, dropdowns, dialogs, popovers,
  notifications, the Settings screen, and buttons.

### 2.2 Persistent app sidebar

- Replaces Odoo's apps dropdown with an always-visible rail listing **every app the user can reach**
  — built from the same `menuService.getApps()` the dropdown uses, so it inherits Odoo's own menu
  access filtering. The current app is marked with an accent spine.
- **62 bundled app icons** ([`static/src/image/icons/`](static/src/image/icons/)), single-colour
  artwork painted through a CSS mask so one icon carries every state (rest, hover, active).
- Icons are matched by the **module part of the app's xmlid** (`crm.crm_menu_root` → `crm`) in
  [`static/src/js/apps_sidebar.js`](static/src/js/apps_sidebar.js), so the mapping keeps working in
  every language. Two narrower maps handle the cases the module alone cannot resolve: an exact-xmlid
  map (`base` owns both Apps and Settings) and a display-name map. An app with no match — and an app
  whose icon fails to decode — falls back to the bundled `custom.png` placeholder.
- Toggled from a grid button in the navbar; the open/closed state is remembered per browser
  (`cmt_apps_sidebar_open` in `localStorage`).
- Hidden below the `md` breakpoint, where Odoo's own slide-in app menu takes over untouched.
- Implemented as an OWL component mounted into `WebClient.components`, with the mount point added by
  a QWeb template inherit — see [`static/src/xml/apps_sidebar.xml`](static/src/xml/apps_sidebar.xml)
  and [`apps_sidebar_patch.js`](static/src/js/apps_sidebar_patch.js). No core file is modified.

### 2.3 Light / dark mode

- Switched from the sidebar footer, applied instantly, remembered per browser (`cmt_color_scheme` in
  `localStorage`).
- The entire dark palette hangs off a single `data-cmt-theme` attribute on `<html>`, so dialogs,
  popovers and tooltips — which Odoo portals out to `<body>` — are covered too.
- **This is a separate mechanism from Odoo's own dark mode** (the `color_scheme` cookie and the
  `web.assets_web_dark` bundle). Odoo's switch rebuilds a second asset bundle server-side and reloads
  the page; this one flips an attribute, with no server round-trip and no bundle rebuild. It is also
  what makes a dark scheme available at all on Community, where Bootstrap is compiled with
  `$enable-dark-mode: false` and the `[data-bs-theme=dark]` block therefore never exists.
- [`static/src/scss/dark_mode.scss`](static/src/scss/dark_mode.scss) is loaded last in the backend
  bundle and re-tints every surface the theme owns, plus the main list / form / kanban / dialog
  chrome.

### 2.4 Dashboard

An optional landing page registered as the **`bluenova_dashboard` client action**
([`views/home_dashboard_actions.xml`](views/home_dashboard_actions.xml)), with its own root menu so
it appears as an app in the rail. The client is an OWL component
([`home_dashboard.js`](static/src/js/home_dashboard.js) + [`home_dashboard.xml`](static/src/xml/home_dashboard.xml));
all data comes from a single RPC into the `bluenova.theme.dashboard` **AbstractModel**
([`models/theme_dashboard.py`](models/theme_dashboard.py)) — an abstract model on purpose, because
the dashboard stores nothing.

| Region | What it shows |
| --- | --- |
| **Hero cards** | The two largest figures, on the brand gradient, with a 30-day-vs-previous-30-day trend line where there is a baseline to compare against (absent, not `+0.0%`, when there is not) |
| **Metric cards** | The remaining figures — open opportunities, quotations, RFQs, posted customer invoices, open tasks, transfers to process, active employees — each with a seven-day sparkline, and IN/OUT pills on Transfers |
| **Trend chart** | A column per day over the last 30 days, **one app at a time**; chips above the plot switch between the apps that saw activity, and hovering a column reads out that day |
| **Recent activity** | The four newest records across everything the user can read; a row opens that record |
| **Quick actions** | "New …" shortcuts, one per model the user may **create**, plus Settings for an administrator |
| **Preview** | The user's own open tasks — falling back to their pipeline, then their quotations — with *View All* into the underlying action |
| **Chat launcher** | A floating bubble; see §2.6 |

**The chart is hand-rolled, not a charting library.** Geometry is computed in
`home_dashboard.js` (`chartMax`, `chartBars`, `chartTicks`, `chartAxis`) and handed to the template
as percentages, so bars are laid out by the CSS grid in
[`home_dashboard.scss`](static/src/scss/home_dashboard.scss) and reflow with the panel. Only one
series is plotted at a time — "quotations created" and "transfers created" are counts of different
things and would need two y-scales.

Throughout:

- a card, panel or row is **dropped silently** if its model is not installed or the current user
  cannot read it, so every region degrades on its own and a bare database falls through to a written
  empty state;
- records are counted **as the current user**, never `sudo()`, so record rules apply exactly as they
  do in the underlying list view;
- every colour is a `--cmt-*` token — including the hero gradients, derived from the brand ramp — so
  the page follows whatever is picked under Settings, in both schemes;
- anything counted **by creation date** (the hero trend, the sparklines, the chart, the activity
  feed) is counted against a tile's `history` domain rather than its needs-attention `domain`, so a
  quotation confirmed since it was written stays in the history it belongs to. Without that split,
  older buckets come back systematically emptier and the chart sags left while the trend reports
  growth on a database where nothing changed;
- it can optionally be set as the page a user lands on right after login
  ([`controllers/main.py`](controllers/main.py) hooks `Home._login_redirect`, and only when super()
  returned the default `/odoo` — an explicit `?redirect=`, a portal user's landing page, or a 2FA
  interstitial is passed through untouched).

### 2.5 Settings → Theme Settings

A full panel ([`views/res_config_settings_views.xml`](views/res_config_settings_views.xml), backed by
[`models/res_config_settings.py`](models/res_config_settings.py)) that recolours the theme without an
SCSS edit or an asset rebuild.

- **Colour pickers** for the core palette, top bar, search box, sidebar, active-app highlight, the
  semantic colours (info / success / warning / danger) and the auth pages — each with a separate
  dark-mode counterpart where the surface needs one.
- **Primary repaints the whole brand family, not one token.** Hover/pressed fills, the active-row and
  list-hover wash, focus rings, the sidebar app-icon tints, the accent family behind the hero
  gradients and kanban washes, and Bootstrap's own `--bs-primary` / `--bs-link-color` — which paint
  Odoo's badges, links, progress bars and pagination. All derived at page load by
  [`models/theme_color.py`](models/theme_color.py), **in HSL** so the hue holds and the ramp stays
  coordinated for any brand (mixing toward black desaturates, turning a vivid green into a muddy
  olive at the hover state).
- **Background drives the neutrals the same way.** A pick re-derives the whole neutral ramp —
  surfaces, hairlines, scrollbars, ink, `--cmt-bg-rgb` — plus Bootstrap's `:root` neutrals and a
  conditional tint layer for the surfaces Odoo compiles from Sass literals. The ramp **flips
  direction on a dark pick**: choose a near-black canvas for light mode and the ink goes light with
  it. How far panels travel toward the canvas scales with how much colour is in it.
- **Fitted to reproduce the shipped theme exactly.** Fed `#f8f9fa` the ramp reproduces
  `variables.scss`; fed `#0b1120` it reproduces `dark_mode.scss`. A picker still holding the value it
  shipped with is treated as *unset* rather than as a choice, so an untouched install is byte-for-byte
  the theme as designed.
- **`brand_bridge.scss` covers core's compiled literals by role.** Core spells its brand six ways
  (`$o-brand-primary`, `$o-action`, `$o-brand-odoo`, `$o-enterprise-action-color`, `$primary`,
  `$o-component-active-bg`) and derives washes from them inline with `mix()` / `tint-color()` /
  `rgba()` — none of it reachable from a custom property.
  [`brand_bridge.scss`](static/src/scss/brand_bridge.scss) collapses all of it onto the theme's own
  tokens by role, covering selection surfaces, links, focus rings, form fields, the kanban and
  **calendar** renderers, the properties and colour-picker widgets, Discuss/chatter, and the loading
  indicator.
- **Uploadable images** — a sidebar brand image and a login background image (light and dark), stored
  as `ir.attachment` records rather than `ir.config_parameter`, so no base64 payload rides along on
  every request.
- **Hero typography controls** (title/lead size and weight) for the public home page, emitted on a
  bare `:root` because a type scale is not part of a palette.
- **Export / Import / Reset** — download the palette as a JSON preset, re-import it later (validated
  field-by-field by [`wizard/theme_import_wizard.py`](wizard/theme_import_wizard.py) before anything
  is written), or reset to the compiled defaults in one click.
- **Every colour is validated twice** — against a strict hex/`rgb()`/`rgba()` pattern on save *and*
  again before it is rendered, because the value is interpolated into a `<style>` block served to
  every user and nothing guarantees the stored row came through `set_values()` rather than a shell
  session or a data file.
- Changes apply on save via a page reload — no asset-bundle rebuild, no server restart.

### 2.6 Chat / communication

A floating chat launcher on the dashboard
([`chat_launcher.js`](static/src/js/chat_launcher.js) + [`chat_launcher.xml`](static/src/xml/chat_launcher.xml),
server side in `get_chat_threads` / `get_chat_messages` / `post_chat_message`).

**This is a functional chat, not a mock-up or a static UI.** It reads and writes real
`discuss.channel` data:

- the **conversation list** shows the channels the user has pinned in Discuss — the same rows, in the
  same order (`last_interest_dt desc`), with the last message preview, relative time and unread count;
- picking a row opens that **conversation inside the card**: its recent messages (up to 30, oldest
  first), authors and avatars, self/other bubble sides;
- the **composer posts into the channel** via `message_post` with `message_type="comment"`. Because
  that runs `mail_bot` in the same transaction, OdooBot's reply is already written by the time the
  server hands the thread back — question and answer arrive in one round trip;
- **new messages arrive live** over `bus_service` (`discuss.channel/new_message`), while the panel is
  open;
- opening a conversation **marks it read**, exactly as opening it in Discuss does;
- *Open Discuss* in the header hands over to the real Discuss client action, on the open conversation.

**What it deliberately is not:** attachments, mentions, reactions, message editing, sub-threads and
typing indicators are not implemented. Those are Discuss, they are one click away, and each is a
component this file would have to reimplement against `@mail/…` imports it must not take.

**It requires Discuss and is absent — not disabled — without it.** The theme depends only on `web`
and `base_setup`, so nothing in it imports from `@mail/…`: importing a module that is not in the
bundle takes the *whole backend bundle* down rather than costing one feature. Everything mail-shaped
is reached by name at runtime instead — the client asks the actions registry whether
`mail.action_discuss` is registered, `bus_service` is looked up in `env.services` rather than through
`useService`, and the three server methods are guarded by the same "model exists + user may read"
pair as every dashboard tile.

**Authorisation.** Reading and posting are gated on one question, asked as the current user: does
this user have a `discuss.channel.member` row for this conversation? That is the same search
Discuss's own controllers run. It is what lets the post itself use `sudo()` — the elevation covers
the rows an ordinary member cannot write directly (the message, their seen pointer, the channel's
`last_interest_dt`), not the decision about whether they may. Message bodies are `html_sanitize`d on
the way out and posted text goes through `plaintext2html`, capped at 4000 characters.

### 2.7 Calendar

**BlueNova does not add a calendar view, a calendar component, or a calendar action.** Odoo 19's own
calendar view — a FullCalendar **v6** renderer — is used unchanged, with all of its own day / week /
month / year scales, filters, drag-and-drop and quick-create intact.

What the theme contributes is **styling of that standard view**, in three places:

1. **Dark mode** ([`dark_mode.scss`](static/src/scss/dark_mode.scss), ~180 lines). FullCalendar v6
   exposes a `--fc-*` custom property set that v4 lacked, so the palette is remapped at the source —
   `--fc-page-bg-color`, `--fc-border-color`, `--fc-neutral-bg-color`, `--fc-highlight-color`,
   `--fc-non-business-color`, `--fc-more-link-*`, `--fc-now-indicator-color` — rather than chased
   surface by surface. A handful of v6 surfaces that ignore those properties (the all-day strip,
   disabled/weekend cells, week numbers, the time-grid divider, column headers, list-view rows, the
   "+N more" popover) are restated by hand.
2. **Brand colour** ([`brand_bridge.scss`](static/src/scss/brand_bridge.scss)) — the dropdown option,
   the "+N more" link, the month/year A/B toggle, and the ghost event and drag mirror while an event
   is being dragged.
3. **Settings-driven tints** ([`res_config_settings.py`](models/res_config_settings.py)) — the
   calendar sidebar, disabled cells, week numbers, scroller and popover surfaces follow a picked
   Background through the conditional light-mode tint layer.

Anything beyond that — event card layouts, custom scales, extra calendar sections — is **not
implemented**, and this documentation does not claim it.

### 2.8 Card-based UI components

The card is the theme's main layout primitive. Every card below exists in the source:

| Card | Where | What it carries |
|---|---|---|
| **Hero card** | Dashboard band 1 | Brand-gradient card, app glyph, label chip, large figure, trend line or subtitle |
| **Metric card** | Dashboard band 2 | Masked app icon, label, figure, seven-day sparkline, and either IN/OUT pills or a subtitle |
| **Activity card** | Dashboard band 1 | Panel of newest records — icon, "New *kind*: *name*", relative time; a row opens the record |
| **Chart panel** | Dashboard band 3 | Series chips, running total, gridlines with y-ticks, day columns with hover tooltips, three-label x-axis |
| **Quick-action card** | Dashboard band 4 | Icon + "New …" label, one per creatable model |
| **Preview card** | Dashboard band 4 | The user's own open work — marker, name, secondary line — with *View All* |
| **Chat card** | Floating, bottom-right | Header with back/open/close, conversation rows or message bubbles, auto-growing composer |
| **Workspace card** | Sidebar header | Active company and database |
| **Kanban card** | Odoo's kanban views | Glassmorphic fill with an accent spine — a restyle of Odoo's own card, not a replacement ([`kanban.scss`](static/src/scss/kanban.scss)) |

Every one of them is built from `--cmt-*` tokens and therefore follows the Settings palette in both
colour schemes.

### 2.9 Themed login, signup and public pages

- The login, signup and reset-password screens pick up the same palette, plus an optional brand logo
  and a tagline set from Settings ([`views/auth_theme.xml`](views/auth_theme.xml),
  [`auth_pages.scss`](static/src/scss/auth_pages.scss)).
- They **keep that styling when the `website` module is installed**. `website` replaces the whole
  `web.login_layout` body with its own layout, which would otherwise drop the theme's card, logo and
  body class; this module's inherit runs at priority 30, after website's 20, and rebuilds the themed
  layout either way. The website header and footer stay off these three pages — remove the
  `no_header` / `no_footer` lines in `auth_theme.xml` to keep them.
- An **optional themed public page at `/`** for anonymous visitors ([`views/public_home.xml`](views/public_home.xml)).
  Off by default, and it stands down automatically when `website` is installed, since that module
  owns `/` properly. It is served by an override of `Home.index` that checks four conditions in
  order: a database is resolved, nobody is logged in, the admin turned it on, and `website` is absent.

### 2.10 Responsive behaviour

Implemented in [`static/src/scss/responsive.scss`](static/src/scss/responsive.scss) (573 lines), and
verifiable there:

| Query | Purpose |
|---|---|
| `min-width: 1600px` | Wide-screen layout — the dashboard grids gain columns |
| `max-width: 1199.98px` | Below `xl` |
| `max-width: 991.98px` | Below `lg` — tablet |
| `max-width: 767.98px` | Below `md` — the app rail steps aside for Odoo's own slide-in menu |
| `max-width: 575.98px` | Below `sm` — phone |
| `max-height: 500px and (orientation: landscape)` | Short landscape viewports |
| `hover: none` / `pointer: coarse` | Touch devices — hover-only affordances and larger hit areas |
| `prefers-reduced-motion: reduce` | Motion is dropped for users who ask for it |
| `print` | Print stylesheet |

The breakpoints mirror Bootstrap's, which is what Odoo 19 itself uses.

---

## 3. Screenshots / Preview

The module currently ships **two images**, both real files in
[`static/description/`](static/description/):

### Banner

![BlueNova Backend Theme banner](static/description/banner.png)

### App icon

![BlueNova app icon](static/description/icon.png)

> **No UI screenshots are bundled yet.** Rather than reference filenames that do not exist, the
> screenshots that *should* be captured are listed below. Add them under
> `static/description/screenshots/` and they can then be linked from this section and from
> `static/description/index.html`.
>
> | Suggested file | What to capture |
> |---|---|
> | `dashboard-light.png` | The `bluenova_dashboard` landing page in light mode — heroes, metric cards, chart, quick actions |
> | `dashboard-dark.png` | The same page with `data-cmt-theme="dark"` |
> | `sidebar.png` | The app rail open, with an app active, next to the navbar toggle |
> | `chat-launcher.png` | The floating chat panel, conversation list and an open thread |
> | `theme-settings.png` | Settings → Theme Settings, with the colour pickers and Export/Import/Reset |
> | `calendar-dark.png` | Odoo's standard calendar view under the theme's dark palette |
> | `kanban.png` | A kanban board with the glassmorphic card restyle |
> | `login.png` | The themed login page, with logo and tagline |
> | `responsive.png` | The backend at a phone width, with Odoo's slide-in menu in place of the rail |

---

## 4. Technical Architecture

```
bluenova_backend_theme/
├── __init__.py                        # imports controllers, models, wizard
├── __manifest__.py                    # depends, data, asset-bundle registration
├── README.md
├── README.rst
├── controllers/
│   └── main.py                        # opt-in login-redirect & public-home hooks on web.Home
├── models/
│   ├── res_config_settings.py         # Theme Settings fields, validation, runtime CSS, presets
│   ├── theme_color.py                 # HSL colour maths: brand, accent and neutral ramps
│   └── theme_dashboard.py             # AbstractModel behind the dashboard + chat RPCs
├── wizard/
│   ├── theme_import_wizard.py         # validates & applies an uploaded JSON preset
│   └── theme_import_wizard_views.xml
├── security/
│   └── ir.model.access.csv            # ACL for the transient import wizard only
├── views/
│   ├── theme_styles.xml               # renders the saved palette into the backend page head
│   ├── auth_theme.xml                 # login / signup / reset-password styling & branding
│   ├── public_home.xml                # optional themed page at `/`
│   ├── home_dashboard_actions.xml     # dashboard client action + root menu
│   └── res_config_settings_views.xml  # the Theme Settings panel
└── static/
    ├── description/                   # app card: icon.png, banner.png, index.html
    └── src/
        ├── fonts/                     # Inter + Poppins, latin subset (OFL 1.1)
        ├── image/icons/               # 62 bundled single-colour app icons
        ├── scss/
        │   ├── primary_variables.scss # PREPENDED to web._assets_primary_variables
        │   ├── variables.scss         # ← every design token lives here
        │   ├── fonts.scss             # @font-face, bundled not CDN
        │   ├── base.scss              # canvas, scrollbars, shared mixins
        │   ├── navbar.scss            # top bar, via --NavBar-* properties
        │   ├── apps_sidebar.scss
        │   ├── control_panel.scss
        │   ├── kanban.scss
        │   ├── stats_banner.scss      # loaded, but its component is dormant — see below
        │   ├── home_dashboard.scss
        │   ├── chat_launcher.scss
        │   ├── settings_page.scss
        │   ├── buttons_misc.scss
        │   ├── brand_bridge.scss      # core's compiled brand literals → var(--cmt-*)
        │   ├── responsive.scss        # device rules; after every desktop rule above
        │   ├── dark_mode.scss         # LAST; overrides every backend surface above
        │   ├── auth_pages.scss        # login / signup / reset (frontend bundle)
        │   └── public_home.scss       # the public `/` page (frontend bundle)
        ├── js/
        │   ├── theme_mode.js              # light/dark reactive store + <html> attribute
        │   ├── apps_sidebar_state.js      # open/closed rail reactive store
        │   ├── apps_sidebar.js            # the rail component + icon mapping
        │   ├── apps_sidebar_patch.js      # WebClient.components + NavBar toggle handler
        │   ├── chat_launcher.js           # floating chat panel
        │   └── home_dashboard.js          # dashboard client action
        └── xml/
            ├── apps_sidebar.xml           # AppsSidebar + WebClient/NavBar inherits
            ├── home_dashboard.xml         # HomeDashboard
            └── chat_launcher.xml          # ChatLauncher
```

### Directory purposes

| Path | Purpose |
|---|---|
| `controllers/` | Two opt-in overrides on `web`'s own `Home` controller. Both fall through to the original behaviour when their setting is off. |
| `models/` | The Settings screen and its colour arithmetic, plus the dashboard/chat data layer. None of the three defines a stored table. |
| `wizard/` | The transient model behind Import Preset. |
| `security/` | One ACL row, for that transient model. There is nothing else to grant access to. |
| `views/` | QWeb templates and records. `theme_styles.xml` is the one that injects the runtime palette. |
| `static/src/scss/` | The stylesheets, in a load order the manifest documents inline and depends on. |
| `static/src/js/` | OWL 2 components and two module-level `reactive()` stores. |
| `static/src/xml/` | OWL templates, plus `t-inherit` extensions of `web.WebClient` and `web.NavBar`. |

### <a name="dormant-code"></a>Dormant code

**None.** Every file in the tree is registered in the manifest and reaches the browser or the ORM.

This section used to list six unregistered files. They were removed before the Odoo Apps Store
submission, because shipping code that is never loaded is a review liability rather than a feature:

| Removed | Why |
|---|---|
| `static/src/js/crm_pipeline_stats.js`, `crm_pipeline_stats_patch.js`, `static/src/xml/crm_pipeline_stats.xml` | A CRM pipeline stat banner. The patch imported `@crm/views/crm_kanban/crm_kanban_renderer`; an import of a module absent from the bundle is a load-time failure of the **whole** backend bundle, so enabling it without adding `crm` to `depends` produced a blank web client rather than a missing banner. |
| `views/crm_lead_views.xml` | A CRM kanban card restyle, xpath-ing into `crm.crm_case_kanban_view_leads` — a view from a module this theme does not depend on. |
| `views/assets.xml` | Odoo 13/14-era asset-bundle templates, superseded by the manifest `assets` dict in 15.0+. It also referenced `odoo13_compat.scss`, which does not exist, so loading it could only ever fail. |
| `static/src/js/shared_state.js` | An OWL 1 subscription helper (`const { hooks } = owl`) that would throw under OWL 2. Superseded by `reactive()` in `apps_sidebar_state.js` and `theme_mode.js`. |

`stats_banner.scss` and the `.o_cmt_card_*` blocks in `kanban.scss` / `responsive.scss` were
deliberately **left in place**. They are inert CSS selectors that match no markup, they cost about
2&nbsp;KB in a ~950&nbsp;KB bundle, and stripping them would mean editing two working stylesheets
for no functional gain.

---

## 5. Odoo Integration

The theme integrates through Odoo 19's supported extension points only. **No Odoo core file is
modified.**

### Manifest & dependencies

```python
'depends': ['web', 'base_setup']
'application': True      # gets its own card under Apps, with an Activate button
'auto_install': False    # dropping it in the addons path changes nothing until activated
```

Two dependencies, deliberately. `crm`, `mail`, `bus`, `sale`, `stock`, `project`, `hr`, `account`,
`purchase` and `website` are all *optional* — every feature that touches them is guarded at runtime.

### Asset bundles

| Bundle | Contents |
|---|---|
| `web._assets_primary_variables` | `primary_variables.scss`, **prepended**. Core's own `primary_variables.scss` declares `$o-brand-primary` with `!default` and immediately derives a dozen variables from it; loading *after* it would win the assignment and lose every derivation. |
| `web.assets_backend` | 17 SCSS files in a documented order (tokens → surfaces → brand bridge → responsive → dark mode), 6 JS modules, 3 QWeb template files. |
| `web.assets_frontend` | 4 SCSS files only — `fonts`, `variables`, `auth_pages`, `public_home`. The login and public pages render through `web.frontend_layout`, a different bundle, so none of the backend stylesheets reach them. `dark_mode.scss` is deliberately excluded: it is written against backend DOM that does not exist there. |

The SCSS load order is load-bearing and commented inline in the manifest — `home_dashboard.scss`
must follow `apps_sidebar.scss` and `base.scss` for their mixins, `brand_bridge.scss` must follow
every file that paints a surface itself, `responsive.scss` must follow every desktop rule, and
`dark_mode.scss` must be last.

### OWL components (Odoo 19 web framework)

| Component | Registered as |
|---|---|
| `HomeDashboard` | `registry.category("actions").add("bluenova_dashboard", …)` — an OWL client action, which in Odoo 19 gets no control panel unless it renders one |
| `AppsSidebar` | Added to `WebClient.components`, mounted by a `t-inherit` on `web.WebClient` |
| `ChatLauncher` | A child component of `HomeDashboard` |

All three use OWL 2 idioms: `useState`, `useService`, `useRef`, `useEffect`, `useExternalListener`,
`onWillStart`, `onWillUnmount`, module-level `reactive()` stores, and `t-key` on every `t-foreach`.

### View / template inheritance

- `web.WebClient` — `<xpath expr="//NavBar" position="after">` adds `<AppsSidebar/>`, inside the same
  `t-if` so both stand down in fullscreen mode.
- `web.NavBar` — `<xpath expr="//t[@t-call='web.NavBar.AppsMenu']" position="after">` adds the rail
  toggle button.
- `NavBar.prototype` — patched with `@web/core/utils/patch` for the toggle handler and its pressed
  state.
- `res.config.settings` — a standard `_inherit`, with the panel added by an inherited form view.
- `web.login_layout` and friends — inherited at priority 30 in `auth_theme.xml`.
- `web.Home` — the controller is subclassed; `index` re-uses the parent routing with a bare
  `@http.route()`.

### The runtime CSS path

Saved colours take a higher-priority path than the bundle. `views/theme_styles.xml` renders
`_get_theme_css()`, `_get_theme_css_dark()` and `_get_theme_metrics_css()` as a small `<style>` block
in the page `<head>`, **after** the compiled bundle, so it wins the cascade at equal specificity —
and it is regenerated straight from `ir.config_parameter` on every page load, with no bundle rebuild.
The light block is scoped `:root:not([data-cmt-theme="dark"])` and the dark block
`:root[data-cmt-theme="dark"]`, so the two can never both match and neither has to outrank the other.
`auth_theme.xml` and `public_home.xml` render the same blocks for the unauthenticated pages.

### How underlying functionality is preserved

- No Odoo view, model or controller is *replaced* — everything is an inherit, a patch, an
  `xpath position="after"`, or a subclass calling `super()`.
- The dashboard is a **signpost**, not a second place records are managed: every click hands over to
  the owning app's own action (`action.doAction`).
- The app rail is built from `menuService.getApps()`, so it shows exactly the apps Odoo would.
- All counts and reads run as the current user, so record rules and access rights apply unchanged.
- The theme adds no field to any business model and no column to any table.

---

## 6. Installation

The technical name is **`bluenova_backend_theme`**, and the directory must be named exactly that —
every asset path in the manifest is an absolute reference to it.

1. **Copy the module into an addons directory** on your Odoo 19 server:

   ```bash
   cd /path/to/odoo/custom-addons
   git clone https://github.com/NandiniGohel/odoo_theme_backend.git bluenova_backend_theme
   ```

   Make sure that directory is listed in `addons_path` in your `odoo.conf`.

2. **Restart the Odoo service**, so the new module is picked up:

   ```bash
   ./odoo-bin -c odoo.conf
   ```

3. **Enable Developer Mode** — Settings → General Settings → Developer Tools → *Activate the developer
   mode*. (Needed only to reach *Update Apps List*; skip it if the app is already listed.)

4. **Update the Apps List** — Apps → ⋮ → *Update Apps List*.

5. **Search for "BlueNova"** in Apps. The module is flagged `application: True`, so it gets its own
   card rather than hiding behind the *Extra* filter.

6. **Activate** it. It never installs itself (`auto_install: False`).

7. **Refresh the backend** (a hard reload, `Ctrl`/`Cmd` + `Shift` + `R`) so the new asset bundle is
   fetched.

Or install from the command line:

```bash
./odoo-bin -c odoo.conf -d <database> -i bluenova_backend_theme
```

Then open **Settings → Theme Settings** to recolour, upload a brand image, or enable the landing
dashboard and public home page.

### While developing

SCSS is compiled server-side and cached in `ir.attachment`. To see edits without restarting:

```bash
./odoo-bin -c odoo.conf --dev=all
```

Otherwise regenerate the bundles (Settings → Technical → Regenerate Assets Bundles) and hard-refresh.
**Adding a new file to the manifest's `assets` dict requires a service restart**, not just a bundle
regeneration.

---

## 7. Requirements

| | |
|---|---|
| **Odoo** | 19.0 |
| **Edition** | Community Edition (see §8) |
| **Python** | Whatever your Odoo 19 installation requires — the module adds **no** Python package dependency. It imports only from the standard library (`base64`, `colorsys`, `json`, `logging`, `re`, `datetime`) and from `odoo` / `markupsafe`, both already present in any Odoo install. There is no `external_dependencies` key in the manifest. |
| **Odoo dependencies** | `web`, `base_setup` |
| **Frontend dependencies** | **None.** No npm package, no CDN, no external font or script request. Fonts are bundled `woff2`; icons are bundled PNG/SVG; the dashboard chart is hand-rolled CSS, not a charting library. |
| **Optional at runtime** | `mail` + `bus` (chat launcher), `crm` / `sale` / `purchase` / `account` / `project` / `stock` / `hr` (dashboard tiles), `website` (changes login-page and `/` behaviour). Every one of these is guarded — absent means the feature is absent, never broken. |
| **Browser** | Any evergreen browser that Odoo 19 itself supports (see §8) |

---

## 8. Compatibility

### Odoo

**Odoo 19.0 Community Edition** is the target this module is written and documented against. Every
version-sensitive decision in the source is written for 19 specifically:

- client actions are OWL components in the `actions` registry, not legacy `AbstractAction`s;
- the web client is routed on real paths (`/odoo/action-42`), not on the hash fragment;
- `has_access` is used rather than the `check_access_rights(..., raise_exception=False)` deprecated
  in 18.0;
- `Home` is imported from `odoo.addons.web.controllers.home` (split out of `main.py` in 16.0);
- FullCalendar **v6** class names and `--fc-*` custom properties are used, not v4's;
- `t-out` with `Markup` is used, not the `t-raw` removed in 15.0;
- assets are declared in the manifest `assets` dict, not as XML bundle templates.

Nothing in the module depends on an Enterprise-only module or feature — `depends` is `web` and
`base_setup`, and `brand_bridge.scss` maps `$o-enterprise-action-color` only as one more compiled
literal to redirect. **Enterprise has not been verified**, so no Enterprise claim is made here.

Odoo versions **other than 19.0 are not supported**. The Odoo 13/14-era leftovers that used to sit
in the tree have been removed — see [Dormant code](#dormant-code).

### Browsers

The theme adds no browser requirement beyond Odoo 19's own. It uses CSS custom properties, `grid`,
`flex`, `mask-image`, `backdrop-filter` and `:is()` — all supported in current Chrome, Edge, Firefox
and Safari. `backdrop-filter` is what produces the glassmorphic panels; where it is unsupported those
surfaces fall back to a solid fill and remain fully legible.

### Responsive

Yes — and it is verifiable in [`responsive.scss`](static/src/scss/responsive.scss), which carries
explicit rules for wide screens, `xl`/`lg`/`md`/`sm` breakpoints, short landscape viewports, touch
pointers, reduced motion, and print. See §2.10 for the table.

### Accessibility notes

Click targets on the dashboard are real `<button>` elements, so they are keyboard-reachable for free.
Decorative icons carry `aria-hidden`; the chart carries `role="img"` and a written summary rather
than thirty focusable columns; the chat panel is a labelled `role="dialog"` with `Escape` stepping
back one level at a time and returning focus to the button that opened it.

---

## 9. Customization

### 9.1 Recolour from the UI (recommended)

**Settings → Theme Settings** covers every token that has a picker, with Export / Import / Reset built
in — no code change and no asset rebuild. This is the intended path for per-instance branding. See
§2.5.

### 9.2 SCSS — the compiled defaults

For anything without a picker, every visual decision is a token in
[`static/src/scss/variables.scss`](static/src/scss/variables.scss):

```scss
:root {
    --cmt-primary: #3959b0;          // brand + primary actions
    --cmt-tertiary: #0284c7;         // accent: hero gradients, won washes, counters
    --cmt-bg: #f8f9fa;               // the main canvas
    --cmt-surface: #ffffff;          // cards, navbar, panels
    --cmt-text: #111827;
    --cmt-font-sans: 'Inter', …;
}
```

The dark palette is the same token set redeclared under `:root[data-cmt-theme="dark"]` in
[`dark_mode.scss`](static/src/scss/dark_mode.scss) — edit the two blocks in parallel and both modes
stay in step.

Three caveats:

- Any token the Settings screen also exposes a picker for is **overridden at runtime** by a saved
  value. The SCSS default is what a fresh install shows.
- `--cmt-on-primary` — the ink on every brand fill (`.btn-primary`, the outline button's filled
  states, the `Enterprise` pill, the hero card, the login action) — is **not** derived from Primary.
  It stays the scheme's own: `#ffffff` in light, `#0b1120` in dark. Picking Primary moves fills, not
  ink. Override it per scheme with the **Button Text** / **Button Text (Dark)** pickers, which is
  also where to go if a deliberately pale Primary needs dark labels.
- The brand *shades* (`--cmt-primary-dark/-light/-soft/-rgb`, `--cmt-on-primary-container`,
  `--cmt-app-icon-hover/-active`) and the neutral ramp are the compiled defaults **until** Primary or
  Background is picked, after which they are derived. `theme_color.py`'s multipliers are fitted to
  reproduce `variables.scss` and `dark_mode.scss` exactly when fed the shipped values — so a
  hand-edited default belongs in both places or in neither.

`$o-brand-primary` in [`primary_variables.scss`](static/src/scss/primary_variables.scss) is a
**build-time** value and cannot follow a picker; it is what a fresh install compiles against. The
runtime path to core's brand is Bootstrap's `:root` custom properties plus `brand_bridge.scss`.

### 9.3 Navigation — add an icon for your own app

Drop a single-colour PNG/SVG on a transparent background into
[`static/src/image/icons/`](static/src/image/icons/), then map it in
[`static/src/js/apps_sidebar.js`](static/src/js/apps_sidebar.js) by the module part of the app's
xmlid:

```js
const ICON_BY_MODULE = {
    my_module: "my-icon.svg",
    // …
};
```

Matching on the module rather than the display name keeps the mapping working in every language. Use
`ICON_BY_XMLID` when one module owns several app menus, and `ICON_BY_NAME` for an app installed
without an xmlid. Anything unmatched falls back to `custom.png`.

Adding a *file* to the icons directory needs no manifest change (it is served from `static/`), but it
does need a browser refresh.

### 9.4 Dashboard — add a tile

Tiles are plain data in the `_TILES` list in
[`models/theme_dashboard.py`](models/theme_dashboard.py) — model, label, singular noun, subtitle,
icon, domain and the action to open on click, plus optional `pills` for an IN/OUT breakdown. A new
entry inherits the same access and `try/except` guards, so a tile for a model that is not installed,
or that the viewing user cannot read, is dropped rather than shown broken.

One entry feeds five regions: it is a candidate for a hero card (the two largest figures win), a
metric card, a series on the chart, the activity feed, and a quick action.

> **If the tile's `domain` filters on *state* rather than on what the record *is*, give the entry a
> `history` domain with the state clauses removed.** That is the domain every by-creation-date
> reading uses. Without it, records leave their own history behind as they progress — which does not
> merely lose rows, it loses more of them the further back you look, sagging the chart to the left
> and reporting growth in the trend line on a database where nothing changed.

The preview panel has its own list, `_PREVIEW_SOURCES`, in preference order — the first source that
is installed, readable and non-empty wins. Module-level constants tune the windows: `SPARK_DAYS`,
`TREND_WINDOW`, `CHART_WINDOW`, `CHART_SERIES`.

### 9.5 Cards and layout — SCSS

| To restyle | Edit |
|---|---|
| Hero, metric, chart, activity, quick-action and preview cards | [`home_dashboard.scss`](static/src/scss/home_dashboard.scss) |
| The chat bubble and its panel | [`chat_launcher.scss`](static/src/scss/chat_launcher.scss) |
| Kanban cards and columns | [`kanban.scss`](static/src/scss/kanban.scss) |
| The app rail | [`apps_sidebar.scss`](static/src/scss/apps_sidebar.scss) |
| Top navbar | [`navbar.scss`](static/src/scss/navbar.scss) |
| Control panel / breadcrumbs | [`control_panel.scss`](static/src/scss/control_panel.scss) |
| Buttons, badges, chips | [`buttons_misc.scss`](static/src/scss/buttons_misc.scss) |
| Breakpoints and touch/print rules | [`responsive.scss`](static/src/scss/responsive.scss) |
| The dark scheme for any of the above | [`dark_mode.scss`](static/src/scss/dark_mode.scss) |

### 9.6 OWL components and QWeb templates

| To change | Edit |
|---|---|
| Dashboard markup | [`static/src/xml/home_dashboard.xml`](static/src/xml/home_dashboard.xml) |
| Dashboard behaviour, chart geometry | [`static/src/js/home_dashboard.js`](static/src/js/home_dashboard.js) |
| Chat panel markup | [`static/src/xml/chat_launcher.xml`](static/src/xml/chat_launcher.xml) |
| Chat panel behaviour | [`static/src/js/chat_launcher.js`](static/src/js/chat_launcher.js) |
| Chat server methods, limits | `get_chat_threads` / `get_chat_messages` / `post_chat_message` in [`theme_dashboard.py`](models/theme_dashboard.py) |
| Sidebar markup + WebClient/NavBar inherits | [`static/src/xml/apps_sidebar.xml`](static/src/xml/apps_sidebar.xml) |

Any new `.js` or `.xml` file must be added to `web.assets_backend` in the manifest **and the service
restarted** before it loads.

### 9.7 Calendar

There is no calendar component to customise. To restyle Odoo's standard calendar view, edit the
`.o_calendar_renderer` / `--fc-*` block in [`dark_mode.scss`](static/src/scss/dark_mode.scss) for the
dark scheme, or the calendar rules in [`brand_bridge.scss`](static/src/scss/brand_bridge.scss) for
brand colour. See §2.7.

---

## 10. Upgrade / Maintenance Notes

### Version compatibility

This module is written against Odoo **19.0** and its version string is `19.0.1.0.0`. Do not install
it on another major version: the OWL client-action registration, the `/odoo/action-<id>` routing, the
`has_access` API, the `Home` import path, the FullCalendar v6 selectors and the manifest `assets`
dict are all 19-specific. Porting to a future version means re-checking each of those.

### Asset updates

- Editing an **existing** SCSS/JS/XML file: clear the ORM cache or run with `--dev=all`; a bundle
  regeneration is enough.
- Adding a **new** file to the manifest `assets` dict: **restart the Odoo service.** The manifest is
  read at load time, so a new entry is invisible until then.
- After either, hard-refresh the browser — bundles are cached aggressively.

### Module upgrade

`-u bluenova_backend_theme` reloads the `data` XML and regenerates the bundles. Because the module
owns no stored business model, there is **no migration to write** and no data to convert. Saved
colours live in `ir.config_parameter` and survive an upgrade untouched; images live in
`ir.attachment` and do the same.

### View-inheritance considerations

Every template extension is an `xpath` against a core anchor. When Odoo 19 ships a patch release that
renames one of them, the inherit fails loudly at upgrade time. The anchors currently relied on are:

| File | Anchor |
|---|---|
| `apps_sidebar.xml` | `//NavBar` in `web.WebClient`; `//t[@t-call='web.NavBar.AppsMenu']` in `web.NavBar` |
| `auth_theme.xml` | `web.login_layout` and the auth templates, at priority 30 |
| `res_config_settings_views.xml` | `res_config_settings.view_general_configuration` |

If a CRM kanban card restyle is ever reinstated, use surgical xpaths rather than replacing
`<t t-name="card">`: sibling views — notably `crm.crm_lead_view_kanban_forecast` — xpath into that
card's internals, and a wholesale replace breaks them at install time.

### Do not modify Odoo core

Every rule in this theme lands through a supported extension point: an appended asset bundle, a
`t-inherit`, an `xpath`, `patch()` on a prototype, a model `_inherit`, or a controller subclass
calling `super()`. **Never edit files under `odoo/addons/`** — a core edit is lost on the next
`git pull` and makes this theme unsupportable.

The three-layer strategy the theme uses, in order of preference:

1. **Odoo's own custom properties** — `--NavBar-*`, `--Kanban-*` and friends are redefined rather
   than overridden, so core keeps control of layout and the theme supplies only colour.
2. **Bootstrap runtime variables** — retargeted at `:root` and component level, in both the
   unprefixed (`--primary`) and prefixed (`--bs-primary`) spellings, because Odoo's
   `bootstrap_overridden.scss` sets `$variable-prefix: ''` and emitting one spelling alone is
   silently inert on half the versions this theme claims to support.
3. **Direct overrides** — last resort, for surfaces Odoo compiles straight into SCSS literals that no
   runtime variable can reach.

### Staying compatible with Odoo updates

- Prefer adding a token to `variables.scss` over hardcoding a colour.
- Prefer a new `_TILES` entry over a new RPC.
- Keep `mail` / `bus` / `crm` reached by name at runtime rather than imported — an import of an
  absent module takes down the whole backend bundle, not one feature.
- Re-run the fit check after touching `theme_color.py`: fed `#3959b0` / `#f8f9fa` it must reproduce
  `variables.scss`, and fed `#7c9aff` / `#0b1120` it must reproduce `dark_mode.scss`.

### <a name="known-limitations"></a>Known limitations

- Odoo's slide-in app menu on small screens (below `md`) is left as-is.
- **Graph views stay light on purpose.** Odoo draws chart axis labels onto the canvas from JS, taking
  the colour from a cookie that Community pins to `"light"` server-side. Darkening the panel would
  put near-black text on a near-black background, so the chart is given an explicit light card
  instead.
- Dark mode covers this theme's surfaces plus the main list / form / kanban / calendar / dialog
  chrome. Deeper corners — some reports, iframes and third-party widgets — can still show light
  patches.
- The chat panel is not a full Discuss client: no attachments, mentions, reactions, message editing,
  sub-threads or typing indicators. See §2.6.
- No custom calendar view is provided. See §2.7.
- The public home page has no drag-and-drop editing; it renders the company's own data plus the
  Settings tagline. Anything richer is what the `website` module is for, and this theme stands down
  automatically once `website` is installed.
- The CRM pipeline stat banner and kanban card restyle are present in the tree but not loaded. See
  [Dormant code](#dormant-code).
- No UI screenshots are bundled yet. See §3.

### Uninstall

Apps → BlueNova Backend Theme → Uninstall. The backend returns to stock Odoo immediately. The module
owns no business models or records — only the transient import wizard, the `ir.config_parameter` rows
holding the saved palette, and the `ir.attachment` rows holding the uploaded images, all of which go
with it.

---

## 11. Credits

**BlueNova Backend Theme**

| | |
|---|---|
| Author | Strats360 Technolabs-LLP |
| Company | Strats360 Technolabs-LLP |
| Maintainer | Strats360 Technolabs-LLP |
| Website | <https://strats360.com/> |
| Odoo compatibility | **Odoo 19 Community Edition** |
| Version | 19.0.1.0.0 |

**Official Odoo 19 documentation**

- Documentation home — <https://www.odoo.com/documentation/19.0/>
- Discover the JavaScript framework — <https://www.odoo.com/documentation/19.0/developer/tutorials/discover_js_framework.html>
- View records reference — <https://www.odoo.com/documentation/19.0/developer/reference/user_interface/view_records.html>
- Backend development tutorial — <https://www.odoo.com/documentation/19.0/developer/tutorials/backend.html>
- Dashboards — <https://www.odoo.com/documentation/19.0/applications/productivity/dashboards.html>
- Calendar — <https://www.odoo.com/documentation/19.0/applications/productivity/calendar.html>
- Installation — <https://www.odoo.com/documentation/19.0/administration/install.html>

**Bundled third-party assets**

- [Inter](https://rsms.me/inter/) and [Poppins](https://fonts.google.com/specimen/Poppins) — SIL Open
  Font License 1.1, licence text bundled at [`static/src/fonts/OFL.txt`](static/src/fonts/OFL.txt).

---

## 12. License

**OPL-1** — the Odoo Proprietary License v1.0 — as declared in
[`__manifest__.py`](__manifest__.py) (`'license': 'OPL-1'`) and in [`README.rst`](README.rst).

This is the licence Odoo requires for modules sold on the Apps Store. Full text:
<https://www.odoo.com/documentation/19.0/legal/licenses.html>

Bundled fonts — Inter and Poppins — are under the
[SIL Open Font License 1.1](https://openfontlicense.org/), separately from the module's own licence.
