# ADR-003: Hosting-Strategie

## Status

Akzeptiert (2026-03-19)

## Kontext

Der TSV benoetigt zuverlaessiges, kostenguenstiges Hosting fuer mehrere Apps. Selbst-Hosting wird bevorzugt (DSGVO, Kontrolle).

## Entscheidung

- **Produktion**: Hetzner Cloud CX22 (2 vCPU, 4 GB RAM, Nuernberg) — Docker Compose
- **Dev/Demo**: Scaleway Serverless (Paris) — Scale-to-zero, kosteneffizient
- **Website**: Bestehendes Strato-Hosting fuer TYPO3
- **DNS**: Strato (tsv-wartenberg.de), CNAME fuer Subdomains

### Subdomain-Schema

| Subdomain | App |
|-----------|-----|
| tsv-hub.tsv-wartenberg.de | tsv-hub |
| auth.tsv-wartenberg.de | tsv-auth |
| dashboard.tsv-wartenberg.de | tsv-dashboard |
| www.tsv-wartenberg.de | tsv-website (TYPO3) |

## Konsequenzen

- **Positiv**: Guenstig (~5 EUR/Monat Hetzner), Daten in Deutschland, volle Kontrolle
- **Negativ**: Selbst-Administration, kein managed Scaling
