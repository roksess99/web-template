---
paths:
  - "src/lib/pricing*"
  - "src/lib/cart/**"
  - "src/lib/checkout/**"
  - "src/lib/orders/**"
  - "src/lib/discounts/**"
  - "src/lib/invoices/**"
  - "src/lib/returns/**"
  - "src/app/api/**"
---

# Geld — regels die uit schade komen

Laadt bij werk aan prijzen, winkelwagen, afrekenen, bestellingen, kortingen,
facturen en retouren. Elke regel hieronder staat er omdat het mis is gegaan of
op een haar na mis ging.

## Rekenen

**Bedragen zijn integers in centen.** Nooit floats. `0.1 + 0.2` is in
JavaScript niet `0.3`, en een cent verschil op een factuur is een
boekhoudprobleem.

**Reken in bruto centen als je bron bruto is.** Een bedrag inclusief btw eerst
naar netto rekenen en er daarna weer btw bij optellen kost een cent aan
afronding — en dan staat er € 37,99 waar de leverancier € 38,00 zegt. Haal de
btw er pas uit bij het factureren, met een functie die dat exact doet.

**Afronden doe je bewust en één keer.** Leg in het commentaar vast welke kant
op en waarom. Een percentage dat je toont rond je naar beneden af: liever 19%
tonen bij een korting van 19,6% dan een percentage beloven dat de klant niet
terugziet in het bedrag.

## Wat de browser mag meesturen

**Een bedrag dat naar de betaaldienst gaat komt nooit uit de browser.** De
winkelwagen leeft in localStorage en is dus door de klant aan te passen. Wat de
browser mag meesturen is hoogstens een artikelnummer, een aantal en de tekst
van een kortingscode. Naam, prijs en voorraad komen vers uit de catalogus,
server-side, bij elk verzoek opnieuw.

**Reken op de server alles opnieuw uit.** Ook het subtotaal, ook de
verzendkosten, ook de korting. Het bedrag dat naar de betaaldienst gaat komt
uit dát document.

## Prijzen en voorraad

**Prijs en voorraad komen van dezelfde aanbieding.** Heeft de leverancier
meerdere verkopers per artikel, kies er dan één — de goedkoopste **mét**
voorraad — en neem alles daarvandaan. Een lege verkoper is geen aanbieding:
zijn prijs tonen belooft iets dat niet te koop is.

**Begrens het aantal op die voorraad.** Anders bestelt iemand er vier waar er
één ligt, en koopt de winkel de rest duurder in dan ze verkocht is.

**Een ondergrens gaat vóór het percentage.** Een korting mag de marge opeten,
nooit meer dan dat — ook niet als de leverancier zijn inkoopprijs verhoogt
nadat de actie is aangemaakt. Reken de korting dus uit op de plek waar de
inkoopprijs nog in beeld is.

Concreet: een actie van 20% op een artikel met 10% opslag levert **9%** korting
op. De maximale korting die een opslag toelaat is `opslag / (100 + opslag)`.
De volledige opbouw — prijsregel, actie, code, en hoe ze elkaar begrenzen —
staat in `docs/PRIJZEN.md`.

**Wat er terugkomt is wat er werkelijk is toegepast.** Ligt dat lager dan
gevraagd, dan hoort het beheerpaneel dat te laten zien in plaats van te doen
alsof het gelukt is.

## Buiten bereik: weigeren, niet afkappen

Een bedrag of aantal buiten het toegestane bereik wordt **geweigerd met een
melding**. Afkappen voelt veilig en is het niet. Eén keer gezien in het echte werk: een
getypte "-5" werd door een `Math.max(1, …)` een terugboeking van één cent, die
het retour meteen op "terugbetaald" zette — waarna de rest er niet meer
doorheen kon.

Zelfde regel bij een korting die te diep is, een aantal dat te hoog is, een
datum die achterstevoren staat. Liever een melding dan een waarde die stilletjes
iets anders wordt dan iemand bedoelde.

## Volgorde bij een betaling of terugbetaling

1. **Betaaldienst eerst, database daarna.** Andersom staat een mislukte
   terugboeking als "terugbetaald" in de administratie — en dan wacht de klant
   op geld dat nooit komt.
2. **Een idempotentiesleutel mee**, zodat twee tabbladen samen één boeking
   opleveren. Gebruik daarvoor je eigen referentie, niet een willekeurig getal.
3. **Schrijf weg wat de betaaldienst zegt**, niet wat het formulier vroeg. Met
   een idempotentiesleutel krijgt een tweede poging met een ánder bedrag de
   éérste boeking terug; volg het bankafschrift, niet het invoerveld.
4. **Lukt de boeking wél en de database niet**, dan moet dat luid zijn. Het
   kenmerk van de boeking in het logboek, en een melding op het scherm die
   zegt dat het met de hand bijgewerkt moet worden.

## Gelijktijdigheid

Twee verzoeken tegelijk is geen randgeval maar twee tabbladen.

**Laat de database het uitmaken, niet een controle in code.** Lezen en daarna
schrijven laat precies genoeg ruimte voor een tweede verzoek ertussen. Gebruik
een unieke sleutel waar dat kan ("één code per klant"), en anders een
transactie met een vergrendelde rij.

Dat geldt voor: factuurnummers, volgnummers, "één keer per klant", "er loopt al
een retour", en elke teller.

## Tellers en nummers

**Factuurnummers zijn opeenvolgend en zonder gaten.** Dat is een eis van de
belastingdienst, geen voorkeur. Ze komen dus uit een teller in de database, in
dezelfde transactie als de rij waar ze bij horen.

**Een ordernummer mag raadbaar zijn, een sleutel niet.** Wil je een klant zijn
bestelling laten terugvinden zonder account, geef hem dan een aparte token in
de link. Het ordernummer alleen is nooit genoeg bewijs; vraag er altijd iets
bij dat de klant weet (zijn mailadres bijvoorbeeld), en geef bij een misser
exact dezelfde melding als bij een niet-bestaand nummer — anders vertelt het
formulier of een nummer bestaat.

## Bestellingen

**De bevestiging hangt aan de betaalstatus**, niet aan de terugkeerpagina. Een
klant die zijn tabblad sluit heeft wél betaald.

**Een webhook kan twee keer komen.** Zet een vinkje dat de mail verstuurd is,
en zet dat vinkje pas ná een geslaagde verzending.

**Mislukt de mail, dan is de bestelling er nog steeds.** Laat zo'n fout niet de
afhandeling van de betaling omgooien, maar laat hem ook niet verdwijnen.

**Bewaar wat de klant zag.** Prijs, korting en btw op het moment van bestellen
horen in de bestelregel. De catalogus van morgen is geen bewijs van gisteren.
