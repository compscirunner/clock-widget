# Make your own Portal widget

**No coding needed.** Give the URL of this repo to an AI assistant you already use
(ChatGPT, Claude, Copilot, …) and say something like:

> "Make me a moon-phase widget for my Portal, using this repo as the example: https://github.com/compscirunner/clock-widget"

The assistant has everything it needs below to build and publish a widget repo for
you. Once it's published and tagged, it shows up automatically in your **Portal
Widgets → Widget Gallery**, where you tap **Add to loop**.

This **Clock** is the worked example — your assistant should copy its shape.

---

## For the AI assistant — how to build a Portal widget

A Portal widget is a **public GitHub repo** that:
1. is tagged with the topic **`meta-portal-widget`** (this is how the Portal discovers it), and
2. contains two files at its root:
   - **`index.html`** — one self-contained page (inline CSS + JS, no build step).
   - **`meta-portal-widget.json`** — the manifest (schema: `meta-portal-widget.schema.json` in this repo).

When the manifest uses `"entry": "index.html"`, the host loads the widget from its
**GitHub Pages** site at `https://<owner>.github.io/<repo>/` — so the manifest needs
no hard-coded URL (it works even if the repo is renamed or copied). You just have to
turn Pages on; the steps below do it automatically (raw-CDN shortcuts like jsDelivr
or githack don't work here — they serve `.html` as plain text or add a click-through
page, so the widget wouldn't render).

### `index.html` rules
- One file; inline everything. No external dependencies unless you truly need them.
- Fill the entire viewport; readable on a TV across a room; dark-friendly.
- Read user settings from the URL query string (`new URLSearchParams(location.search)`),
  always with sensible defaults so it works with no params.
- No network calls except keyless, CORS-friendly APIs you actually need.

### `meta-portal-widget.json`
Follow `meta-portal-widget.schema.json`. Minimal shape:
```json
{
  "name": "Moon Phase",
  "description": "Tonight's moon phase.",
  "entry": "index.html",
  "dwellSec": 30,
  "config": [ { "key": "color", "type": "text", "label": "Text color", "default": "#eaeaea" } ],
  "urlParams": { "color": "{color}" }
}
```
- `config[]` = the settings the user can fill in the Portal's add-time form.
- `urlParams` maps those settings onto the widget URL (`{key}` tokens). The widget
  reads them from `location.search`. A param with no value is dropped, so defaults apply.
- For location-based widgets, use a field of `"type": "location"` with
  `"default": "@global.location"` (inherits the user's shared location); reference it
  as `{location.json}` / `{location.name}` / `{location.lat}` / `{location.lon}`.

### Publish it (the clean path — you have GitHub access)
Include this repo's `.github/workflows/pages.yml` in the new repo (copy it as-is) —
it auto-enables and deploys GitHub Pages on first push, so there's no manual hosting
step. Then:
```bash
gh repo create <user>/<name> --public --source=. --remote=origin --push
gh api -X PUT repos/<user>/<name>/topics -f names[]=meta-portal-widget
```
The Pages workflow runs on push (publishing to `https://<user>.github.io/<name>/`);
the topic is what makes it discoverable. (Both Pages' first build and GitHub's topic
search index take a couple of minutes.)

### No-agent fallback: "Use this template"
Click **Use this template** on this repo and edit `index.html`. The included Pages
workflow auto-enables hosting on the first commit — so the only manual step is to
**add the topic** `meta-portal-widget` in the new repo under **About → ⚙ → Topics**
(GitHub templates copy files and workflows but **not** topics).

---

## How it shows up on the Portal
The Portal Widgets host live-searches GitHub for repos tagged `meta-portal-widget`,
reads each manifest, and lists them in the gallery. Add one to the loop and it joins
the rotating cross-fade carousel. Config you entered is templated into the widget's
URL — no server, no account, no API key for the host.
