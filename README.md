# Datenschutzerklärung – Bachelorarbeit OneTutor

Statische Datenschutzerklärung zur Online-Befragung, ausgeliefert über einen schlanken
nginx-Container. Gedacht für das Hosting auf einem Raspberry Pi via Portainer.

## Inhalt

```
.
├── site/index.html      # die Datenschutzerklärung (wird unter / ausgeliefert)
├── nginx.conf           # nginx-Server-Config (Security-Header, gzip)
├── Dockerfile           # baut das nginx-Image inkl. HTML
└── docker-compose.yml   # Stack-Definition für Portainer
```

Standard-Port: **8088** auf dem Host (`http://<raspi-ip>:8088`).
Zum Ändern in `docker-compose.yml` den linken Wert bei `ports` anpassen.

## Deployment in Portainer (Git-Stack)

1. Repo zu GitHub/GitLab pushen.
2. Portainer → **Stacks** → **Add stack** → Name z. B. `datenschutz`.
3. Build method: **Repository**.
4. Repository URL eintragen, Branch `main`, Compose path `docker-compose.yml`.
5. **Deploy the stack**. Portainer baut das Image und startet den Container.

Aufruf danach: `http://<raspi-ip>:8088`

## Lokal testen

```bash
docker compose up --build -d
# http://localhost:8088
docker compose down
```

## Aktualisieren

HTML in `site/index.html` ändern, committen, pushen. In Portainer beim Stack auf
**Pull and redeploy** klicken.
