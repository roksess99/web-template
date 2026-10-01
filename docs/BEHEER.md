# Beheerpaneel — wat de eigenaar nodig heeft

De winkel is voor de klant, dit paneel is voor de eigenaar. Dat is een ander
soort scherm: het wordt niet gelezen maar **bediend**, elke dag, vaak haastig
en vaak op een telefoon.

Inloggen, rechten en het logboek staan in `.claude/rules/beveiliging.md`. Dit
bestand gaat over wat er op de schermen hoort.

---

## De grondvraag

**Een beheerscherm beantwoordt "wat moet ik nu doen", niet "hoe gaat het".**
Een getal waar geen handeling bij hoort is decoratie. Bouw geen grafieken die
niemand gebruikt om een beslissing te nemen — dat is de makkelijkste manier om
een paneel te maken dat er indrukwekkend uitziet en waar de eigenaar niets aan
heeft.

Bij elk blok dat je toevoegt: **welke handeling volgt hieruit?** Is het
antwoord "geen", dan hoort het er niet.

---

## Dashboard

Het eerste scherm na het inloggen. Hooguit een handvol tegels, en elke tegel is
een deur naar werk dat openstaat.

| Tegel | Waarom het werk is |
|---|---|
| Bestellingen die nog ingekocht moeten worden | dit is de dagelijkse taak |
| Omzet deze maand | de enige die puur informatief mag zijn |
| Openstaande retouren | hier wacht een klant op geld |
| Beoordelingen die aandacht vragen | een lage score hoort beantwoord |

Zet bij een bedrag dat later nog verandert erbij **wat het is**: "omzet, vóór
retouren" is een ander getal dan "omzet". Een tegel die stilletijd iets anders
betekent dan de boekhouding is erger dan geen tegel.

**Een lege nieuwe winkel heeft lege tegels.** Zorg dat die er dan niet uitzien
als een fout: "nog geen bestellingen" is een zin, geen streepje.

---

## Bestellingen — de werkvoorraad

### De lijst

Dit is geen archief maar een wachtrij. Sorteer op wat er nog moet gebeuren,
niet alleen op datum.

- Per regel: ordernummer, datum, klant, bedrag, status, en **of er al
  ingekocht is**.
- Zoeken op ordernummer en op mailadres. Dat zijn de twee dingen waarmee een
  klant belt.
- Filteren op status, en een duidelijk onderscheid tussen "betaald, nog niets
  gedaan" en "afgehandeld".
- Paginering. Een lijst die alles ophaalt werkt prima bij vijftig bestellingen
  en valt om bij vijfduizend.

### De bestelling zelf

Dit scherm is een **werkbon**. De eigenaar moet hiermee kunnen inkopen zonder
iets op te zoeken.

- De regels met het artikelnummer **zoals de leverancier het kent**, niet zoals
  de klant het ziet.
- Komen de artikelen bij twee bronnen vandaan, dan staat dat er met een
  waarschuwing bij: dat worden twee inkooporders.
- Het afleveradres in één blok, zo over te nemen.
- Bedragen zoals ze op de factuur staan: subtotaal, korting, verzending, btw.
- **Een knop "ingekocht" met een datum.** Zonder dat weet niemand na een week
  nog welke bestelling al gedaan is — en dat is precies de fout die een klant
  laat wachten.
- De betaalstatus, met het kenmerk van de betaaldienst erbij voor als er iets
  nagezocht moet worden.

---

## Facturen

- Opeenvolgend genummerd, zonder gaten. Zie `docs/DATAMODEL.md`.
- Te downloaden als PDF, en terug te vinden op nummer.
- Een overzicht per maand met de btw apart: dat is wat de boekhouder vraagt.
- **Een creditfactuur bij een terugbetaling**, met een eigen nummerreeks. Een
  terugboeking zonder creditfactuur klopt niet in de boeken.

---

## Prijzen

Hier stelt de eigenaar zijn marge in. Twee dingen maken het verschil tussen
bruikbaar en eng:

- **Laat zien wat een instelling doet met een echt artikel** voordat hij
  opslaat. "15% opslag" zegt niets; "dit filter gaat van € 8,40 naar € 9,66"
  zegt alles.
- **Zeg het als de ondergrens ingrijpt.** Vraagt de eigenaar 40% korting en kan
  de marge er maar 9%, dan hoort het paneel dat te melden — niet stilletjes 9%
  toepassen en 40% blijven tonen.

De volledige opbouw — prijsregel, actie, code en hun onderlinge grenzen — staat
in `docs/PRIJZEN.md`.

Leg vast waaraan een prijsregel hangt, en gebruik overal dezelfde sleutel. Een
regel die op de ene pagina geldt en bij het afrekenen niet, laat de klant een
ander bedrag betalen dan hij zag.

---

## Kortingen en acties

- Een actie wijst een groep aan, met een begin- en einddatum.
- **Stoppen is niet weggooien.** Een afgelopen actie blijft staan, anders is
  over een half jaar niet meer te verklaren waarom een oude bestelling die
  prijs had.
- Overlappende acties: zeg welke wint (de hoogste, en niet stapelen) en toon
  dat ook.
- Kortingscodes zijn iets anders dan acties: percentage, minimumbedrag,
  looptijd, een maximum aantal, en eventueel "één keer per klant" — dat laatste
  hangt aan een unieke sleutel in de database, niet aan een controle in code.
- Bij een actie op een groep die je niet kunt uitlijsten: zeg dat in het
  paneel. Anders zet de eigenaar een actie aan die werkt maar nergens
  zichtbaar is, en denkt hij dat er iets stuk is.

---

## Retouren

- Aanvragen komen binnen met ordernummer en mailadres, al gecontroleerd.
- Drie knoppen in volgorde: **ontvangen** → **terugbetalen** → of **afwijzen
  met reden**.
- **Terugbetalen is de enige knop in het hele paneel die geld verplaatst.** Dus
  met een bevestiging én het bedrag erbij, en het bedrag komt uit de database —
  nooit uit het formulier.
- Wat er in de administratie komt is wat de betaaldienst zegt te hebben
  teruggeboekt. Verschilt dat van wat er gevraagd was, dan zegt het scherm dat.
- Afwijzen vraagt een reden, en die reden wordt vastgelegd.

---

## Beoordelingen

- Verschijnen meteen; verbergen kan alleen **met een reden**. Selectief
  publiceren is een oneerlijke handelspraktijk.
- Antwoorden werkt beter dan weghalen.
- Een verzoek om verwijdering moet uitvoerbaar zijn: daarvoor moet je weten
  welke beoordeling van wie is.

---

## Beheerders

- De eerste komt uit een opzetpagina die **zichzelf sluit** zodra er één is.
- Daarna met uitnodigingen, met een vervaltermijn op de link.
- Rechten per onderdeel, ook met één beheerder. Zodra er een tweede bijkomt wil
  je kunnen zeggen: wel bestellingen, geen prijzen.
- **Het logboek is zichtbaar in het paneel**, niet alleen in de database. Wie,
  wat, wanneer, waarop.

---

## Hoe een beheerscherm eruitziet

Dit is bewust anders dan de winkel. De winkel verleidt, het paneel werkt.

- **Dicht op elkaar.** Een tabel met twintig regels op een scherm, geen kaart
  per bestelling met veel wit eromheen.
- **Cijfers in kolommen** die uitlijnen (`tabular-nums`). Bedragen rechts.
- **Status als chip met een woord**, niet alleen een kleur.
- **Gevaarlijke knoppen zien er anders uit** en staan niet naast een knop die
  je vaak gebruikt. Terugbetalen, verwijderen en afwijzen vragen een
  bevestiging waarin staat wát er gaat gebeuren — en bij geld: hoeveel.
- **Geen merkaccent voor statussen.** Het accent is van de winkel; een
  statuskleur die eruitziet als een knop is verwarrend.
- **Eén taal.** Een paneel voor één persoon hoeft niet tweetalig te zijn.

### Formulieren die gebruikt worden

- **Een mislukt formulier houdt zijn waarden.** Niets is irritanter dan
  opnieuw invullen omdat één veld fout was. Dit gaat vaak stuk zonder dat
  iemand het merkt — controleer het expliciet.
- Na een geslaagde handeling: een melding die zegt wat er gebeurd is, en een
  scherm dat ververst is. Geen scherm dat nog de oude toestand toont.
- Een knop die iets verstuurt is tijdens het versturen uitgeschakeld en zegt
  dat hij bezig is. Twee keer klikken mag nooit twee handelingen opleveren.
- Fouten in gewone taal: "Het bedrag moet tussen € 0,01 en € 5,94 liggen" in
  plaats van "validatiefout".

### Op een telefoon

De eigenaar krijgt de bestelmelding op zijn telefoon en kijkt daar als eerste.
Minstens moeten werken: de bestelling bekijken, hem als ingekocht markeren, en
een retour afhandelen. Een tabel die op een telefoon uit beeld loopt is daar
onbruikbaar — laat kolommen vallen in plaats van horizontaal te scrollen.

---

## Wat er bewust niet in hoort

- **Analyses die niemand gebruikt.** Bezoekersaantallen, conversiegrafieken,
  een verkooptrechter: mooi, en geen enkele handeling volgt eruit. Komt er
  vraag naar, dan bouw je het dan.
- **Rechtstreeks in de database kunnen.** Een paneel dat willekeurige
  wijzigingen toestaat omzeilt elke regel die je in code hebt gezet.
- **Een knop die bestelt bij de leverancier**, zolang inkopen handwerk is. Een
  knop die er staat wordt een keer per ongeluk geraakt.
