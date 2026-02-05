# Frappe All-in-One Docker Image

Ein Docker Image mit **allen** offiziellen Frappe Apps für Self-Hosting mit Dokploy oder Docker Compose.

## Enthaltene Apps

| App | Branch | Beschreibung |
|-----|--------|--------------|
| ERPNext | version-16 | ERP System |
| Frappe HR | version-16 | Personalverwaltung |
| Frappe CRM | develop | Kundenmanagement |
| Frappe Helpdesk | develop | Ticketsystem |
| Frappe Insights | develop | Business Intelligence |
| Frappe Drive | main | Cloud-Speicher |
| Frappe Builder | develop | Website-Builder |
| Frappe LMS | develop | Lernplattform |
| Frappe Wiki | main | Wissensdatenbank |
| Frappe Gameplan | main | Team-Forum |
| Print Designer | develop | Druckvorlagen |

## Quick Start mit Dokploy

### Option 1: Template + Custom Image

1. In Dokploy: **Create Service → Template → ERPNext** (oder Frappe HR)
2. **VOR dem Deploy** die Environment Variables anpassen:

```
IMAGE_NAME=ghcr.io/danmitrea/frappe_allinone
VERSION=latest
INSTALL_APP_ARGS=--install-app erpnext --install-app hrms --install-app crm --install-app helpdesk --install-app insights --install-app drive --install-app builder --install-app lms --install-app wiki --install-app gameplan --install-app print_designer
```

3. Deploy klicken

### Option 2: Docker Compose in Dokploy

1. In Dokploy: **Create Service → Compose**
2. Provider: GitHub
3. Dieses Repository auswählen
4. Compose Path: `compose.yaml`
5. Environment Variables setzen (siehe `.env.example`)
6. Deploy

## Manuelles Deployment mit Docker Compose

```bash
# Repository klonen
git clone https://github.com/DanMitrea/frappe_allinone.git
cd frappe_allinone

# .env Datei erstellen
cp .env.example .env
# .env anpassen (Domain, Passwörter, etc.)

# Starten
docker compose up -d

# Logs verfolgen
docker compose logs -f create-site
```

## Apps nach dem Start installieren

Die Apps sind im Image enthalten, müssen aber auf der Site installiert werden:

```bash
# In den Container einloggen
docker compose exec backend bash

# Apps installieren
bench --site deine-site.local install-app erpnext
bench --site deine-site.local install-app hrms
bench --site deine-site.local install-app crm
bench --site deine-site.local install-app helpdesk
bench --site deine-site.local install-app insights
bench --site deine-site.local install-app drive
bench --site deine-site.local install-app builder
bench --site deine-site.local install-app lms
bench --site deine-site.local install-app wiki
bench --site deine-site.local install-app gameplan
bench --site deine-site.local install-app print_designer
```

## Image selbst bauen

Falls du das Image lokal bauen willst:

```bash
# frappe_docker klonen
git clone https://github.com/frappe/frappe_docker.git
cd frappe_docker

# apps.json aus diesem Repo kopieren
cp ../apps.json .

# Base64 encoden
export APPS_JSON_BASE64=$(base64 -w 0 apps.json)

# Image bauen
docker build \
  --build-arg=FRAPPE_PATH=https://github.com/frappe/frappe \
  --build-arg=FRAPPE_BRANCH=version-16 \
  --build-arg=PYTHON_VERSION=3.11.6 \
  --build-arg=NODE_VERSION=18.18.2 \
  --build-arg=APPS_JSON_BASE64=$APPS_JSON_BASE64 \
  --tag=frappe-allinone:latest \
  --file=images/custom/Containerfile .
```

## Automatische Builds

Dieses Repository baut automatisch ein neues Image:
- Bei jedem Push auf `main`
- Jeden Sonntag um 02:00 UTC (wöchentliches Update)
- Manuell über "Actions" → "Run workflow"

## Backups

```bash
# Backup erstellen
docker compose exec backend bench --site alle backup --with-files

# Backups liegen in ./sites/SITENAME/private/backups/
```

## Lizenz

MIT - Nutze es wie du willst.

Die enthaltenen Frappe Apps unterliegen ihren jeweiligen Lizenzen (meist GPL-3.0).
