# De factuur-PDF

Een factuur is geen opgemaakte bestelbevestiging. Het is een boekstuk met
wettelijke eisen, en het is het enige document uit de winkel dat jaren later
nog klopt moet zijn.

---

## Wat er wettelijk op moet (NL, art. 35a Wet OB)

| Veld | Opmerking |
|---|---|
| Factuurdatum | de datum van uitreiken |
| **Opeenvolgend factuurnummer** | zonder gaten, nooit hergebruikt |
| Naam en adres van de onderneming | zoals bij de KvK |
| **Btw-identificatienummer** van de onderneming | |
| Naam en adres van de afnemer | |
| Omschrijving van de goederen, met hoeveelheid | |
| Datum van levering | mag de besteldatum zijn als die samenvalt |
| Vergoeding **per btw-tarief** | exclusief btw |
| Het btw-tarief | |
| Het btw-bedrag | |

Het KvK-nummer is niet verplicht op de factuur maar wel gebruikelijk, en het
staat toch al op de site.

**Verkoop je aan consumenten, dan hoef je niet per bestelling een factuur uit
te reiken** — maar je moet de verkoop wel vastleggen, en een klant die erom
vraagt krijgt er een. In de praktijk is het eenvoudiger om er altijd een te
maken: dan is de administratie compleet en hoef je nooit achteraf iets te
reconstrueren.

---

## Nummering

- **Eén doorlopende reeks**, uit een teller in de database, in dezelfde
  transactie als de factuur zelf. Zie `docs/DATAMODEL.md`.
- Kies een vorm en leg hem vast: `2026-0001` (per jaar opnieuw) of doorlopend.
  Per jaar opnieuw is gangbaar en leest prettiger; dan is "zonder gaten" een
  eis per reeks.
- **Een nummer dat is uitgedeeld blijft uitgedeeld.** Gaat een bestelling niet
  door, dan vervalt het nummer niet — dan maak je een creditfactuur. Een gat in
  de reeks is een vraag van de boekhouder.
- Een creditfactuur krijgt een **eigen reeks** (`C2026-0001`) en verwijst naar
  het nummer van de oorspronkelijke factuur. Een terugboeking zonder
  creditfactuur klopt niet in de boeken.

---

## Rekenen

**Alles in centen, integers.** En één keuze die je consequent moet maken:

> **Bereken je de btw per regel, of over het totaal per tarief?**

Die twee geven verschillende uitkomsten door afronding. Kies er één, zet hem in
het commentaar, en gebruik overal dezelfde — ook in het besteloverzicht op de
site en in de bevestigingsmail. Een factuur die één cent afwijkt van de
bevestiging levert vragen op die je niet wilt beantwoorden.

**Groepeer per btw-tarief.** Zelfs als je nu één tarief hebt: bouw het alsof er
twee kunnen zijn. Een assortiment dat uitbreidt met iets van 9% breekt anders
de hele factuur.

**Korting hoort zichtbaar.** Een regelprijs die al verlaagd is zonder dat de
korting ergens staat, is voor de klant niet na te rekenen. Zet de korting als
eigen regel of als eigen kolom.

---

## De opbouw van het document

```
┌──────────────────────────────────────────────┐
│ logo            FACTUUR                      │
│                 Nummer     2026-0042         │
│                 Datum      14 maart 2026     │
│                 Bestelling ORD-7391          │
├──────────────────────────────────────────────┤
│ Van                      Aan                 │
│ <bedrijfsnaam>           <klantnaam>         │
│ <adres>                  <adres>             │
│ KvK / btw-nummer         <plaats>            │
├──────────────────────────────────────────────┤
│ Omschrijving      Aantal   Per stuk   Totaal │
│ ...                                          │
├──────────────────────────────────────────────┤
│                     Subtotaal excl. btw      │
│                     Korting                  │
│                     Verzendkosten            │
│                     Btw 21%                  │
│                     TOTAAL                   │
├──────────────────────────────────────────────┤
│ Betaald via <methode> op <datum>             │
│ <bedrijfsgegevens, IBAN, contact>            │
└──────────────────────────────────────────────┘
```

- **Het totaal is het meest gezochte getal.** Groot, vet, onderaan de
  totalenblok.
- **"Betaald" hoort erop.** Een factuur zonder die vermelding leest als een
  verzoek om te betalen, en dan betaalt iemand twee keer.
- Bij meer regels dan op één pagina passen: de kolomkoppen herhalen op elke
  pagina, en het totalenblok op de laatste. Paginanummers erbij.

---

## Technisch

### Lettertypen — hier gaat het altijd mis

De ingebouwde standaardlettertypen van een PDF-bibliotheek dekken vaak alleen
de WinAnsi-tekenset. Dan verdwijnt het euroteken, of een naam met `ë` of `ş`
laat de generatie klappen met een onduidelijke foutmelding.

**Sluit een echt lettertype in** (een TTF-subset) en test met:

```
€ 1.234,56   ë ï ü ø å   Łukasz   Öztürk   ß
```

Een klantnaam komt uit een formulier en kan alles bevatten. Een factuur die
voor één klant niet te maken is, is een factuur die op het verkeerde moment
ontbreekt.

### Determinisme

Dezelfde bestelling moet dezelfde PDF opleveren. Zet er dus **geen tijdstip van
genereren in** en geen willekeurig id. Dan kun je hem altijd opnieuw maken en
hoef je hem niet per se te bewaren.

### Bewaren of opnieuw maken?

Twee geldige keuzes — leg vast welke:

- **Opnieuw maken uit de bevroren bestelregels.** Mag, mits die regels echt
  bevroren zijn (zie `docs/DATAMODEL.md`) en het sjabloon niet verandert op een
  manier die de inhoud raakt.
- **Opslaan als bestand.** Zekerder, en nodig zodra je het sjabloon vrij wilt
  kunnen wijzigen. Kost opslag en een plek waar die bestanden veilig staan.

De fiscale bewaartermijn in Nederland is zeven jaar — voor de factuur, of voor
de gegevens waaruit hij exact te reconstrueren is.

### Toegang

Een factuurlink is persoonsgegeven. Dus: of alleen in het beheerpaneel, of met
een token in de URL dat bij die ene bestelling hoort. **Nooit op te halen met
alleen het factuurnummer** — dat is te raden, en dan leest iemand andermans
adres.

Bestandsnaam: `factuur-2026-0042.pdf`. Niet `document.pdf`.

### Grootte

Hou hem klein. Een ingesloten lettertype-subset en een logo van een paar kB
zijn genoeg. Een factuur van drie megabyte is een bijlage die blijft steken.

---

## Het btw-overzicht voor de boekhouding

Naast de losse facturen heeft de eigenaar één scherm nodig: **per maand de
omzet per btw-tarief, het btw-bedrag, en het aantal facturen.** Dat is wat de
aangifte vraagt.

Let op dat retouren daarin meetellen als negatieve post via de creditfacturen,
en niet door de oorspronkelijke factuur aan te passen. **Een uitgereikte
factuur verander je nooit meer.**

---

## Controlelijst

- [ ] Alle wettelijke velden staan erop
- [ ] Het nummer volgt op het vorige, zonder gat
- [ ] Btw per tarief gegroepeerd, en de berekening gelijk aan die op de site
- [ ] Een naam met accenten en een bedrag met een euroteken komen goed door
- [ ] Meer regels dan één pagina: koppen herhaald, totalen op de laatste
- [ ] "Betaald via … op …" staat erop
- [ ] De link is niet te raden
- [ ] Een creditfactuur verwijst naar het origineel
