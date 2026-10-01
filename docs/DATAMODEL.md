# Datamodel — wat er bewaard wordt, en wat bevroren

Dit beschrijft de vorm van de gegevens, niet de tabellen. Vul de echte namen in
zodra ze er zijn. Het gaat hier om de twee vragen die bij elke entiteit
terugkomen:

1. **Komt dit vers uit de catalogus, of is het bevroren?**
2. **Wie kan dit tegelijk met iemand anders aanpassen?**

---

## De scheidslijn: vers versus bevroren

**Alles wat de klant nog niet gekocht heeft komt vers.** Naam, prijs, voorraad
en foto van een artikel worden bij élk verzoek opnieuw opgehaald. De winkelwagen
bewaart daarom alleen een **verwijzing**, geen productgegevens:

```
CartItem { productId, quantity, (bron als je meerdere catalogi hebt) }
```

Zo rekent een prijswijziging nooit een oude prijs af en toont een uitverkocht
artikel zich ook zo. Dat is het hele punt.

**Alles wat de klant wél gekocht heeft is bevroren.** Een bestelregel bewaart
wat er op het scherm stond op het moment van bestellen:

```
OrderLine { productId, naam, merk, artikelnummer,
            priceCents, discountPercent, vatPercent, quantity }
```

De catalogus van morgen is geen bewijs van gisteren. Een artikel kan vervallen,
van naam veranderen of duurder worden; de factuur van vorige maand moet
hetzelfde blijven zeggen. **Dit is de fout die je niet meer kunt repareren**:
wie alleen het artikelnummer bewaart, kan over een jaar niet meer vertellen wat
hij verkocht heeft.

---

## De entiteiten

### Product (niet opgeslagen — het contract van de adapter)

Eén type waar de hele winkel mee werkt. De adapter vertaalt de leverancier
hiernaartoe; componenten kennen alleen dit.

| Veld | Opmerking |
|---|---|
| `id` | string, van de leverancier |
| `slug` | leesbaar, met het id erin zodat hij terug te vertalen is |
| `name`, `brand` | |
| `priceCents` | integer, inclusief btw |
| `listPriceCents` | de "van"-prijs; alleen met 30 dagen geschiedenis |
| `discountPercent` | wat er werkelijk is toegepast, niet wat er gevraagd was |
| `availability` | drie toestanden, geen boolean: op voorraad, wordt besteld, uitverkocht |
| `stock` | van de aanbieding waarvan ook de prijs komt |
| `imageUrl` | mag ontbreken |
| `specs` | de eigenschappen, met het label van de leverancier |

**Verzin geen extra velden in een component.** Ontbreekt er iets, dan verandert
dit type — en dan ziet iedereen meteen wat er allemaal op vastzit.

### Order

| Veld | Opmerking |
|---|---|
| `reference` | het nummer dat de klant ziet; mag raadbaar zijn |
| `accessToken` | de sleutel waarmee hij zijn bestelling terugvindt; mag dat **niet** |
| klantgegevens | naam, adres, mail, telefoon |
| `lines[]` | bevroren, zie boven |
| bedragen | subtotaal, verzendkosten, korting, btw, totaal — allemaal in centen |
| `status` | aangemaakt → betaald → verzonden |
| `paymentId` | van de betaaldienst; nodig voor een terugbetaling |
| `notifiedAt` | wanneer de bevestiging eruit ging — zodat hij niet twee keer gaat |
| `purchasedAt` | wanneer er bij de leverancier is ingekocht |

Twee velden die je pas mist als je ze niet hebt: `notifiedAt` (een webhook kan
twee keer komen) en `purchasedAt` (anders weet de eigenaar niet meer welke
bestelling hij al heeft ingekocht).

### Invoice

Apart van de bestelling, met een **eigen opeenvolgende nummerreeks zonder
gaten**. Dat is een eis van de belastingdienst, geen voorkeur. Het nummer komt
uit een teller in de database, in dezelfde transactie als de factuur zelf.

Een creditfactuur bij een terugbetaling heeft een **eigen reeks**. Een
terugboeking zonder creditfactuur klopt niet in de boekhouding. De opbouw van
het document staat in `docs/FACTUUR.md`.

### DiscountRule

Een regel wijst een **groep** aan, geen lijst artikelen. "15% op categorie X"
is één rij, geen tweehonderd. Dat scheelt opslag en vooral werk bij het
bijhouden van prijzen.

| Veld | Opmerking |
|---|---|
| `scope` | product / groep / soort / hele familie |
| `target` | waar hij op slaat |
| `percent` | |
| `startsAt`, `endsAt` | |
| `disabledAt` | **stoppen is niet weggooien** |

Een afgelopen actie blijft staan met een einddatum. Weggooien betekent dat je
over een half jaar niet meer kunt verklaren waarom een oude bestelling die
prijs had.

### PriceHistory

Per artikel per dag de prijs. Dit is wat de wettelijke "van"-prijs mogelijk
maakt: de laagste prijs van de afgelopen 30 dagen. **Begin hier vroeg mee** —
een geschiedenis die je vandaag niet opbouwt, heb je over dertig dagen nog
steeds niet.

### Return

| Veld | Opmerking |
|---|---|
| `reference` | eigen nummer |
| `orderReference` | |
| `emailKey` | waarmee de aanvraag gecontroleerd is |
| `reason` | herroeping / verkeerd geleverd / beschadigd / defect |
| `lines[]` | wat er terugkomt, met aantallen |
| `status` | aangevraagd → ontvangen → terugbetaald / afgewezen |
| `refundedCents` | **wat de betaaldienst zegt te hebben teruggeboekt** |

De reden is geen administratie maar bepaalt wie de verzendkosten draagt.

---

## Waar twee verzoeken elkaar raken

Dit is de lijst waar een unieke sleutel of een transactie omheen moet. Een
controle in code is hier niet genoeg: tussen lezen en schrijven past een tweede
verzoek, en twee tabbladen zijn geen randgeval.

| Wat | Waarom het misgaat |
|---|---|
| Factuurnummer | twee bestellingen krijgen hetzelfde nummer |
| "Eén kortingscode per klant" | twee tabbladen gebruiken hem allebei |
| "Er loopt al een retour" | twee aanvragen, twee terugbetalingen, één pakket |
| Voorraad bij het afrekenen | twee klanten kopen hetzelfde laatste stuk |
| De nachtelijke taak | draait twee keer en meet dubbel |

**Het gereedschap:** een unieke sleutel in de database waar dat kan, en anders
een transactie die de rij vergrendelt. Laat de database het uitmaken.

---

## Migraties

- Genummerd, en **alleen toevoegend**. Een kolom weghalen doe je in een aparte
  migratie, ná de code die hem niet meer gebruikt.
- Elke migratie draait één keer en is te herhalen zonder schade.
- Hou een script dat verbinding én schema nakijkt, en draai het na elke deploy.
- Test een onbekende databasemogelijkheid eerst op een wegwerptabel. Gedeelde
  hosting staat niet alles toe, en dat merk je anders pas bij de deploy.

---

## Bewaren en opruimen

| Gegeven | Termijn | Waarom |
|---|---|---|
| Bestellingen en facturen | 7 jaar (NL) | fiscale bewaarplicht |
| Verlopen sessies | opruimen | ze stapelen op en niemand kijkt ernaar |
| Prijsgeschiedenis | ~90 dagen | 30 nodig, de rest is marge |
| Logboek van beheerhandelingen | zolang het account bestaat | |

Zet het opruimen in dezelfde dagelijkse taak als de rest. Een opruimfunctie die
nergens wordt aangeroepen is geen opruimfunctie — controleer dat ook echt.
