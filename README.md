# SlopeSense cloud dashboard

The same dashboard as the board's own page, but it gets its data through HiveMQ Cloud,
**Live page:** https://leninjacobregi.me/slopesense/  (repo: github.com/leninjacobregi123/slopesense)
To update: `python3 tools/make_cloud_page.py` then commit and push in this folder.

so it works from any phone or laptop on any internet.

    ESP32 (on any Wi-Fi with internet) --MQTT/TLS 8883--> HiveMQ Cloud <--WSS 8884-- this page

Built from `firmware/sentinel_live/dashboard.h` by `tools/make_cloud_page.py` — rerun it after
changing the dashboard. Host this folder as static files (GitHub Pages).

## No-login mode (default)
Out of the box the board sends to HiveMQ's free public broker `broker.hivemq.com` (no login),
and the page opens straight onto it: no accounts. Anyone with the page can watch, so in this mode it is
**view-only**: the board ignores commands from the internet. Over USB: `K,public` / `K,off`.

## Private mode (optional): own HiveMQ Cloud broker
1. Sign up at hivemq.com → HiveMQ Cloud → create a free **Serverless** cluster.
   Note the **Cluster URL** (like `abc123.s1.eu.hivemq.cloud`).
2. Cluster → Access Management → create two credentials:
   - `slopesense-board` (for the ESP32)
   - `slopesense-view` (for people opening the page)
3. Local dashboard (http://192.168.4.1 on the SlopeSense Wi-Fi, or the board's hotspot IP) →
   Settings → **Cloud dashboard** → enter the Cluster URL + board login → Save. Status shows "Connected".
4. Open the hosted page → enter the Cluster URL + viewer login → Connect.
   "Copy share link" makes a link that logs other devices in directly (it contains the viewer password).

## Notes
- Data: readings every 1.5 s while someone has the page open, every 30 s otherwise (~2.5 KB each).
- Alerts (state changes) are sent at once.
- Camera: the board relays one photo (320x240, ~10 KB) every ~2.5 s while someone has the page open (topic `/cam`).
- Wi-Fi changes can only be made from the local dashboard.
- Topics: `slopesense/<board id>/api|notes|meta|status` (retained), `/cmd`, `/hello`.
