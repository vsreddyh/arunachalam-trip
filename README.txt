Hyderabad -> Arunachalam trip plan, 24-28 Dec 2026 (Plan C primary: sleeper bus)
--------------------------------------------------------------------------------
trip-plan-24-28-dec-2026.html  Beautiful site (open this; works offline)
trip-plan-24-28-dec-2026.pdf  Print/PDF version of the site
plan-picker.html               Direct-to-temple plan picker (reference)
index.html                     Copy of the site (for tunnel serving)

Live tunnel (while session runs):
https://c0ea1878a033bf0d.kigkkp757xvu.opentunnel.xyz

Serving: python3 /tmp/opencode/trip-plan-serve.py (127.0.0.1:8765, no-cache;
         serves / -> index.html). Plain http.server is avoided: this subdomain
         was previously used for the Finance Planning dashboard, and browsers
         kept a stale cached copy of it.
Tunnel:  opentunnel up   (status: opentunnel status, stop: opentunnel down)

Saved: 5 Nov 2026 · tunnel updated 12 Nov 2026
