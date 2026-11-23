# HAUDT orderstatus-automatisering

Zet in CCV Shop automatisch elke order met status **"Nieuw" (1)** die **betaald** is en **maximaal 32 dagen** oud is, over op **"In behandeling" (2)**. Oudere orders worden overgeslagen.

Draait via GitHub Actions (openbare repository = onbeperkt gratis Actions-minuten), standaard elke 5 minuten (12× per uur). Bij de statuswijziging wordt de klant niet gemaild (de API-standaard `mail: false` blijft).

## Eenmalige installatie

1. Push deze repository naar GitHub (of laat het script dat doen).
2. Maak het environment aan: *Settings → Environments → New environment* → naam `orderstatus-automatisering`.
3. Voeg daaronder toe (*Environment secrets* en *Environment variables*):
   - `CCV_API_KEY` = je API key uit CCV Shop (Mijn Webshop → Instellingen → API gegevens), met lees- en schrijfrechten op orders
   - `CCV_API_SECRET` = je API secret
   - Variabele `CCV_SHOP_URL` = `https://www.haudt.nl`
4. Zet de workflow aan (staat standaard aan na de eerste push; aan te passen onder *Actions*).
5. Test eerst veilig: ga naar *Actions → Orderstatus automatisering → Run workflow* en vink **dry_run** aan. Je ziet dan alleen wat er zou veranderen. Daarna kun je het schema gewoon laten draaien.

## Schema aanpassen

Pas de cron-regel in `.github/workflows/update-order-status.yml` aan. Standaard elke 5 minuten (`*/5 * * * *`); omdat deze repository openbaar is, zijn de Actions-minuten onbeperkt gratis.

## Regels

| Instelling | Waarde |
|---|---|
| Van status | 1 (Nieuw) |
| Naar status | 2 (In proces / In behandeling) |
| Voorwaarde | `paid = true` én `create_date` binnen 32 dagen |
| Filtermethode | Server-side: `status`, `min_create_date` (formaat `Y-m-d`); betaald-filter client-side op `paid` |
| API-limieten | Ophalen 150/min, patchen 100/min (script vertraagt automatisch) |

## Lokaal testen

```bash
CCV_SHOP_URL=https://www.haudt.nl CCV_API_KEY=... CCV_API_SECRET=... DRY_RUN=true python update_order_status.py
```

Het script gebruikt alleen de Python-standaardbibliotheek; geen dependencies.
