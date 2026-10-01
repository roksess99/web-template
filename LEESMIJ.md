# Template voor een nieuwe webshop

Dit is de documentatieset waarmee een nieuwe webshop begint. Hij bevat wat een
half jaar bouwen aan een eerdere dropship-winkel heeft opgeleverd: niet het
assortiment, maar de dingen die bij élke webshop met een leverancier-API
terugkomen.

Kopieer deze map naar de wortel van het nieuwe project. Daarna hoort het
volgende te gebeuren, in deze volgorde.

## 1. De plaatshouders invullen

Overal waar `<<...>>` staat hoort iets ingevuld te worden. Ze staan hieronder
compleet, zodat je ze in één keer kunt vervangen in plaats van ze op te jagen.

| Plaatshouder | Wat erin komt | Waar het vandaan komt |
|---|---|---|
| `<<SHOP>>` | De naam van de winkel | eigenaar |
| `<<DOMEIN>>` | Het domein zonder protocol, bv. `voorbeeld.nl` | eigenaar |
| `<<MARKT>>` | Land en taal, bv. "Nederland, NL primair" | eigenaar |
| `<<ASSORTIMENT>>` | Wat je verkoopt, in één zin | eigenaar |
| `<<LEVERANCIER>>` | Naam van de groothandel of API-partij | eigenaar |
| `<<KVK>>`, `<<BTW>>` | Inschrijving en btw-nummer | eigenaar |
| `<<BEDRIJFSNAAM>>` | Naam zoals bij de KvK | eigenaar |
| `<<SUPPORT_MAIL>>` | Het adres dat op de site staat | eigenaar |
| `<<KLEUR_ACCENT>>` etc. | Zie `docs/BRAND.md` | huisstijl |

Staat iets nog niet vast, laat de plaatshouder dan **staan**. Een lege plek die
opvalt is beter dan een verzonnen waarde die later voor waar wordt aangezien.

## 2. De beslissingen langslopen

`docs/DECISIONS.md` is geen verslag maar een **vragenlijst**. Alles staat er op
OPEN. Elke beslissing die je neemt schrijf je daar op mét de reden, en pas dan
mag er code voor komen. Dat klinkt traag en is het niet: de helft van het
herwerk kwam eerder doordat een keuze wel in code stond maar nergens in
woorden, en een half jaar later niemand meer wist waarom.

Begin met de vier die alles daarna bepalen:

1. **#1 Welke leverancier, en wat kan die API echt?** (meten, niet lezen)
2. **#2 Wat verkopen we wel en niet?**
3. **#3 Hoe komt de inkoopprijs aan een verkoopprijs?**
4. **#4 Wie koopt er in: een mens of de code?**

## 3. De API meten voordat je bouwt

`docs/api/LEVERANCIER.md` is een invulformulier met de vragen die je anders pas
ná het bouwen blijkt te moeten stellen. Loop hem helemaal af met echte
verzoeken op het echte account. Elk antwoord krijgt er het woord **GEMETEN**
bij met de datum. Wat niet gemeten is, is een aanname.

De duurste les staat daar als vraag 7: **de voorraad en de prijs moeten van
dezelfde verkoper komen.** Daar ging eerder een half jaar lang een bedrag van
gemiddeld € 60 per bestelling in zitten zonder dat iemand het zag.

## 4. De rest

**Wat waar staat:**

| Bestand | Waarvoor |
|---|---|
| `CLAUDE.md` | Het instructiebestand dat de agent elke sessie leest. Kort genoeg om te lezen, concreet genoeg om naar te handelen |
| `docs/DECISIONS.md` | De beslissingen, met de reden erbij. Het geheugen van het project |
| `docs/BRAND.md` | Huisstijl. Leidend voor alle UI |
| `docs/SCHERMEN.md` | **Wat** er op elk scherm hoort en waarom — de ontwerpkant |
| `docs/BEHEER.md` | Wat de eigenaar in het beheerpaneel nodig heeft, en hoe zo'n scherm eruitziet |
| `docs/DATAMODEL.md` | Welke gegevens vers zijn en welke bevroren, en waar twee verzoeken elkaar raken |
| `docs/PRIJZEN.md` | Prijsregels, acties en kortingscodes — en hoe ze elkaar begrenzen |
| `docs/RETOUREN.md` | De retourstroom, wie wat betaalt, en hoe je terugbetaalt |
| `docs/MAIL.md` | Welke berichten er zijn, hoe de sjablonen zijn opgebouwd, en hoe ze aankomen |
| `docs/FACTUUR.md` | De factuur-PDF: wettelijke velden, nummering, btw-afronding, opmaak |
| `docs/PRIVACY.md` | De onderbouwing van de privacyverklaring. Bijwerken bij elke nieuwe tabel of cookie, niet achteraf |
| `docs/HOSTING.md` | Wat er bij het uitrollen misging. Lezen vóór de eerste deploy, niet erna |
| `docs/TESTEN.md` | Hoe je weet dat het werkt, zonder testrunner |
| `docs/CHECKLIST.md` | De lijst voor livegang |
| `docs/api/LEVERANCIER.md` | Invulformulier voor de API. Meten vóór je bouwt |
| `docs/api/VRAGEN.md` | Wat je de leverancier moet vragen |

**En de regels in `.claude/rules/`.** Die laden alleen bij het soort werk waar
ze over gaan, dus ze kosten niets als je er niet aan werkt. Daar hoort in wat
je anders in elke prompt zou moeten herhalen:

| Regelbestand | Laadt bij |
|---|---|
| `frontend.md` | UI-werk: kleur, toegankelijkheid, prestaties, SEO |
| `geld.md` | Prijzen, winkelwagen, afrekenen, bestellingen, retouren |
| `catalogus.md` | De adapter naar de leverancier |
| `beveiliging.md` | Beheerpaneel, webhooks, invoer van buiten |

## Wat hier bewust níet in zit

- **Geen code.** Een componentenbibliotheek uit een andere winkel kopiëren
  levert een winkel op die eruitziet als die andere winkel. De documenten
  dragen de lessen; de code komt vers.
- **Geen keuze voor een framework.** Die staat als open beslissing in
  `DECISIONS.md` #0, met de afweging erbij.
- **Geen marketingteksten.** Die horen bij de huisstijl en die is er nog niet.
