# Startprompt

> **Voor de eigenaar:** dit is het eerste wat je in Claude Code stuurt in een
> nieuw, leeg project. Plak de hele inhoud van dit bestand als bericht, óf zet
> deze map in het project en zeg: *"lees START-PROMPT.md en voer hem uit"*.
> Alles hieronder is gericht aan Claude.

---

Je begint aan een nieuwe webshop. Er staat nog geen code. Wat er wél is, is een
complete documentatieset uit een eerdere webshop: beslissingen, regels,
schermen, datamodel, en de lessen die geld hebben gekost. Die set is de basis
van dit project.

**Je opdracht in dit eerste gesprek is niet bouwen.** Hij is: lezen, begrijpen,
op de goede plek zetten, en mij vertellen wat ik moet beslissen. Schrijf geen
applicatiecode, installeer niets, en bedenk geen waarden die ik niet gegeven
heb.

## 1. Lees alles, in deze volgorde

De bestanden staan in `<<PAD NAAR DE TEMPLATE-MAP>>`.

1. `LEESMIJ.md` — hoe de set bedoeld is
2. `CLAUDE.md` — het instructiebestand
3. `docs/DECISIONS.md` — de open beslissingen; dit is de kern
4. `docs/SCHERMEN.md` en `docs/BEHEER.md` — wat er op de schermen hoort
5. `docs/DATAMODEL.md`, `docs/PRIJZEN.md`, `docs/RETOUREN.md` — de domeinlogica
6. `docs/MAIL.md`, `docs/FACTUUR.md` — wat er uitgaat naar de klant
7. `docs/api/LEVERANCIER.md` en `docs/api/VRAGEN.md` — de koppeling
8. `docs/PRIVACY.md`, `docs/HOSTING.md`, `docs/TESTEN.md`, `docs/CHECKLIST.md`
9. `docs/BRAND.md` — huisstijl, nog niet ingevuld
10. `.claude/rules/*.md` — de regels die per soort werk laden

Lees ze echt, niet alleen de koppen. Een flink deel van de inhoud is
contra-intuïtief en staat er omdat het één keer is misgegaan.

## 2. Zet ze op de goede plek

Verplaats ze naar de wortel van dit project, in deze indeling. **Verplaatsen,
niet kopiëren** — twee kopieën betekent dat er één achterloopt.

| Van | Naar |
|---|---|
| `CLAUDE.md` | `./CLAUDE.md` |
| `LEESMIJ.md` | `./docs/LEESMIJ.md` |
| `START-PROMPT.md` | niet overnemen; die is hiermee klaar |
| `.env.example` | `./.env.example` |
| `.claude/rules/*.md` | `./.claude/rules/` |
| `.claude/launch.json` | `./.claude/launch.json` — alleen als het dev-commando klopt |
| `docs/*.md` | `./docs/` |
| `docs/api/*.md` | `./docs/api/` |

Daarna:

- Zorg dat `.env` in `.gitignore` staat, en `.env.example` juist **niet**.
- Controleer dat elke `@docs/...`-verwijzing in `CLAUDE.md` naar een bestand
  wijst dat er echt is.
- De regelbestanden in `.claude/rules/` hebben bovenaan een `paths:`-blok. Pas
  die paden aan op de mappenindeling die we echt gaan gebruiken, anders laden
  ze nooit.
- `docs/api/LEVERANCIER.md` krijgt de naam van de echte leverancier zodra die
  bekend is.

## 3. Vertel me wat je begrepen hebt

Geef daarna één bericht terug met:

**a. Wat voor winkel dit volgens de documenten wordt** — in vijf zinnen, in je
eigen woorden. Als die samenvatting niet klopt, weet ik dat meteen.

**b. De tien regels die volgens jou het zwaarst wegen.** Niet de tien uit het
eerste bestand dat je las, maar die waarvan jij denkt: als ik deze vergeet,
kost het geld of moet het werk over. Zeg er per regel in één zin bij waarom.

**c. Alle plaatshouders die ik moet invullen**, als lijst, met per stuk wat je
nodig hebt om verder te kunnen. `LEESMIJ.md` heeft er een tabel van; controleer
of hij compleet is door zelf op `<<` te zoeken.

**d. De beslissingen uit `docs/DECISIONS.md` die als eerste beantwoord moeten
worden**, in de volgorde waarin ze elkaar blokkeren. Zeg erbij wat er niet kan
zolang een beslissing openstaat.

**e. Wat je in de set tegenstrijdig, onduidelijk of verouderd vindt.** Die set
komt uit een andere winkel met een andere leverancier; het is waarschijnlijk
dat er iets in staat dat hier niet opgaat. Noem het, en pas het nog niet aan.

## 4. Wat je in dit gesprek niet doet

- Geen applicatiecode schrijven, geen framework installeren, geen
  mappenstructuur aanmaken buiten wat hierboven staat.
- Geen plaatshouders invullen met iets dat je zelf bedacht hebt. Een `<<...>>`
  die blijft staan is informatie; een verzonnen naam is een fout die later voor
  waar wordt aangezien.
- Niets committen of pushen zonder dat ik "GO" zeg.
- Geen beslissing uit `DECISIONS.md` zelf nemen. Ook niet als het antwoord voor
  de hand ligt. Stel de vraag.

## 5. Hoe we daarna verder gaan

Zodra ik de plaatshouders heb aangeleverd:

1. Jij vult ze in, in één ronde, en meldt wat er is blijven staan.
2. Bij de huisstijl reken je de contrasttabel in `docs/BRAND.md` echt uit en pas
   je de regels eronder aan op de uitkomst.
3. Daarna beantwoorden we `DECISIONS.md` #0 tot en met #4, in die volgorde, en
   schrijf je elk antwoord daar op mét de reden.
4. Pas als de leverancier-API gemeten is — `docs/api/LEVERANCIER.md` ingevuld,
   met data en het woord GEMETEN erbij — begint fase 1 uit `CLAUDE.md`.

Houd die volgorde aan, ook als ik erop aandring eerder te beginnen. Bouwen op
een niet-gemeten API is precies waar de helft van die documenten over gaat.
