# Infrastruktur-Uebersicht

## Server

| Umgebung | Anbieter | Specs | Zweck |
|----------|----------|-------|-------|
| **Produktion** | Hetzner Cloud CX22 | 2 vCPU, 4 GB RAM, Nuernberg | tsv-hub, tsv-auth, tsv-dashboard |
| **Dev/Demo** | Scaleway Serverless | fr-par, Scale-to-zero | API-Previews, Frontend-Hosting |
| **Website** | Strato | Shared Hosting | TYPO3 (tsv-wartenberg.de) |

## DNS & Domains

| Domain | Anbieter | Zweck |
|--------|----------|-------|
| `tsv-wartenberg.de` | Strato | Hauptdomain |
| `tsv-hub.tsv-wartenberg.de` | CNAME → Hetzner | Vereinsmanagement |
| `auth.tsv-wartenberg.de` | CNAME → Hetzner | Benutzerverwaltung (geplant) |
| `tsv-hub-dev.ck99.io` | Scaleway | Dev-Umgebung |

## Container-Stack (Produktion)

```yaml
# docker-compose.yml (Hetzner)
services:
  nginx:        # Reverse Proxy, SSL Termination
  tsv-hub-api:  # FastAPI Backend
  tsv-hub-web:  # React Frontend (static files)
  tsv-auth-api: # Auth Service (geplant)
  postgres:     # Datenbank
  prometheus:   # Monitoring
  grafana:      # Dashboards
```

## SSL

Let's Encrypt via Certbot (automatische Erneuerung)

## Backup

- **DB**: `pg_dump` via Cron (taeglich, 03:00 UTC), 30 Tage Aufbewahrung
- **Code**: Git + GitHub (geschuetzter `main`-Branch)
- **Recovery**: `docker compose down && docker compose up -d`, dann `pg_restore`

## Monitoring

- Prometheus + Grafana (bestehendes Setup)
- Health-Check-Endpunkte pro App (`/health`)
