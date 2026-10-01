# <<LEVERANCIER>> — integratienotities

Bron: `<<naam van de documentatie>>`, aangeleverd <<datum>>.

| Wat | Waarde |
|---|---|
| Host | `<<...>>` |
| Base path | `<<...>>` |
| Auth | `<<header of parameter>>` → `<<ENV_SLEUTEL>>` |
| Rate limit | `<<... per minuut>>` — geldt voor de héle winkel |
| Formaat | JSON / XML. Let op welke numerieke velden als **string** terugkomen |

**Dit bestand is een invulformulier.** Elk antwoord krijgt het woord
**GEMETEN** met de datum. Wat niet gemeten is, is een aanname — en aannames
over een leveranciers-API kosten meer tijd dan het bouwen zelf.

---

## 1. Werkt het token, en op welk platform?

Veel groothandels draaien per land een eigen platform met een eigen base path.
Taal, assortiment én beschikbaarheid van endpoints kunnen per platform
verschillen.

- [ ] Token werkt op `<<platform>>`
- [ ] Welke platformen bestaan er, en welke geven JSON (en niet stilletjes een
      HTML-pagina)?
- [ ] Is er een testomgeving, of is alles live?

GEMETEN <<datum>>: <<antwoord>>

---

## 2. Hoe blader je door het assortiment?

- [ ] Is er een categorieboom? Hoe diep?
- [ ] Zijn de categorie-id's **stabiel over de tijd**?

> Reken daar niet op. Eerder gemeten: nummers die in september waren
> vastgelegd wezen een maand later naar de groep ernaast. Dat is stil kapot — geen fout, de link
> werkt, hij wijst alleen naar iets anders. **Leg dus geen id's vast in code**
> als je ze niet hebt gecontroleerd; zoek de groep op naam op in de boom die je
> toch al ophaalt.

- [ ] Kun je artikelen opvragen **zonder** eerst iets anders te kiezen?

> Dit bepaalt of je een "alle artikelen"-pagina, een aanbiedingenlijst en een
> nachtelijke prijsmeting kunt bouwen. Kan het niet, dan is dat geen detail
> maar een vorm die je hele navigatie bepaalt.

GEMETEN <<datum>>: <<antwoord>>

---

## 3. Zoeken

- [ ] Op naam? In welke taal?
- [ ] Op artikelnummer, EAN, fabrikantsnummer?
- [ ] Werkt zoeken zonder de rest van de context (categorie, voertuig, …)?
- [ ] Hoe nauwkeurig is het? Zoek op een term en kijk wat er bovenaan staat.

> Zoeken zonder context haalt rommel naar boven. Eerder gemeten: een
> gebruikelijke zoekterm gaf als eerste treffer een onderdeeltje van € 0,28. Als je zoekresultaten ergens
> prominent toont, heb je een ondergrens of een naamfilter nodig.

GEMETEN <<datum>>: <<antwoord>>

---

## 4. Paginering

- [ ] Begint de telling bij 0 of bij 1?
- [ ] Wat is het maximum per pagina, en wat gebeurt er als je meer vraagt?
- [ ] Kun je twee verzoeken tegelijk doen?

> Eerder gemeten: twee gelijktijdige verzoeken op dezelfde categorie met het
> maximum gaven bij één van de twee een HTTP 500 — en de adapter ving dat op met
> een lege lijst, dus de halve categorie verdween zónder foutmelding. Haal
> pagina's sequentieel op tot je het tegendeel gemeten hebt.

GEMETEN <<datum>>: <<antwoord>>

---

## 5. Hoe groot is een antwoord?

- [ ] Meet de omvang bij het maximale aantal per pagina.

> Boven een paar megabyte weigeren sommige caches stilletjes. Eerder gemeten:
> een antwoord van 4 MB was te groot voor de cache van het framework, waardoor
> élke filterklik opnieuw vier megabyte ophaalde. Als dat zo is: een eigen cache
> van een paar minuten in het geheugen.

GEMETEN <<datum>>: <<antwoord>>

---

## 6. Prijzen

- [ ] Welk veld is de **inkoopprijs**, en is die inclusief of exclusief btw?
- [ ] Welk veld is de **adviesprijs**, en is die inclusief of exclusief btw?
- [ ] Komen ze als getal of als string terug?
- [ ] Zijn er meerdere prijsblokken per verkoper (staffel, vracht, borg)?

> **Controleer dit tegen een schermafdruk van het platform zelf.** De aanname
> dat beide bedragen op dezelfde basis staan kostte eerder weken met 21% te
> hoge prijzen. De gangbare conventie: inkoop tussen bedrijven is netto, een
> adviesprijs voor de consument is bruto. Maar meet het.

GEMETEN <<datum>>: <<antwoord>>

---

## 7. Voorraad — de duurste vraag van allemaal

- [ ] Staat er voorraad op het **artikel** of op de **aanbieding van een
      verkoper**?
- [ ] Als beide: komen die overeen?

> **Eerder gemeten: níet, en dat kostte geld.** Het voorraadgetal bovenin het artikel
> kwam bij de helft van de artikelen niet overeen met de verkopers eronder, en
> was soms een veelvoud: één artikel meldde 2741 stuks terwijl twintig
> verkopers samen 981 hadden en de goedkoopste er één had.
>
> Gevolg: de winkel toonde de prijs van verkoper A en de voorraad van het hele
> platform. Wie vier stuks bestelde kon er één tegen die prijs krijgen en moest
> de rest duurder inkopen — gemiddeld € 60 verschil per set van vier, te
> betalen door de winkel.
>
> **De regel die daaruit volgt:** kies één aanbieding — de goedkoopste mét
> voorraad — en neem prijs, voorraad en levertijd allemaal daarvandaan. Begrens
> het aantal dat een klant kan bestellen op die voorraad.

GEMETEN <<datum>>: <<antwoord>>

---

## 8. Foto's

- [ ] Komt er een bruikbare URL mee, of een plaatshouder met een formaatcode?
- [ ] Zijn de foto's echt, of voor elke categorie hetzelfde generieke bestand?

> Dat laatste komt vaker voor dan je denkt. Eerder gemeten: een categorie-API
> gaf voor élke categorie exact hetzelfde bestand van 1789 bytes. Controleer de omvang
> van een paar foto's voordat je ze in je ontwerp opneemt.

- [ ] Van welk domein komen ze? Dat domein komt in je HTML te staan, in
      `og:image` en in de zoekmachinemarkering. Wil de eigenaar de naam van
      zijn leverancier niet weggeven, dan moeten de foto's via het eigen
      domein lopen.

GEMETEN <<datum>>: <<antwoord>>

---

## 9. Taal en namen

- [ ] In welke talen komen artikelnamen en eigenschappen?
- [ ] Is er een Engels platform?

> Is die er niet, dan is dat niet met een woordenlijst op te lossen: het gaat
> om miljoenen vrije-tekstvelden van honderden fabrikanten. De anderstalige
> pagina's tonen dan productnamen in de taal van de leverancier. Besluit dat bewust
> en zet het in DECISIONS, anders gaat iemand het later "repareren".

GEMETEN <<datum>>: <<antwoord>>

---

## 10. Bestellen

- [ ] Kan het via de API? Welke stappen?
- [ ] Is er een overeenkomst of vrijgave nodig per groothandel?
- [ ] Wat gebeurt er bij een aantal dat niet op voorraad is?
- [ ] Wordt een bestelling met artikelen van twee verkopers **twee**
      bestellingen?

> ⚠️ **De call die echt bestelt plaatst een echte, factureerbare bestelling.**
> Draai hem nooit "even ter controle". Zet hem achter een aparte sleutel en
> geef hem een idempotentiesleutel.

GEMETEN <<datum>>: <<antwoord>>

---

## 11. Foutgedrag

- [ ] Komen fouten als HTTP-status terug, of als foutcode in een 200-antwoord?
- [ ] Welke endpoints falen structureel? (Noteer ze — een kapot onderdeel van
      de leverancier mag geen foutpagina in jouw winkel opleveren.)
- [ ] Wat zegt de API als je over de rate limit gaat?

GEMETEN <<datum>>: <<antwoord>>

---

## Mapping naar ons datacontract

Vul dit in zodra `lib/catalog/types.ts` staat. Dit is de tabel waar iemand
over een jaar naar kijkt als een veld leeg blijkt.

| Ons veld | Bron bij de leverancier | Opmerking |
|---|---|---|
| `id` | | |
| `name` | | |
| `priceCents` | | integer, in centen |
| `availability` | | van de gekozen aanbieding |
| `stock` | | van diezelfde aanbieding |
| `imageUrl` | | |

## Kosten per paginaweergave

Zet het loggen van uitgaande verzoeken aan en tel ze, één keer, per soort
pagina. Dit is de goedkoopste meting die er is en hij vindt altijd iets:
eerder bleek de navigatie dezelfde lijst drie keer op te halen, en het
filterblok van een categoriepagina duurder dan de artikelen zelf.

GEMETEN <<datum>>: <<antwoord>>
