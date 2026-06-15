# Clock — a Portal widget (and the example/template for making your own)

A big, clean clock for the [Portal Widgets](https://github.com/compscirunner) host.
Configurable time zone, 12/24-hour, seconds, date, and color.

This repo does double duty:
- **A widget** you can add from the Portal Widgets gallery (tagged `meta-portal-widget`).
- **The canonical example/template.** Want your own widget? You don't need to code —
  hand this repo's URL to an AI assistant and ask it to "make me a ___ widget."
  See **[MAKE-A-WIDGET.md](MAKE-A-WIDGET.md)**.

## Files
- `index.html` — the whole widget, one self-contained, commented file.
- `meta-portal-widget.json` — the manifest (uses `entry`, so it's served via a CDN — no hosting setup).
- `meta-portal-widget.schema.json` — the manifest contract (for humans and AI assistants).
- `MAKE-A-WIDGET.md` — how anyone (or their AI) builds a new widget from this example.

## Config
Settings are entered in the Portal's add-time form and passed to the page as URL
query params: `tz`, `format` (12/24), `seconds`, `date`, `color`.
