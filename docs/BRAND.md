# <<SHOP>> — Huisstijl

**Dit document is leidend voor alle UI.** Staat er iets niet in, dan is dat een
vraag aan de eigenaar en geen eigen keuze. Wijk hier nooit van af omdat iets
"mooier" is; een huisstijl die per scherm een beetje anders is, is geen
huisstijl.

De waarden ontbreken nog. De **regels** staan er al wel, want die hangen niet
aan de kleur: zodra de hexwaarden er zijn hoeft alleen de invulling te
gebeuren, en dan klopt de rest vanzelf.

---

## Wat er van de ontwerper moet komen

Vraag dit in één keer op. De ervaring is dat je anders maanden met een gat
blijft zitten dat je niet meer kunt dichten.

- [ ] **Logo als SVG met outlines** — niet als PDF, niet als PNG, en niet als
      bestand dat een lettertype nodig heeft. Zonder outlines kun je het
      woordmerk niet zetten en moet de header het uit een lettertype
      namaken; dat ziet er net niet goed uit en is niet meer te repareren
      zonder de ontwerper.
- [ ] **Alle varianten**: volledig logo, alleen het beeldmerk, een lijnversie
      voor kleine formaten, en een versie voor donkere achtergrond.
- [ ] **Hexwaarden** van alle merkkleuren, niet een schermafdruk waar je ze
      uit moet pipetteren.
- [ ] **Namen van de lettertypen** plus de licentie om ze zelf te hosten.
- [ ] **De regels**: minimale afmeting, vrije ruimte, wat verboden is.

---

## Kleur

### Merkkleuren

| Token | Hex | Gebruik |
|---|---|---|
| `--accent` | `<<KLEUR_ACCENT>>` | Merkaccent |
| `--ink` | `<<KLEUR_INK>>` | Tekst, donkere achtergrond |
| `--grey` | `<<KLEUR_GREY>>` | Randen en lijnen |
| `--zinc` | `<<KLEUR_ZINC>>` | Lichte achtergrond, kaartvlakken |

### Semantische tokens — hier werkt de UI mee

Componenten gebruiken **nooit** de merkkleuren rechtstreeks. Ze gebruiken de
laag hieronder. Dat is wat donkere modus op één plek laat wonen in plaats van
in tweehonderd componenten.

```css
:root {
  --background: <<TOKEN_BACKGROUND>>;   /* paginavlak */
  --foreground: <<TOKEN_FOREGROUND>>;   /* gewone tekst */
  --surface:    <<TOKEN_SURFACE>>;   /* kaarten, invoervelden, rustige vlakken */
  --muted:      <<TOKEN_MUTED>>;   /* secundaire tekst — moet 4,5:1 halen */
  --border:     <<TOKEN_BORDER>>;   /* lijnen */
  --danger:     <<TOKEN_DANGER>>;   /* fouten */
  --success:    <<TOKEN_SUCCESS>>;   /* bevestiging, "op voorraad" */
  --warning:    <<TOKEN_WARNING>>;   /* "wordt besteld", let op */
}

.dark {
  /* dezelfde namen, andere waarden */
}
```

**`--muted` is bijna nooit het merkgrijs.** Merkgrijs is gekozen om mooi te
zijn naast het accent, niet om leesbaar te zijn op wit. Reken het na en maak
het donkerder tot het 4,5:1 haalt; het merkgrijs zelf blijft dan gewoon staan
voor randen en vlakken.

**Statuskleuren staan los van het accent.** "Op voorraad" mag nooit de
merkkleur zijn: dan leest een statusmelding als een knop.

### Contrast — eerst meten, dan gebruiken

WCAG 2.2 AA vraagt **4,5:1** voor normale tekst, **3:1** voor grote tekst
(vanaf 24px, of 19px vet) en voor UI-elementen zoals randen van invoervelden.

Dit is geen formaliteit maar de eerste vraag die de hele UI bepaalt. Veel
merkkleuren — vooral oranje, geel en lichtgroen — halen op wit niet eens 3:1.
Is dat zo, dan mag het accent **alleen als vlak** gebruikt worden en nooit als
tekst, en dat verandert elke knop, elke link en elk label op de site.

Vul in zodra de kleuren er zijn:

| Combinatie | Ratio | Toegestaan? |
|---|---|---|
| accent op wit | | |
| wit op accent | | |
| ink op accent | | |
| muted op wit | | |
| accent op ink (donkere modus) | | |

**De regels die hieruit volgen** (de invulling hangt af van de meting):

- De primaire knop is een accentvlak met de tekstkleur die de meting toestaat
  — wit óf de donkere merkkleur. Niet allebei, niet per scherm anders.
- Haalt accent-op-wit de eis niet, dan geen accentkleurige tekst en geen
  accentkleurige links op een licht vlak. Gebruik de tekstkleur met een
  accent-onderlijn.
- Het accent op de donkere kleur haalt meestal wél de eis; dat is dan het
  accent voor donkere modus.
- Een rand van een invoerveld is een UI-element: 3:1 tegen zijn achtergrond.

### Donkere modus

Niet een omgekeerd palet maar een eigen set waarden voor dezelfde tokens. Twee
dingen die altijd misgaan en die je hier moet vastleggen:

- **Een vast ingestelde kleur in een component blijft staan in de andere
  modus.** Een blok met een hardgecodeerde donkere achtergrond leest in lichte
  modus als een fout. Uitzonderingen mogen, maar met een reden in het
  commentaar.
- **Geef `body` expliciet een achtergrond.** Een doorzichtige pagina leent de
  kleur van iets anders en dat gaat ergens stuk.

---

## Logo

Bestanden in `public/brand/`:

| Bestand | Gebruik |
|---|---|
| `<<LOGO_BESTANDSNAAM>>.svg` | Volledig logo, lichte achtergrond |
| `<<LOGO_BESTANDSNAAM>>-dark.svg` | Volledig logo, donkere achtergrond |
| `<<LOGO_BESTANDSNAAM>>-mark.svg` | Alleen het beeldmerk |
| `<<LOGO_BESTANDSNAAM>>-mark-line.svg` | Lijnversie, voor kleine formaten |
| `favicon.svg` | Browsertabblad |
| `<<LOGO_BESTANDSNAAM>>-social.png` | Deelplaatje, 1200×630 |
| `<<LOGO_BESTANDSNAAM>>-mail.png` | Voor in de mailsjabloon (PNG, want mailclients kennen geen SVG) |

**Regels** (door de ontwerper in te vullen, daarna hard):

- Minimale afmeting: `<<LOGO_MIN_AFMETING>>`. Daaronder de lijnversie, want fijne vormen
  lopen dicht en worden modderig.
- Vrije ruimte rondom: `<<LOGO_VRIJE_RUIMTE>>`.
- Nooit uitrekken, roteren, van schaduw voorzien of inkleuren.
- Het accent komt **één keer** per logo voor. Twee accentelementen naast elkaar
  maken geen van beide bijzonder.

---

## Typografie

| Rol | Lettertype | Gewicht | Opmerking |
|---|---|---|---|
| Woordmerk | `<<FONT_WOORDMERK>>` | | Alleen in het logo, nooit in de UI |
| Koppen | `<<FONT_KOPPEN>>` | 700 | `letter-spacing: -0.02em` |
| Body | `<<FONT_BODY>>` | 400 | `line-height: 1.6` |
| Labels | `<<FONT_LABELS>>` | 600 | hoofdletters, ruime letterafstand |

**Typeschaal.** Leg er één vast en blijf erop; losse pixelmaten per scherm zijn
waaraan je een ontwerp zonder systeem herkent. Een bruikbare schaal:

```
12 · 14 · 16 · 18 · 20 · 24 · 30 · 36 · 48
```

Lopende tekst rond de 65 tekens breed. Langer leest slecht, en op een
productpagina leest niemand het dan nog.

**Cijfers**: `font-variant-numeric: tabular-nums` voor prijzen, aantallen en
artikelnummers. Dat zijn data; die moeten in een kolom uitlijnen.

**Zelf hosten**, niet van een extern lettertypedomein laden. Dat scheelt een
derde partij in de privacyverklaring en een verbinding bij het laden.

---

## Ruimte, randen en schaduw

```
spacing  4 · 8 · 12 · 16 · 24 · 32 · 48 · 64
radius   <<RADIUS_KLEIN>> klein (knoppen, velden) · <<RADIUS_GROOT>> groot (kaarten)
shadow   één zachte schaduw, voor wat echt boven de pagina zweeft
```

**Niet alles is een kaart.** Rand, vlak, afronding en schaduw zeggen alle vier
"dit is een los object". Geef je ze aan elk blok, dan is er geen hiërarchie
meer en ziet de pagina eruit als een lijst dozen. Besteed ze aan het ene ding
dat de aandacht moet hebben.

---

## Componenten — hoe het merk eruitziet in de praktijk

Dit zijn geen implementatiedetails maar merkbeslissingen. Leg ze één keer vast
en dan zijn ze overal hetzelfde.

| Component | Vorm |
|---|---|
| Primaire knop | Accentvlak, tekstkleur volgens de contrastmeting, `<<RADIUS_KLEIN>>` |
| Secundaire knop | Omlijnd, transparante achtergrond, tekst in `--foreground` |
| Tertiair | Alleen tekst met onderlijn |
| Invoerveld | Achtergrond `--surface`, rand `--border`, fout: rand `--danger` **plus** tekst |
| Statusbadge | Eigen kleurpaar per status, altijd met icoon **én** woord |
| Kortingsvlag | Accentvlak, kort: `-15%` |
| Focus | Zichtbare ring in `<<KLEUR_FOCUS>>`, nooit `outline: none` zonder vervanging |

**Eén primaire actie per scherm.** Twee accentknoppen naast elkaar laten de
klant kiezen waar niets te kiezen valt.

**Kleur draagt nooit alleen de boodschap** (WCAG 1.4.1). Elke status heeft een
eigen icoon en een eigen woord, niet alleen een kleur.

---

## Beeld

- **Productfoto's** komen van de leverancier en zijn niet te sturen. Zet ze op
  een vast vlak met een vaste verhouding en `object-fit: contain`, anders
  springt de pagina bij elke foto.
- **Geen foto?** Een eigen plaatshouder met het beeldmerk, gedempt. Nooit een
  gebroken plaatje en nooit een lege ruimte.
- **Sfeerbeeld** (<<SFEERBEELD_JA_NEE>>): afspreken met de eigenaar. Gekochte
  stockfoto's die niet bij het assortiment horen doen meer kwaad dan goed.
- **Iconen**: één set, één stijl (lijn óf gevuld, niet door elkaar), één
  lijndikte. Liever zelf tekenen dan een tweede set erbij halen.

---

## Tone of voice

<<TONE_OF_VOICE>>

Wat bij een webshop vrijwel altijd geldt:

- Direct en zakelijk, geen marketingtaal. De klant zoekt een product, geen
  belevenis. "Past op jouw <<PRODUCTSOORT>>" is beter dan "Ontdek onze collectie".
- Een knop zegt wat er gebeurt: "Bestellen en betalen", niet "Versturen".
- Een foutmelding zegt wat er misging én wat de klant eraan kan doen. Geen
  excuses, geen vaagheid.
- Een lege staat is een uitnodiging, geen mededeling. Niet "geen resultaten"
  maar "niets gevonden voor X — probeer Y".
- Noem dingen zoals de klant ze noemt, niet zoals het systeem ze noemt.

---

## Als de huisstijl binnenkomt

1. Vul de hexwaarden in, en de semantische tokens erbij.
2. **Reken de contrasttabel uit** en vul "toegestaan?" in. Pas daarna de regels
   eronder aan op wat eruit komt.
3. Zet de logobestanden in `public/brand/` met de namen uit de tabel.
4. Vul de lettertypen in, host ze zelf, en zet de typeschaal in de CSS.
5. Loop de componenttabel langs en leg per rij de echte waarde vast.
6. Haal elke plaatshouder (`<<NAAM>>`) uit dit bestand. Wat er dan nog staat, is een gat dat
   iemand moet dichten.
