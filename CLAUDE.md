# <<SHOP>> — webshop (<<MARKT>>)

Webshop voor <<ASSORTIMENT>>. Markt: <<MARKT>>.

Huisstijl: @docs/BRAND.md — wijk hier nooit vanaf.
Openstaande beslissingen: @docs/DECISIONS.md — **niet gokken, vragen.**
Wat er op een scherm hoort: @docs/SCHERMEN.md (winkel) en @docs/BEHEER.md
(beheerpaneel).
Welke gegevens vers zijn en welke bevroren: @docs/DATAMODEL.md.

## Bouwvolgorde — belangrijk

We bouwen **frontend-first**. Database, externe productcatalogus en betaling
komen aan het eind. Dat is geen voorkeur maar een volgorde die zich bewijst:
een scherm laat zien wat er mis is aan een aanname, een datamodel niet.

| Fase | Wat | Status |
|---|---|---|
| 1 | UI, routing, taal, thema, componenten — op verzonnen data | OPEN |
| 2 | Winkelwagen (client-side, localStorage) | OPEN |
| 3 | De echte catalogus achter een provider-interface | OPEN |
| 4 | Database: bestellingen, factuurnummers, tellers | OPEN |
| 5 | Betaling | OPEN |
| 6 | Beheerpaneel | OPEN |

**Regels tijdens fase 3 — deze drie voorkomen het meeste herwerk:**

- Het datacontract in `lib/catalog/types.ts` is leidend. Past de echte API daar
  niet op, dan passen we de **adapter** aan — nooit de componenten.
- Componenten importeren **alleen** types uit dat ene bestand. Niet uit de
  adapter, niet uit de API-schema's.
- Alle calls naar de leverancier lopen **server-side**. De browser praat nooit
  rechtstreeks met de leverancier: het token is een secret en er zit een
  rate limit op die voor de héle winkel geldt.
- Zonder sleutel valt de provider terug op een mock. **De site moet altijd
  zonder token blijven werken** — anders kan niemand aan de UI werken zonder
  verbruik bij de leverancier.
- Waar later serverwerk komt: zet een functie in `lib/` met een
  `// TODO fase X` en laat hem leeg. Geen halve implementatie.

## Stack

- **Framework**: zie @docs/DECISIONS.md #0 — nog te kiezen
- **Styling**: CSS-variabelen uit @docs/BRAND.md, geen UI-kit
- **Taal**: <<MARKT>>
- **Package manager**: pnpm

Verdere regels laden vanzelf bij het werk waar ze over gaan:
`.claude/rules/frontend.md` (UI), `geld.md` (bedragen en bestellingen),
`catalogus.md` (de adapter) en `beveiliging.md` (beheer en invoer van buiten).

**Voeg geen libraries toe zonder te vragen.** Geen state-manager, geen UI-kit,
geen datumbibliotheek voor één functie. Elke afhankelijkheid is iets dat
straks een beveiligingsmelding kan geven en dat iemand moet bijwerken.

## Commands

```bash
pnpm dev          # http://localhost:3000
pnpm build        # moet slagen voor elke commit
pnpm lint
pnpm typecheck
pnpm audit        # kwetsbaarheden; moet nul zijn
```

Draai `pnpm typecheck && pnpm lint` voordat je zegt dat werk af is. Raak je
`package.json` aan, dan ook `pnpm audit`: de hostingpartij scant mee en meldt
wat daar blijft staan.

## Codeconventies

- **Geen `any`.** Ook niet tijdelijk, ook niet met een TODO erbij.
- Alle externe data door een schemavalidatie aan de rand (Zod of gelijkwaardig).
  Daarbinnen is alles getypeerd en hoef je nergens meer te controleren.
- Namen in het Engels, teksten voor de klant in de taal van de winkel.
- Commentaar legt uit **waarom**, niet wat. Code die zichzelf uitlegt heeft
  geen commentaar nodig; een keuze die niet voor de hand ligt wél.
- Een meting hoort in het commentaar met het woord **GEMETEN** en de datum.
  Zonder dat is het over drie maanden niet van een aanname te onderscheiden.

## Geld — hier gaat het echt mis

Deze regels komen stuk voor stuk uit een fout die geld heeft gekost of had
kunnen kosten. Zie @.claude/rules/geld.md voor de uitleg erbij.

- **Bedragen zijn integers in centen.** Nooit floats voor geld.
- **Een bedrag dat naar de betaaldienst gaat komt nooit uit de browser.** De
  winkelwagen leeft in localStorage en is dus door de klant aan te passen; wat
  de browser mag meesturen is hoogstens een artikelnummer en een aantal.
- **Prijs en voorraad komen van dezelfde aanbieding.** Als de leverancier
  meerdere verkopers per artikel heeft: kies er één — de goedkoopste mét
  voorraad — en neem prijs, voorraad en levertijd allemaal daarvandaan.
- **Een korting kan nooit groter zijn dan de marge.** Een actie van 20% op een
  artikel met 10% opslag levert 9% korting op, niet 20% — en het paneel hoort
  dat te zeggen. Zie @docs/PRIJZEN.md.
- **Een bedrag buiten bereik wordt geweigerd, niet afgekapt.** Afkappen voelt
  veilig en levert stil een ander bedrag op dan iemand bedoelde.
- **Wat in de administratie komt is wat de betaaldienst zegt te hebben gedaan**,
  niet wat het formulier vroeg.
- **Betaaldienst eerst, database daarna.** Andersom staat een mislukte
  terugboeking als "terugbetaald" in de boeken.
- **Elke actie die geld verplaatst krijgt een idempotentiesleutel**, zodat twee
  tabbladen samen één boeking opleveren.

## Structuur

Voorstel, gegroeid uit een eerdere webshop. De scheiding die ertoe doet:
**`lib/` kent geen React en `components/` kent geen database.**

```
src/
  app/                   # routes
  app/api/               # webhooks en geplande taken
  components/            # UI, per domein gegroepeerd
  components/<domein>/   # cart, checkout, catalog, admin, …
  lib/                   # domeinlogica, geen React
  lib/catalog/           # types.ts (het contract) + mock + echte adapter
  lib/cart/              # winkelwagenlogica, framework-onafhankelijk
  lib/pricing.ts         # inkoop → verkoop, integers in centen
  lib/orders/            # opslaan, afhandelen, mailen
  lib/db/                # verbinding en transacties
  lib/admin/             # inloggen, sessies, rechten, logboek
db/migrations/           # genummerd, alleen toevoegen
docs/                    # zie LEESMIJ.md
messages/                # teksten per taal
public/brand/            # logo's
scripts/                 # controlescripts (db, mail, betaaldienst)
```

**Waarom `lib/` zonder React:** het bedrag dat de klant ziet en het bedrag dat
de server afrekent moeten uit dezelfde functie komen. Trekt die functie een
databasestuurprogramma de browserbundel in, dan valt de bouw om — en dat merk
je pas bij het bouwen, niet tijdens het ontwikkelen.

Schrijf voor elk extern systeem een **controlescript** dat los van de site
draait: verbinding met de database, een testmail, een testaanroep bij de
betaaldienst. Als er iets niet werkt wil je weten of het aan de winkel ligt of
aan de koppeling.

## Git-workflow

- Branches: `feature/<naam>`, `fix/<naam>`. Nooit direct op `main`.
- Conventional Commits: `feat:`, `fix:`, `chore:`, `refactor:`.
- **Vraag altijd toestemming voor commit of push. Wacht op "GO".**
- Nooit `git push --force`.
- De commitboodschap vertelt wat er veranderde én waarom. Over een jaar is dat
  het enige dat er nog van de overweging over is.

## Werkwijze

- Taak raakt meer dan één bestand: **eerst een plan, dan code.**
- Keuze staat niet in dit bestand of in DECISIONS: **vraag het.** Niet gokken.
- Iets niet kunnen meten is een antwoord: zeg dat, verzin geen getal.
- Werkt iets niet zoals verwacht: zoek de oorzaak, bouw er geen omweg omheen.
- Een fout die je vindt buiten de opdracht: melden, niet stilletjes meenemen.

## Definition of done

- [ ] `pnpm typecheck` en `pnpm lint` groen
- [ ] `pnpm build` slaagt
- [ ] In de browser bekeken, ook op telefoonbreedte en in donkere modus
- [ ] Toetsenbord: alles bereikbaar, focus zichtbaar
- [ ] Teksten in alle talen van de winkel, met dezelfde sleutels
- [ ] Nieuwe beslissing? In @docs/DECISIONS.md, mét reden
- [ ] Nieuwe persoonsgegevens of browseropslag? In @docs/PRIVACY.md

De uitgebreide versie staat in @docs/TESTEN.md, met wat je per soort risico
echt moet controleren. **"Het werkt bij mij" is geen bevinding:** zeg wat je
hebt gedaan, waar, en wat je zag — en wat je niet hebt kunnen controleren hoort
er net zo goed bij.
