---
paths:
  - "src/app/**/*.{ts,tsx,css}"
  - "src/components/**/*.{ts,tsx}"
  - "messages/*.json"
---

# Frontend regels — <<SHOP>>

Laadt alleen bij UI-werk.

Dit bestand gaat over **hoe** je bouwt. Twee buren:
`docs/BRAND.md` zegt hoe het eruitziet, `docs/SCHERMEN.md` zegt **wat** er op
een scherm hoort en waarom. Alle drie lezen voordat je een nieuw scherm maakt.

## Kleur

Alle kleuren via CSS-variabelen, zodat donkere modus één plek raakt. Gebruik
`bg-background`, `bg-surface`, `text-foreground`, `text-muted`,
`border-border` — **geen vaste kleuren**. Een blok met een hardgecodeerde
donkere achtergrond blijft donker in lichte modus en leest dan als een fout.

Uitzonderingen mogen bestaan, maar alleen met een reden erbij in het
commentaar (een balk die als donkere app-balk ontworpen is, een scrim achter
een paneel, een element waarvan de kleur wettelijk vastligt).

De contrastregels staan in `docs/BRAND.md`. Reken elke nieuwe combinatie na.

### Native formulierelementen

Geef een `<select>` nooit een doorzichtige achtergrond. De browser rendert het
uitklapmenu dan met zijn eigen lichte achtergrond terwijl de tekstkleur van het
donkere thema wordt geërfd: grijs op wit, onleesbaar. Zet een expliciete
achtergrond- én tekstkleur op zowel het `<select>` als de `<option>`s.

### Uitklappanelen en `overflow-hidden`

Een sectie met `overflow-hidden` knipt élk absoluut gepositioneerd paneel af
dat eruit steekt. Zet die eigenschap op het element dat het écht nodig heeft,
niet op de sectie eromheen.

## Layout

- Mobile first. Max contentbreedte vastleggen en overal gebruiken.
- Spacing alleen via de schaal van het framework. Geen willekeurige pixels.
- Productgrid: 2 kolommen mobiel, 3 tablet, 4 desktop.

## Componenten

- Server Component tenzij er interactie nodig is.
- Een laadscherm hoort bij de route die écht op data wacht, nooit hoog in de
  boom. Te hoog betekent dat een pagina zonder data ook met een skelet begint,
  en dat een 404 als status 200 de deur uit gaat.
- Statische inhoud (kop, formulier, uitleg) hoort in de eerste HTML; wat een
  externe partij moet leveren zet je in een eigen laadgrens.
- Lege staat is een uitnodiging tot actie, niet alleen "geen resultaten".
- Artikelnummers en prijzen met `tabular-nums`, zodat kolommen uitlijnen.

## Toegankelijkheid (WCAG 2.2 AA)

- Contrast minimaal 4,5:1 voor tekst. Check elke nieuwe kleurcombinatie.
- Elke afbeelding heeft `alt`; decoratief krijgt `alt=""`.
- Focusring altijd zichtbaar. Nooit `outline: none` zonder vervanging.
- Formulierfouten in tekst én gekoppeld via `aria-describedby`. Niet alleen
  kleur, en niet alleen een rode rand.
- **Let op het verschil tussen naam en beschrijving.** Een label dat ook de
  uitleg omvat maakt die hele uitleg de naam van het veld; een schermlezer
  leest dan een alinea voor waar één woord hoorde te staan. Label kort, uitleg
  eronder, gekoppeld met `aria-describedby`.
- **Een knop die niet kan, zegt waarom.** `disabled` neemt de klik weg — ook
  die van het toetsenbord — en dan staat er een knop die niets doet en niets
  uitlegt; dat leest als een storing. Gebruik `aria-disabled` plus een antwoord
  bij de klik, in een `role="status"` zodat een schermlezer het voorleest
  zonder de focus te verplaatsen. Echt `disabled` blijft goed waar er niets uit
  te leggen valt.
- Bewegende inhoud die langer dan vijf seconden doorloopt moet te stoppen zijn
  (WCAG 2.2.2). Wil de eigenaar die knop niet in beeld, dan mag hij onzichtbaar
  zijn tot het toetsenbord hem bereikt — maar hij moet bestaan en focus kunnen
  krijgen. Respecteer daarnaast `prefers-reduced-motion`.
- Alles bereikbaar met Tab, in een logische volgorde.

## Teksten

- Geen hardgecodeerde tekst. Alles via de taalbestanden.
- Alle talen hebben dezelfde sleutels. Een ontbrekende sleutel hoort de bouw te
  laten falen, niet stilletjes de sleutelnaam op het scherm te zetten.
- Gebruik logische eigenschappen (`ms-`, `me-`, `ps-`, `pe-`, `start-`, `end-`):
  het kost niets en houdt de deur open voor een taal die rechts begint.
- Bedragen in de notatie van de markt, ook op anderstalige pagina's, als je
  maar in één land levert en de factuur in één taal is.

## Performance

- Afbeeldingen met expliciete breedte en hoogte tegen verspringen, en altijd
  een `sizes` zodra de foto meeschaalt — zonder dat kiest de browser de
  grootste variant, ook op een telefoon.
- Externe foto's door de eigen beeldoptimalisatie, met een eigen minimale
  cachetijd: media-servers van leveranciers zetten korte tijden, en dan haalt je
  server dezelfde foto telkens opnieuw op.
- Lazy loading behalve de eerste rij van het grid en het grootste beeld boven
  de vouw.
- Geen library groter dan 15 kB gzipped zonder te vragen.
- Budget: LCP < 2,5 s op 4G, CLS < 0,1.

## SEO

- Eén `<h1>` per pagina.
- Metadata op **elke** route: titel, omschrijving, canonical, taalvarianten.
  Ook als de gegevens uit een andere bron komen dan de rest van de pagina —
  een route die daarop uitvalt heeft geen titel en geen canonical, en dat zie
  je niet in de browser.
- De canonical draagt geen parameters die bij déze bezoeker horen.
- Productpagina's krijgen gestructureerde data met prijs en beschikbaarheid,
  en lopende tekst die uit dezelfde velden is opgebouwd. Een andere
  omschrijving in de markering dan op de pagina is reden om de markering te
  negeren.
- **Geen sterrengemiddelde zolang er geen echte beoordelingen zijn.**
- Markering beschrijft wat er op de pagina staat. Een vragenlijst hoort bij de
  pagina waar de vragen de hoofdinhoud zijn.
- Een pagina met alleen een productraster krijgt lopende tekst: een korte
  inleiding onder de `<h1>`, en uitleg onderaan.
- URL's zijn leesbare woorden in de taal van de winkel, niet de interne
  nummers van de leverancier. Verandert er een, laat de oude dan omleiden met
  een 308 — en doe dat in de laag vóór de pagina, want vanuit de pagina zelf is
  de HTML al onderweg.
