# Flow Room Artifacts

Generated pages shown on the LEJ1 flow-room screens. Those devices have no
backend and no ANT access, so everything has to arrive over plain HTTPS.

This repository holds **only** these generated artifacts. It is public because
GitHub Pages is free for public repos only, and making `ufo-engine` itself
public was never an option (source code, Einteilung logic, internal hostnames).
The backend publisher commits here roughly every 30s.

## Files

- `dashboard.html` -- "UFO - Engine Monitor" (Capacity, Workflow, Einteilung,
  Reporting, OB Uebergabeort). Rewritten every ~30s by the backend scheduler
  (`dashboard_service.publish_dashboard`). Has no `<meta refresh>` of its own.
- `ubergabe_board.html` -- shift-handover Uebergabe Board. Rewritten on each
  ANT upload of the Schichtuebergabe report (`ubergabe_service.publish_board`),
  not on a timer. Carries its own `<meta refresh content="60">`.
- `kiosk.html` -- the page each screen should actually point at. Polls the
  target file every 10s and swaps it into an iframe via `srcdoc`, so the
  screen updates without flicker and without the target needing a refresh tag.

## Screen setup

Requires **GitHub Pages** (Settings -> Pages -> Deploy from a branch ->
`main` / `/ (root)`). Point each screen's browser at:

    https://jashenke.github.io/ufo-flowroom/flow-room/kiosk.html?file=dashboard.html
    https://jashenke.github.io/ufo-flowroom/flow-room/kiosk.html?file=ubergabe_board.html

Or `https://jashenke.github.io/ufo-flowroom/` for a menu of both.

Query parameters:

| Param | Default | Meaning |
|---|---|---|
| `file` | `dashboard.html` | File to display. Comma-separated for rotation. |
| `interval` | `10000` | Poll interval in ms (how often content is refetched). |
| `rotate` | `20000` | With several files: ms each one stays on screen. |
| `scale` | `0.95` | Shrink content to compensate for TV overscan cropping edges. |

One screen alternating between both boards:

    .../kiosk.html?file=dashboard.html,ubergabe_board.html&rotate=30000

## Do not use raw.githubusercontent.com

`raw.githubusercontent.com` serves every file as `Content-Type: text/plain`
with `X-Content-Type-Options: nosniff`. A browser therefore shows the HTML
source as text instead of rendering it -- including `kiosk.html` itself, so
the srcdoc workaround cannot save it. Logging in to GitHub does not change the
Content-Type. GitHub Pages is the only mechanism that serves `text/html`.
