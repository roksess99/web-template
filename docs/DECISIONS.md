# Openstaande beslissingen — <<SHOP>>

Zolang een beslissing hier op **OPEN** staat: niet gokken, vragen.

Dit bestand is het geheugen van het project. Elke keuze die niet uit de code
af te leiden is hoort hier, met de reden erbij en met de datum. Een beslissing
zonder reden is over drie maanden niet van een toevalligheid te onderscheiden,
en dan wordt hij bij de eerste tegenslag omgegooid.

**Vorm van een vastgestelde beslissing:**

```
## 7. Korte titel — VASTGESTELD 2026-01-31

Wat er besloten is, in één alinea.

**Waarom dit en niet het alternatief.** Het alternatief benoemen is het
belangrijkste deel: dat is wat je later opnieuw zou overwegen.

GEMETEN 2026-01-31: het getal of de proef waarop het rust.

### Wat er niet in zit
Wat bewust is overgeslagen, zodat het later geen vergeten werk lijkt.
```

---

## 0. Framework en hosting — OPEN

De afweging zoals hij eerder viel, voor een serverrenderend framework op
gedeelde hosting:

- **Serverrendering is voor een webshop geen luxe.** Productpagina's moeten
  door een zoekmachine te lezen zijn, en de prijs mag niet pas na een
  JavaScript-ronde verschijnen.
- **Gedeelde hosting is goedkoop en knijpt.** Lees `docs/HOSTING.md` vóór de
  keuze: het aantal processen is er begrensd, native modules vallen om, en de
  bouwstap kan er anders aflopen dan op je eigen machine.
- **Eén taal voor front- en backend** scheelt een hele categorie fouten bij
  het rekenen met geld, omdat dezelfde functie beide kanten bedient.

**Te beantwoorden:** framework, hostingpartij, en of de bouw op de server
draait of dat je een gebouwde map uploadt.

---

## 1. Welke leverancier, en wat kan die API echt? — OPEN

Niet de verkooppraat maar het gemeten gedrag. Loop `docs/api/LEVERANCIER.md`
helemaal af vóórdat er een regel adaptercode komt.

Drie dingen die eerder pas achteraf bleken en het ontwerp raakten:

1. **Een catalogus is niet altijd te bladeren.** Eén van de twee API's daar kon
   alleen artikelen tonen bij een gekozen auto. Dat betekent: geen
   "alle artikelen"-pagina, geen aanbiedingenlijst, geen prijsmeting over die
   groep. Dat soort beperking bepaalt je navigatie en dus je hele UI.
2. **Meerdere verkopers per artikel.** Dan is "de prijs" een keuze en geen
   gegeven, en hangt de voorraad aan diezelfde keuze.
3. **Taal.** Artikelnamen komen uit de API en zijn niet te vertalen. Een
   tweetalige winkel heeft dus eentalige productnamen. Besluit dat bewust.

**Te beantwoorden:** welke API, welk token per onderdeel, rate limit,
dekking van het assortiment, en of bestellen via de API kan.

---

## 2. Wat verkopen we wel en niet? — OPEN

Een leverancier biedt bijna altijd meer dan je wilt verkopen. Zonder een harde
grens staat er na een update ineens iets in je winkel waar je garantie- en
retourteksten niet op kloppen.

**Leg het vast als allowlist in code**, niet als filter in de navigatie: ook
een directe URL naar een artikel buiten het assortiment hoort niets op te
leveren.

**Te beantwoorden:** welke productgroepen, welke bewust niet, en wat er gebeurt
met een groep die leeg blijft (een eerlijke lege staat, geen verzonnen aanbod).

---

## 3. Van inkoopprijs naar verkoopprijs — OPEN

Dit is de beslissing waar het meeste geld in zit en die het vaakst te laat
wordt genomen.

Wat je bij de leverancier aantreft en uit elkaar moet houden:

- **De inkoopprijs is bijna altijd exclusief btw**, de adviesprijs bijna altijd
  inclusief. Dat verschil is eerder pas na weken gezien, met 21% te hoge
  prijzen tot gevolg. Toon een schermafdruk van het platform van de
  leverancier naast je eigen berekening voordat je dit afsluit.
- **Een opslag per groep** werkt beter dan één percentage voor alles: kleine
  artikelen dragen een veel hogere opslag dan grote.
- **De sleutel waar die opslag aan hangt moet overal gelijk zijn.** Hangt hij
  aan de categorie, en komt een artikel via het zoekveld binnen zonder
  categorie, dan rekent de kassa een ander bedrag dan de pagina toonde.
- **Een ondergrens is noodzakelijk.** Een korting mag de marge opeten, nooit
  meer dan dat.

**Te beantwoorden:** vaste opslag of per groep, waar de regel aan hangt, wat de
ondergrens is, en wat er gebeurt als de leverancier geen adviesprijs levert.

---

## 4. Wie koopt er in: een mens of de code? — OPEN

Automatisch doorbestellen bij de groothandel klinkt als het hele punt van
dropshipping en is het grootste risico in het systeem: één fout in het aantal
of het artikelnummer is een echte, factureerbare bestelling.

Eerder bleef dit bewust **handwerk** zolang de eigenaar annuleringen van tien
minuten wilde kunnen honoreren. Voor een beginnende winkel is dat de goede
keuze: het dwingt de eigenaar langs elke bestelling.

**Als je het ooit automatiseert:** de call die echt bestelt mag nooit "even ter
controle" gedraaid worden, hoort achter een aparte sleutel, en krijgt een
idempotentiesleutel zodat twee pogingen niet twee bestellingen opleveren.

**Te beantwoorden:** met de hand of automatisch, en zo ja: wanneer precies, en
wat er gebeurt als de leverancier de bestelling weigert nadat de klant betaald
heeft.

---

## 5. Betaaldienst — OPEN

**Te beantwoorden:** welke dienst, welke methodes, en of je de webhook kunt
ontvangen (dat laatste bepaalt of een betaling die buiten de browser
terugkomt wel wordt afgehandeld).

Drie dingen die algemeen gelden:

- Het bedrag komt van de server, nooit uit de browser.
- De bevestigingsmail hangt aan de betaalstatus, niet aan de terugkeerpagina:
  een klant die zijn tabblad sluit heeft wél betaald.
- Een webhook kan twee keer komen. De afhandeling moet dat overleven.

---

## 6. Waar leven de bestellingen? — OPEN

Begin eenvoudig, maar weet wanneer eenvoudig niet meer kan. Bestellingen als
JSON-bestanden op de schijf werken verrassend lang — tot de eerste **teller**: factuurnummers moeten opeenvolgend zijn zonder gaten, en
"één kortingscode per klant" is niet af te dwingen zonder iets dat twee
gelijktijdige verzoeken uit elkaar houdt.

**De vuistregel:** zodra twee verzoeken tegelijk hetzelfde nummer kunnen
krijgen, heb je een database met transacties nodig. Niet eerder, maar dan ook
echt.

**Te beantwoorden:** welke database, en wie de migraties draait (op de server
of met de hand — dat laatste betekent dat de code en de database uit de pas
kunnen lopen bij een deploy).

---

## 7. Beheerpaneel — OPEN

**Te beantwoorden:** komt er een, waar leeft hij (eigen route, buiten de
taalstructuur), hoe wordt er ingelogd, en wie mag wat.

Wat goed uitpakt: inloggen vraagt mailadres, wachtwoord én een code
uit een authenticator-app in **één** formulier — een tweetrapsscherm vraagt om
een half-ingelogde toestand die je nergens voor nodig hebt. Verder: elke
handeling die geld of zichtbaarheid raakt in een logboek, met wie het deed.

---

## 8. Kortingen en acties — OPEN

> De regels en de rekenvolgorde staan uitgewerkt in `docs/PRIJZEN.md`; hier
> hoort alleen wat jij ervan kiest.

**Te beantwoorden:** op welk niveau kortingen kunnen (één artikel, een groep,
een soort), en of er kortingscodes komen.

Wat juridisch vastligt in de EU en dus het ontwerp bepaalt: een aangekondigde
prijsverlaging ("van/voor") moet de **laagste prijs van de afgelopen 30 dagen**
als referentie gebruiken. Dat betekent dat je die prijzen moet meten en
bewaren vóórdat een actie begint, anders mag die doorgestreepte prijs er niet
staan. Begin daar vroeg mee: een geschiedenis die je vandaag niet opbouwt heb
je over dertig dagen nog steeds niet.

---

## 9. Retourneren — OPEN

> De stroom en de wettelijke eisen staan in `docs/RETOUREN.md`.

**Te beantwoorden:** hoe een klant een retour aanmeldt, en hoe het geld
terugkomt.

Wat in Nederland (BW 6:230m e.v.) vastligt en in de knoppen terechtkomt:

- 14 dagen bedenktijd, zonder reden, met een modelformulier dat beschikbaar
  moet zijn.
- Bij volledige herroeping gaan ook de **oorspronkelijke verzendkosten** terug
  (de goedkoopste standaardmethode).
- Terugbetalen binnen 14 dagen, maar je mag wachten tot het pakket terug is.
- Waardevermindering mag in mindering worden gebracht.

Praktisch: terugbetalen via de oorspronkelijke betaling scheelt het uitvragen
van een rekeningnummer, en controleer een retouraanvraag altijd op
**ordernummer én mailadres** — een ordernummer alleen is te raden.

---

## 10. Privacy en cookies — OPEN

**Te beantwoorden:** welke gegevens je bewaart, hoe lang, en of er een
toestemmingsbanner nodig is.

Twee vragen die vaak door elkaar lopen en apart beantwoord moeten worden:

1. **Wat komt er op het apparaat van de bezoeker?** Dat bepaalt of er een
   banner moet komen. Noodzakelijke opslag (winkelwagen, taal, thema) mag
   zonder toestemming.
2. **Wat bewaren wij op onze server?** Dat bepaalt wat er in de
   privacyverklaring moet staan.

Zodra er iets bijkomt dat geen van beide is — analytics, een advertentiepixel,
een ingesloten video, een chatwidget — is een banner **mét voorafgaande
blokkering** verplicht. Zie `docs/PRIVACY.md` voor de werkwijze.

---

## 11. Marketingmail — OPEN

**Te beantwoorden:** komt er een nieuwsbrief, en zo ja hoe wordt toestemming
vastgelegd.

Let op het verschil: een bevestigingsmail en een beoordelingsuitnodiging vallen
onder de klantrelatie en mogen zonder toestemming vooraf. Een aanbiedingsmail
niet. Adressen verzamelen "voor later" mag niet — je moet het doel hebben
voordat je verzamelt.

---

## 12. Beoordelingen — OPEN

**Te beantwoorden:** komen ze er, en hoe voorkom je dat ze verzonnen zijn.

Wat vastligt: selectief publiceren (alleen de goede tonen) is een oneerlijke
handelspraktijk. Een uitnodiging per bestelling, met een token in de link als
bewijs van aankoop, en verbergen alleen met een vastgelegde reden.

Geen sterrengemiddelde in de zoekmachinemarkering zolang er geen echte
beoordelingen zijn — verzonnen cijfers zijn reden om de markering van de hele
site te negeren.

---

## 13. Verzending — OPEN

**Te beantwoorden:** welke vervoerder, welk tarief, vanaf welk bedrag gratis,
en wat er gebeurt bij een bestelling uit meerdere groepen (die kan bij twee
verschillende groothandels vandaan komen en dus twee pakketten worden).

---

## Vastgesteld

| Datum | Beslissing | Reden |
|---|---|---|
| | | |
