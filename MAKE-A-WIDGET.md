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

The host serves the widget **from the repo itself via a CDN (githack)** when the
manifest uses `"entry": "index.html"`, so **no GitHub Pages or other hosting setup
is required.** Just create the repo, add the files, and tag it.

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
```bash
gh repo create <user>/<name> --public --source=. --remote=origin --push
gh api -X PUT repos/<user>/<name>/topics -f names[]=meta-portal-widget
```
That's it — the topic is what makes it discoverable. (GitHub's search index can take
a few minutes to pick up a new topic.)

### No-agent fallback: "Use this template"
A person without an assistant that has GitHub access can click **Use this template**
on this repo, edit `index.html`, then **manually add the topic** `meta-portal-widget`
in their new repo under **About → ⚙ → Topics**. (GitHub templates copy files but
**not** topics, so this one click is required.)

---

## How it shows up on the Portal
The Portal Widgets host live-searches GitHub for repos tagged `meta-portal-widget`,
reads each manifest, and lists them in the gallery. Add one to the loop and it joins
the rotating cross-fade carousel. Config you entered is templated into the widget's
URL — no server, no account, no API key for the host.
