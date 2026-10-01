# Wat de shop opslaat

Onderbouwing bij de privacyverklaring op de site. De tabellen op die pagina
zijn voor de bezoeker; dit bestand zegt **hoe ze gecontroleerd zijn**, zodat
niemand ze uit het hoofd hoeft bij te werken.

Het gaat om twee verschillende vragen, en die worden makkelijk door elkaar
gehaald:

1. **Wat komt er op het apparaat van de bezoeker?** Dat bepaalt of er een
   toestemmingsbanner moet komen (in NL: Telecommunicatiewet art. 11.7a).
2. **Wat bewaren wij op onze eigen server?** Dat bepaalt wat er in de
   privacyverklaring moet staan (AVG): welke gegevens, waarvoor, hoe lang.

## Op het apparaat van de bezoeker

**Stel dit vast door de code af te zoeken op élke schrijver** — `localStorage`,
`sessionStorage`, `document.cookie` — en niet door rond te klikken. Een sleutel
die alleen bij het afrekenen ontstaat mis je met bladeren.

| Sleutel | Soort | Geschreven door |
|---|---|---|
| | | |

Vul hier ook in wat er **niet** gebruikt wordt (bijvoorbeeld: "sessionStorage
wordt nergens gebruikt, en de shop schrijft zelf geen cookies"). Dat is net zo
goed een bevinding, en het scheelt de volgende persoon het zoekwerk.

## Op onze eigen server

**Stel dit vast door de migraties langs te lopen** op kolommen die een persoon
aanwijzen — niet door de applicatie te bekijken.

| Tabel | Persoonsgegeven | Grondslag | Bewaartermijn |
|---|---|---|---|
| | | | |

Vuistregels voor de grondslag:

- **Uitvoering van de overeenkomst** (art. 6 lid 1 sub b) voor alles wat nodig
  is om de bestelling te leveren: naam, adres, mailadres.
- **Wettelijke plicht** (sub c) voor facturen. In Nederland is dat zeven jaar
  fiscale bewaarplicht; een bestelling is ook een boekstuk.
- **Gerechtvaardigd belang** (sub f) voor dingen als "één kortingscode per
  klant". Let op: dat betekent dat je het adres bewaart ook nadat de bestelling
  weg is, en dat is dan precies de bedoeling — zet het als eigen regel in de
  tabel op de pagina, anders lijkt het er stilletjes bij te horen.

Noteer per rij ook of hij **meeverdwijnt met de bestelling** of niet. Dat is
het verschil tussen "de klant uitschrijven" en "de klant vergeten", en het is
een keuze, geen detail.

## Partijen die gegevens van ons ontvangen

| Partij | Wat ze krijgen | Waarom |
|---|---|---|
| | | |

Twee dingen die hier vaak misgaan:

- **De betaaldienst wordt vergeten** omdat de verklaring van vóór de
  betaalkoppeling dateert. Loop deze lijst na bij elke nieuwe koppeling.
- **Een leverancier mag zonder naam**, maar niet zonder vermelding. Art. 13 lid
  1 sub e AVG vraagt "de ontvangers **of categorieën van ontvangers**" — "onze
  groothandel" is dus toegestaan, verzwijgen niet.

Let wel op wat de naam alsnog weggeeft: staan de productfoto's op het domein
van de leverancier, dan staat die naam in je HTML, in `og:image` en in de
zoekmachinemarkering. Dat is niet met een tekstwijziging op te lossen.

## Heb je een toestemmingsbanner nodig?

**Alleen voor wat er op het apparaat van de bezoeker gebeurt.** Wat een server
in zijn eigen database zet valt er niet onder; dat is een AVG-vraag.

Zonder banner mag:

- de winkelwagen, de bezorggegevens, de gekozen taal, het gekozen thema —
  allemaal óf noodzakelijk om de winkel te laten werken, óf het gevolg van een
  keuze die de bezoeker zelf maakt.

**De banner is verplicht zodra er iets bijkomt dat geen van beide is:**
analytics, een advertentiepixel, een ingesloten video, een chatwidget van een
derde. Dan moet weigeren net zo makkelijk zijn als accepteren, en mag er
**niets laden voordat er geklikt is**.

## Derden in de browser

Streef ernaar dat de browser van de bezoeker met niemand anders praat dan met
jou. Controleer dat in het netwerkpaneel, niet in de code:

- Lettertypen zelf hosten.
- Externe foto's door de eigen beeldoptimalisatie laten lopen, zodat ze over
  het eigen domein gaan.
- Opzoekdiensten (adres, voertuig, wat dan ook) **server-side** aanroepen, niet
  vanuit de browser — anders ziet die derde partij het IP-adres van elke
  bezoeker.

| Uitzondering | Waarom het toch mag |
|---|---|
| | |

## Bijwerken

Verandert er iets aan de **browseropslag**, dan moeten drie plekken mee:

1. de lijst in de privacypagina zelf
2. de teksten in alle talen
3. dit bestand

Komt er een **kolom of tabel met persoonsgegevens** bij, net zo. En werk de
datum "laatst bijgewerkt" bij, in alle talen: een verklaring met een oude datum
eronder is erger dan geen datum.
