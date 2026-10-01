# Beslissingen — <<SHOP>>

Het geheugen van het project. Elke keuze die niet uit de code af te leiden is,
staat hier met status, afhankelijkheden, datum en reden. **Zolang een
beslissing niet `DECIDED` is: niet gokken, vragen** — ook als het antwoord
voor de hand lijkt te liggen. Werk dat ervan afhangt blijft liggen.

## Statussen

| Status | Betekenis | Verplicht |
|---|---|---|
| `OPEN` | Nog niet beantwoord; er ontbreekt informatie (meting, offerte, advies) | — |
| `BLOCKED` | Wacht op een andere beslissing | `Depends on` met minstens één niet-`DECIDED` beslissing |
| `READY` | Afhankelijkheden `DECIDED`, informatie compleet; alleen het antwoord van de eigenaar ontbreekt | alle `Depends on` `DECIDED` |
| `DECIDED` | Beantwoord | `Decided` (datum), reden, en het verworpen alternatief |
| `SUPERSEDED` | Vervangen | `Superseded by` |

Claude zet een beslissing nooit zelf op `DECIDED`. Claude mag `BLOCKED` →
`READY` voorstellen als de afhankelijkheden beslist zijn.
`node scripts/validate-template.mjs` controleert nummers, statussen en
afhankelijkheden.

**Vorm van een beantwoorde beslissing** (voorbeeld):

```text
## D-99 · Korte titel

- **Status:** DECIDED
- **Depends on:** D-01
- **Decided:** 2026-01-31

Wat er besloten is, in één alinea.

**Waarom dit en niet het alternatief.** Het alternatief benoemen is het
belangrijkste deel: dat is wat je later opnieuw zou overwegen.

GEMETEN 2026-01-31 — omgeving en waarneming waarop het rust.

**Wat er niet in zit:** wat bewust is overgeslagen.
```

## Volgorde

```text
D-00 framework/hosting ──┬─► D-05 betaaldienst ──► D-09 retouren
                         ├─► D-06 database ──► D-07 beheer, D-19 backup/herstel
                         ├─► D-17 CI/CD          D-18 observability
D-01 leverancier ────────┼─► D-02 assortiment, D-04 inkoop, D-13 verzending
                         ├─► D-16 kostprijs ──► D-03 prijsopbouw ──► D-08 kortingen
                         └─► D-22 voorraad en snapshot
```

---

## D-00 · Framework en hosting

- **Status:** OPEN
- **Depends on:** —

Eerdere afweging voor een serverrenderend framework op gedeelde hosting:
serverrendering is voor een webshop geen luxe (vindbaarheid, prijs zonder
JavaScript-ronde); gedeelde hosting is goedkoop en knijpt (`docs/HOSTING.md`:
processen, native modules, symlinks); één taal voor front- en backend laat de
browser en de server dezelfde geldfunctie gebruiken.

Delen van de template gaan uit van een serverrenderend React-framework met
pnpm (`.claude/launch.json`, `.env.example`, `frontend.md`). Dat is een
`AANNAME` die met deze beslissing bevestigd of aangepast wordt.

**Te beantwoorden:** framework; hostingpartij; bouwen op de server of een
gebouwd artefact uploaden; is een webhook-URL van buiten bereikbaar. Meet de
punten uit `docs/HOSTING.md` § 9.

---

## D-01 · Welke leverancier, en wat kan die API echt?

- **Status:** OPEN
- **Depends on:** —

Niet de verkooppraat maar het gemeten gedrag: `docs/api/LEVERANCIER.md`
helemaal invullen vóór er adaptercode komt. Lessen uit een vorig project
(`EERDER WAARGENOMEN`): een catalogus is niet altijd te bladeren; meerdere
verkopers per artikel maken "de prijs" een keuze; artikelnamen zijn niet te
vertalen.

**Te beantwoorden:** welke API; welk token per onderdeel; rate limit; dekking
van het assortiment; bestellen via de API; idempotentie of eigen referentie
bij bestellen.

---

## D-02 · Wat verkopen we wel en niet?

- **Status:** BLOCKED
- **Depends on:** D-01

Een harde grens als **allowlist in code**, niet als filter in de navigatie:
ook een directe URL naar een artikel buiten het assortiment levert niets op.

**Te beantwoorden:** welke productgroepen, welke bewust niet, en wat er gebeurt
met een groep die leeg blijft.

---

## D-03 · Van inkoopprijs naar verkoopprijs

- **Status:** BLOCKED
- **Depends on:** D-01, D-16

De beslissing waar het meeste geld in zit. Inkoop is meestal excl. btw en
advies meestal incl. (`EERDER WAARGENOMEN`: weken 21 % te hoge prijzen) —
controleer met een schermafdruk van het platform. Opslag per groep werkt beter
dan één percentage; de sleutel van de opslag moet overal gelijk zijn.

**Te beantwoorden:** vaste opslag of per groep; waar de regel aan hangt; wat er
gebeurt zonder adviesprijs; minimummarge.

---

## D-04 · Wie koopt er in: een mens of de code?

- **Status:** BLOCKED
- **Depends on:** D-01

Automatisch doorbestellen is het grootste risico in het systeem. Eerder bleef
het bewust handwerk zodat annuleringen binnen minuten te honoreren waren; voor
een beginnende winkel de aanbevolen start. Automatiseren kan pas met een aparte
sleutel, een idempotente bestel-call en de `UNKNOWN`-afhandeling uit
`docs/STATE_MACHINES.md`.

**Te beantwoorden:** handmatig of automatisch; annuleringsvenster tussen
`PAID` en `FULFILLMENT_PENDING`; wat er gebeurt als de leverancier weigert
nadat de klant betaald heeft.

---

## D-05 · Betaaldienst

- **Status:** BLOCKED
- **Depends on:** D-00

**Te beantwoorden:** welke dienst en methodes; webhook met handtekening of
alleen "opvragen"; ondersteunt hij idempotentiesleutels; mapping van zijn
statussen naar `docs/STATE_MACHINES.md` § Payment; geldigheid van een
betaalpoging; zijn rol onder de AVG; toegankelijkheid van zijn betaalpagina;
zijn bestellingen van € 0 toegestaan. Architectuur: `docs/PAYMENTS.md`.

---

## D-06 · Database

- **Status:** BLOCKED
- **Depends on:** D-00

Dat er een database met transacties komt staat vast (D-25). Hier gaat het om
**welke**, en hoe migraties lopen.

**Te beantwoorden:** welk systeem en welke versie; wat de hosting toestaat
(gemeten op een wegwerptabel); wie migraties draait in productie (pipeline of
mens); hoe point-in-time-herstel werkt.

---

## D-07 · Beheerpaneel, inloggen en rechten

- **Status:** BLOCKED
- **Depends on:** D-06

Uitgangspunten staan in `.claude/rules/beveiliging.md` (MFA verplicht, sessies
in de database, rechten per onderdeel, audit).

**Te beantwoorden:** pad van het paneel; welke rechten bestaan; sessieduur
(inactief en absoluut); wie mag terugbetalen en tot welk bedrag zonder tweede
persoon.

---

## D-08 · Kortingen en acties

- **Status:** BLOCKED
- **Depends on:** D-03

Regels en rekenvolgorde: `docs/PRIJZEN.md`. De "van"-prijs vraagt een
prijsgeschiedenis die loopt vóór een actie begint — begin vroeg met meten.

**Te beantwoorden:** niveau van acties (artikel, groep, soort); komen er
kortingscodes; "één keer per klant" op welke sleutel.

---

## D-09 · Retourneren

- **Status:** BLOCKED
- **Depends on:** D-05

Wettelijke kaders en stroom: `docs/RETOUREN.md`.

**Te beantwoorden:** hoe een klant aanmeldt; wie de retourzending betaalt bij
een fout van de winkel; terugbetalen na ontvangst of na verzendbewijs;
waardevermindering ja/nee en hoe vastgesteld.

---

## D-10 · Privacy en cookies

- **Status:** BLOCKED
- **Depends on:** D-01, D-05, D-18

Werkwijze en dataflows: `docs/PRIVACY.md`.

**Te beantwoorden:** bewaartermijnen per gegeven; komt er analytics en zo ja
welke soort; is er een toestemmingsbanner nodig; wie bevestigt de
privacyverklaring juridisch.

---

## D-11 · Marketingmail

- **Status:** OPEN
- **Depends on:** —

Transactiemail mag zonder toestemming vooraf, een aanbiedingsmail niet
(`WETTELIJK`, te bevestigen). Adressen verzamelen "voor later" kan niet.

**Te beantwoorden:** komt er een nieuwsbrief; hoe wordt toestemming
vastgelegd; aparte verzendende mailbox.

---

## D-12 · Beoordelingen

- **Status:** OPEN
- **Depends on:** —

Selectief publiceren is een oneerlijke handelspraktijk (`WETTELIJK`, te
bevestigen). Uitnodiging per bestelling met een token; verbergen alleen met
reden; geen sterrengemiddelde in de markering zonder echte beoordelingen.

**Te beantwoorden:** komen ze er; eigen systeem of een dienst.

---

## D-13 · Verzending

- **Status:** BLOCKED
- **Depends on:** D-01

**Te beantwoorden:** vervoerder en tarieven; drempel voor gratis verzending;
bestelling uit meerdere bronnen (twee pakketten, één of twee keer
verzendkosten); na hoeveel dagen geldt een zending als afgeleverd zonder
melding.

---

## D-14 · Valuta en markten

- **Status:** OPEN
- **Depends on:** —

Het geldmodel draagt altijd een valuta (`.claude/rules/geld.md`).

**Te beantwoorden:** één valuta of meerdere; één land of meerdere (btw-tarieven
en regels per land); prijzen per markt.

---

## D-15 · Btw-berekening, afronding en kortingsverdeling

- **Status:** OPEN
- **Depends on:** —

Laten bevestigen door de boekhouder; daarna één functie overal.

**Te beantwoorden:** btw per regel of per tarief over het totaal; afronding
(half-up, bankers); verdeling van een orderkorting (proportioneel, methode voor
de rest); btw op verzendkosten bij gemengde tarieven.

---

## D-16 · Kostprijs (landedCost) en ondergrens

- **Status:** BLOCKED
- **Depends on:** D-01

Tot deze beslissing is `landedCost = supplierCost` een benoemde aanname.

**Te beantwoorden:** wat telt mee in de kostprijs (inkomende verzending,
betaalkosten per transactie, toeslagen, retourrisico); per artikel of als
opslag; wat de ondergrens voor acties is.

---

## D-17 · CI/CD, testtools en omgevingen

- **Status:** BLOCKED
- **Depends on:** D-00

Architectuur: `docs/CI_CD.md`; teststrategie: `docs/TESTEN.md`.

**Te beantwoorden:** CI-platform; test- en E2E-runner; komt er een staging;
hoe wordt productie-uitrol goedgekeurd.

---

## D-18 · Observability-tooling

- **Status:** BLOCKED
- **Depends on:** D-00

Eisen: `docs/OBSERVABILITY.md`.

**Te beantwoorden:** waar logs heen gaan en hoe lang; error tracking; uptime-
monitor; wie alerts ontvangt.

---

## D-19 · Backup- en hersteldoelen

- **Status:** BLOCKED
- **Depends on:** D-06

Eisen en plaatshouders: `docs/DISASTER_RECOVERY.md`.

**Te beantwoorden:** RPO, RTO, backupfrequentie, retentie, offsite-locatie,
interval van de restoretest, incident-eigenaar.

---

## D-20 · Toegankelijkheid: juridische toepasselijkheid

- **Status:** OPEN
- **Depends on:** —

Het technische doel staat vast (D-28). Deze vraag gaat over de wet:
`docs/ACCESSIBILITY.md`.

**Te beantwoorden:** valt de winkel onder de European Accessibility Act of een
vrijstelling; is een toegankelijkheidsverklaring nodig.

---

## D-21 · Factuurbeleid

- **Status:** OPEN
- **Depends on:** —

Laten bevestigen door de boekhouder (`docs/FACTUUR.md`).

**Te beantwoorden:** altijd een factuur, ook voor consumenten; nummerformaat;
aparte reeks voor creditnota's; PDF opslaan of regenereren; kan de gekozen
PDF-bibliotheek getagde, deterministische PDF's maken (meten).

---

## D-22 · Voorraad, reservering en geldigheid van de snapshot

- **Status:** BLOCKED
- **Depends on:** D-01

Bij dropship ligt de voorraad bij de leverancier en is lokaal reserveren
beperkt zinvol.

**Te beantwoorden:** wordt voorraad gereserveerd bij het afrekenen; mag er
besteld worden bij onbekende voorraad; hoe lang is een bevroren snapshot
betaalbaar.

---

## D-23 · Klantaccounts

- **Status:** OPEN
- **Depends on:** —

Afrekenen zonder account is het uitgangspunt (`docs/SCHERMEN.md`). Een account
voegt wachtwoorden, sessies en herstel voor klanten toe.

**Te beantwoorden:** komen er klantaccounts; zo ja, met welke authenticatie.

---

## D-24 · Bewaartermijnen

- **Status:** OPEN
- **Depends on:** —

**Te beantwoorden:** prijsgeschiedenis (minimaal de referentieperiode plus
marge, of langer als bewijs); auditlog; provider-events; applicatielogs; IP-
adressen in logs.

---

## D-25 · Bedrijfsstaat alleen in een database met transacties

- **Status:** DECIDED
- **Depends on:** —
- **Decided:** 2026-10-01

Bestellingen, betalingen, terugbetalingen, factuurnummers, sessies,
reserveringen, idempotentiesleutels en audit staan in een database met
transacties — nooit op het filesystem (`.claude/rules/database.md`).

**Waarom dit en niet het alternatief.** De eerdere tekst stelde "begin met
JSON-bestanden, stap over bij de eerste teller". Verworpen op instructie van de
eigenaar: een deploy die naar een nieuwe map kopieert, een tweede proces of
een crash halverwege een schrijfactie kan bedrijfsstaat stil breken, en de
overstap later is een migratie van live data. De database zelf kiezen blijft
D-06.

---

## D-26 · Geautomatiseerd testen is de norm

- **Status:** DECIDED
- **Depends on:** —
- **Decided:** 2026-10-01

Bedrijfskritieke logica heeft unit-, integratie-, security- en E2E-tests
(`docs/TESTEN.md`). Handmatige controle vult aan.

**Waarom dit en niet het alternatief.** De eerdere tekst koos "geen
testrunner, controlescripts en echte doorlopen". Verworpen op instructie van de
eigenaar: geld, state machines, idempotentie en autorisatie zijn precies de
fouten die je met kijken niet vindt. De controlescripts blijven, als
aanvulling. Welke tools: D-17.

---

## D-27 · Volgorde bij geldbewegingen: intent eerst

- **Status:** DECIDED
- **Depends on:** —
- **Decided:** 2026-10-01

Een order (`PENDING_PAYMENT`) of terugbetaling (`REQUESTED`) wordt met een
idempotentiesleutel vastgelegd vóór de call naar de provider; de uitkomst
wordt pas geschreven na bevestiging van de provider; reconciliatie herstelt
afwijkingen (`docs/PAYMENTS.md`).

**Waarom dit en niet het alternatief.** De eerdere regel "betaaldienst eerst,
database daarna" voorkwam terecht een terugbetaling die als geslaagd in de
boeken staat zonder geld, maar liet bij een crash ná de provider-call geen
spoor achter, en paste niet op betalingen (een betaling zonder bestaande order).
Intent-first behoudt de bedoeling — nooit "geslaagd" vóór de provider het
bevestigt — en voegt het spoor toe. Opgelost als logisch gevolg van de
bestaande regels en de instructie van de eigenaar.

---

## D-28 · Technisch toegankelijkheidsdoel: WCAG 2.2 AA

- **Status:** DECIDED
- **Depends on:** —
- **Decided:** 2026-10-01

WCAG 2.2 niveau AA is het technische doel voor winkel, beheer, mail en PDF
(`docs/ACCESSIBILITY.md`), los van de juridische vraag (D-20).

**Waarom dit en niet het alternatief.** Een lager doel (A, of alleen "best
effort") laat juist de checkout-problemen staan die klanten kosten; een hoger
(AAA) is voor een winkel niet haalbaar over de hele linie. Instructie van de
eigenaar.

---

## Beslislog

| Datum | Beslissing | Wijziging |
|---|---|---|
| 2026-10-01 | D-00 – D-24 | Herschreven naar statusformaat met afhankelijkheden; D-14 – D-24 toegevoegd bij de herziening van de template |
| 2026-10-01 | D-25 – D-28 | Vastgelegd op instructie van de eigenaar (herziening template) |
