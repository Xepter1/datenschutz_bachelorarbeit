# Datenschutzerklärung – Bachelorarbeit OneTutor

Statische Datenschutzerklärung zur Online-Befragung, ausgeliefert über einen schlanken
nginx-Container.

## Inhalt

```
.
├── site/index.html        # die Datenschutzerklärung (wird unter / ausgeliefert)
├── nginx.conf             # nginx-Server-Config (Security-Header, gzip)
├── Dockerfile             # baut das nginx-Image inkl. HTML
├── docker-compose.yml     # lokaler Test / Standalone (Host-Port 8095)
└── compose.hetzner.yml    # Live-Deploy auf dem Hetzner-Server (Caddy, Auto-HTTPS)
```

## Live auf Hetzner (datenschutz.befragung.xepter.de)

Läuft im Multi-Tenant-Setup hinter dem zentralen Caddy-Proxy – Hintergrund im
**Hosting-Handbuch (Teil B, Schritt 2b)**. Kurzfassung:

1. **DNS** (Hetzner-DNS, Zone `xepter.de`): A-Record `datenschutz.befragung` → `46.225.209.78`.
   Kein Proxy, kein AAAA (oder AAAA auf `2a01:4f8:1c1b:f35b::1`).
2. **Portainer** → **Stacks** → **Add stack** → Build method **Repository**
   - Repository URL: `https://github.com/Xepter1/datenschutz_bachelorarbeit`
   - Branch: `main`
   - **Compose path: `compose.hetzner.yml`**
3. **Deploy the stack.** Caddy erkennt den Container über die Labels, routet
   `datenschutz.xepter.de` darauf und holt das HTTPS-Zertifikat automatisch.

Kein offener Port nötig – die Seite ist ausschließlich über Caddy (HTTPS) erreichbar.
Andere Stacks, Caddy und die Firewall **nicht** anfassen.

### Verifizieren
```bash
# intern (auf dem Server), erwartet 200:
docker run --rm --network caddy curlimages/curl:latest -s -o /dev/null \
  -w "intern: %{http_code}\n" --max-time 15 http://datenschutz-web:80
# von außen (HTTPS):
curl -sS -m 30 -o /dev/null -w "extern: %{http_code}\n" https://datenschutz.befragung.xepter.de/
```

## Lokal testen

```bash
docker compose up --build -d      # http://localhost:8095
docker compose down
```

## Aktualisieren

HTML in `site/index.html` ändern, committen, pushen. In Portainer beim `datenschutz`-Stack
auf **Pull and redeploy** (Häkchen *Re-pull image* aus).
