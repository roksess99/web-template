---
paths:
  - "src/lib/catalog/**"
  - "src/lib/*provider*"
---

# Catalogus — regels voor de koppeling met de leverancier

Laadt bij werk aan de adapter. Dit is de laag tussen een API die je niet in de
hand hebt en een winkel die altijd moet werken.

## Het contract gaat voor

**`types.ts` is leidend.** Past de echte API er niet op, dan past de **adapter**
zich aan — nooit de componenten. Een veld dat de leverancier anders noemt is
een vertaling in de adapter, geen nieuw veld in de UI.

Componenten importeren alleen uit `types.ts`. Niet uit de adapter, niet uit de
schema's van de API. Zo kun je er een tweede leverancier naast zetten zonder
één component aan te raken.

**Verzin geen velden onderweg.** Ontbreekt er iets, dan verandert `types.ts` —
en dan zie je meteen wat er allemaal op vastzit.

## Server-side, altijd

De browser praat nooit rechtstreeks met de leverancier. Het token is een
secret, en de rate limit geldt voor de héle winkel: één bezoeker die
rechtstreeks mag bevragen kan de winkel voor iedereen stilleggen.

Zet een controle bovenin de adapter die gooit als hij toch in een browser
draait. Dat vangt de fout bij het bouwen in plaats van in productie.

## Alles door een schema aan de rand

Elk antwoord van de leverancier gaat door een schemavalidatie voordat het de
code in gaat. Daarbinnen is alles getypeerd en hoef je nergens meer te
controleren of een veld bestaat.

Let op numerieke velden die als **string** terugkomen — dat is bij
leveranciers-API's eerder regel dan uitzondering. Dwing ze af bij de rand, niet
met een `Number()` halverwege een berekening.

Een artikel dat niet door het schema komt wordt **overgeslagen**, niet
gerepareerd. Eén kapot artikel mag geen categoriepagina slopen.

## Zonder sleutel blijft de winkel staan

Is er geen token, dan valt de provider terug op een mock met verzonnen data.
Dat is geen speelgoed maar een eis: anders kan niemand aan de UI werken zonder
verbruik bij de leverancier, en valt de hele site om zodra een sleutel verloopt.

De mock levert dezelfde vorm als het echte ding, inclusief de rare gevallen:
een artikel zonder foto, een artikel zonder voorraad, een lange naam.

## Caching

Drie soorten, met elk een eigen duur:

| Soort | Duur | Waarom |
|---|---|---|
| Structuur (categorieën, merken) | uren tot een dag | verandert nauwelijks |
| Prijzen en voorraad | minuten | moet actueel zijn, maar niet per klik |
| Tellingen en aggregaten | een dag | duur om op te halen, zelden anders |

**Meet de omvang van je antwoorden.** Boven een paar megabyte weigeren sommige
caches stilletjes, en dan haal je bij elke klik opnieuw alles op zonder dat
iets daarover klaagt. Is dat zo: een eigen cache van een paar minuten in het
geheugen ernaast.

**Een cache mag nooit iets laten doorlopen dat is afgelopen.** Een actie die op
zijn einddatum stopt komt langs niemand die de cache kan wissen. Maak de
cachesleutel dus afhankelijk van wat er geldig is, niet alleen van de tijd.

## Rate limit

De limiet geldt voor de hele winkel, dus voor alle bezoekers tegelijk. Dat
betekent:

- Verzoeken bundelen waar het kan, en anders begrenzen hoeveel er tegelijk
  lopen.
- Tel één keer hoeveel calls een paginaweergave kost. Die meting vindt altijd
  iets — meestal een lijst die drie keer wordt opgehaald omdat drie componenten
  hem los nodig hebben.
- Een lijst die elke bezoeker nodig heeft hoort één keer opgehaald en
  doorgegeven te worden, niet per component.

## Fouten

**Een kapot onderdeel van de leverancier mag geen foutpagina opleveren.** Een
categorie die een serverfout geeft wordt een lege staat, met een logregel.

Maar: **vang niet alles af.** Een fout in het pad dat geld raakt — een prijs die
niet uit te rekenen is, een artikel dat tijdens het afrekenen verdwijnt — moet
luid zijn en de handeling tegenhouden. Het verschil is of de klant er geld aan
kwijt is.

Controleer of de API fouten in de **status** zet of in een foutcode binnen een
antwoord met status 200. Dat laatste komt vaak voor; dan is een geslaagde
HTTP-aanroep nog geen geslaagd verzoek.

## Prijs en voorraad

Zie `.claude/rules/geld.md`. De regel die hier thuishoort: **kies één
aanbieding en neem alles daarvandaan** — prijs, voorraad, levertijd. Mengen van
twee verkopers levert een winkel op die iets belooft wat niet te koop is.

## Identiteit van een artikel

- Het id van de leverancier is de sleutel, ook in de winkelwagen en in de
  bestelling.
- De URL is een leesbare slug mét dat id erin, zodat hij terug te vertalen is.
- Verandert een slug, laat de oude dan omleiden met een 308.
- **Leg geen id's van categorieën vast in code** zonder te hebben gemeten dat
  ze stabiel zijn over de tijd. Zoek op naam op in de boom die je toch al
  ophaalt.

## Twee catalogi naast elkaar

Komt er een tweede bron bij (een andere API, een andere productgroep), dan:

- Elke productgroep weet bij welke bron hij hoort; dat gaat mee in de
  winkelwagen, want anders weet je bij het afrekenen niet waar je het artikel
  moet opzoeken.
- Eén bestelling kan twee inkooporders worden. Zorg dat de beheerder dat ziet.
- Het contract blijft één type. Twee bronnen, één `Product`.
