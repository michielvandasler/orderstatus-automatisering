# HAUDT orderstatus-automatisering

Zet in CCV Shop automatisch elke order met status **"Nieuw" (1)** die **betaald** is en **maximaal 32 dagen** oud is, over op **"In behandeling" (2)**. Oudere orders worden overgeslagen.

Draait via GitHub Actions (gratis), standaard elk uur. Bij de statuswijziging wordt de klant niet gemaild (de API-standaard `mail: false` blijft).

## Eenmalige installatie

1. Push deze repository naar GitHub (of laat het script dat doen).
2. Voeg repo-geheimen toe: *Settings → Secrets and variables → Actions → New repository secret*
   - `CCV_API_KEY` = je API key uit CCV Shop (Mijn Webshop → Instellingen → API gegevens)
   - `CCV_API_SECRET` = je API secret
3. Voeg een repo-variabele toe: `CCV_SHOP_URL` = `https://www.haudt.nl`
4. Zet de workflow aan (staat standaard aan na de eerste push; aan te passen onder *Actions*).
5. Test eerst veilig: ga naar *Actions → Orderstatus automatisering → Run workflow* en vink **dry_run** aan. Je ziet dan alleen wat er zou veranderen. Daarna kun je het schema gewoon laten draaien.

## Schema aanpassen

Pas de cron-regel in `.github/workflows/update-order-status.yml` aan. Elke 15 minuten. Op een gratis privérepo heb je 2.000 minuten/maand (elk uur ≈ 730 min/maand, ruim binnen het gratis pakket).

## Regels

| Instelling | Waarde |
|---|---|
| Van status | 1 (Nieuw) |
| Naar status | 2 (In proces / In behandeling) |
| Voorwaarde | `paid = true` én `create_date` binnen 32 dagen |
| Filtermethode | Server-side filters: `status`, `is_paid`, `min_create_date` |
| API-limieten | Ophalen 150/min, patchen 100/min (script vertraagt automatisch) |

## Lokaal testen

```bash
CCV_SHOP_URL=https://www.haudt.nl CCV_API_KEY=... CCV_API_SECRET=... DRY_RUN=true python update_order_status.py
```

Het script gebruikt alleen de Python-standaardbibliotheek; geen dependencies.
