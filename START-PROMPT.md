# Startprompt

> **Voor de eigenaar:** dit is het eerste wat je in Claude Code stuurt in een
> nieuw project. Plak de inhoud als bericht, óf zet de templatemap in het
> project en zeg: *"lees START-PROMPT.md en voer hem uit"*. Vul eerst
> `<<PAD_NAAR_TEMPLATE>>` hieronder in. Alles na de streep is gericht aan
> Claude.

---

Je begint aan een nieuwe webshop. Er staat nog geen applicatiecode. Wat er wél
is, is een documentatie- en controleset uit een eerdere webshop: regels,
beslissingen, contracten, state machines, hooks en een validator. Die set is de
basis van dit project.

**In dit eerste gesprek bouw je niets.** Je stelt vast wat er is, controleert
het, zet het op de goede plek na mijn akkoord, en vertelt me wat ik moet
beslissen. Geen applicatiecode, geen framework installeren, geen waarden
verzinnen die ik niet gegeven heb.

De set staat in `<<PAD_NAAR_TEMPLATE>>`.

## PHASE 0 — INVENTORY (alleen lezen)

Lees alles, echt, niet alleen de koppen. Volgorde:

1. `LEESMIJ.md`, `CLAUDE.md`, `docs/AUTHORITY.md`, `docs/CLAUDE_CODE.md`
2. `docs/DECISIONS.md` — de kern
3. `docs/STATE_MACHINES.md`, `docs/PAYMENTS.md`, `docs/IDEMPOTENCY.md`,
   `docs/DATAMODEL.md`, `docs/PRIJZEN.md`
4. `docs/SUPPLIER_RESILIENCE.md`, `docs/api/LEVERANCIER.md`, `docs/api/VRAGEN.md`
5. `docs/THREAT_MODEL.md`, `docs/TESTEN.md`, `docs/CI_CD.md`,
   `docs/OBSERVABILITY.md`, `docs/DISASTER_RECOVERY.md`
6. `docs/SCHERMEN.md`, `docs/BEHEER.md`, `docs/ACCESSIBILITY.md`, `docs/BRAND.md`
7. `docs/RETOUREN.md`, `docs/FACTUUR.md`, `docs/MAIL.md`, `docs/PRIVACY.md`,
   `docs/HOSTING.md`, `docs/CHECKLIST.md`
8. `.claude/rules/*.md`, `.claude/settings.json`, `.claude/hooks/guard.mjs`,
   `.claude/launch.json`, `.env.example`, `.gitignore`, `scripts/*.mjs`,
   `.github/workflows/*`

En het doelproject: welke bestanden er al zijn (een bestaande `.gitignore`,
`CLAUDE.md`, `.claude/`?) en of het een Git-repository is.

**In deze fase wijzig je niets.**

## PHASE 1 — VALIDATE (alleen lezen)

Draai in de templatemap en noteer de uitvoer letterlijk:

```bash
node scripts/validate-template.mjs
```

```bash
node scripts/test-guard.mjs
```

Kan een controle niet draaien (geen Node, andere fout): noteer `NOT RUN` met
de reden. Doe nooit alsof een controle geslaagd is.

## PHASE 2 — IDENTIFY CONFLICTS (alleen lezen)

Zoek, en noteer met bestand en sectie:

- tegenspraak tussen documenten, en tussen de set en wat je over dit project
  weet (de set komt uit een andere winkel met een andere leverancier);
- regels die in dit project niet opgaan of verouderd zijn;
- aannames over framework, database of hosting terwijl D-00/D-06 open staan;
- botsingen met bestanden die al in het doelproject staan.

Volg het conflictprotocol uit `docs/AUTHORITY.md`. **Los niets zelf op.**

## PHASE 3 — PROPOSE STRUCTURAL CHANGES

Stel voor, en wacht op mijn "GO":

- **Plaatsing:** de indeling van de templatemap is de indeling van het project
  (`CLAUDE.md`, `LEESMIJ.md`, `.claude/`, `docs/`, `scripts/`, `.github/`,
  `.gitignore`, `.env.example` in de wortel). Verplaatsen, niet kopiëren —
  twee kopieën betekent dat er één achterloopt. `START-PROMPT.md` gaat niet
  mee; die is hiermee klaar.
- **Samenvoegen** met wat er al staat (bijv. een bestaande `.gitignore`):
  noem per bestand wat je toevoegt. Niets overschrijven zonder dat ik het zie.
- **`paths:` in `.claude/rules/`**: kloppen ze met de mappenindeling die we
  gaan gebruiken? Zo niet, voorstel per regelbestand — anders laden ze nooit.
- **`.env`** staat in `.gitignore` en `.env.example` niet.

## PHASE 4 — APPLY CHANGES

Alleen wat ik in fase 3 heb goedgekeurd. Geen plaatshouders invullen met iets
dat je zelf bedacht hebt; geen beslissing in DECISIONS nemen. Niets committen
of pushen — dat doe ik, of jij na een expliciete "GO" (de `ask`-regels vragen
het toch).

## PHASE 5 — VALIDATE TEMPLATE

Opnieuw, in de projectwortel, met uitvoer:

```bash
node scripts/validate-template.mjs
```

```bash
node scripts/test-guard.mjs
```

Controleer daarnaast: elke verwijzing in `CLAUDE.md` wijst naar een bestaand
bestand; de hooks staan in `.claude/settings.json` en het project is
vertrouwd; `git status` toont geen `.env` en geen tijdelijke bestanden.

## PHASE 6 — REPORT

Eén bericht met:

**a. Wat voor winkel dit volgens de documenten wordt** — vijf zinnen, in je
eigen woorden.

**b. De tien regels die het zwaarst wegen** — die waarvan jij denkt: als ik
deze vergeet, kost het geld of moet het werk over. Per regel één zin waarom.

**c. Alle plaatshouders** die ik moet invullen, met per stuk wat je nodig hebt.
Vergelijk de registry in `LEESMIJ.md` met wat de validator vindt.

**d. De beslissingen die als eerste beantwoord moeten worden**, in de volgorde
van de afhankelijkheden in DECISIONS, met per beslissing wat er niet kan zolang
hij openstaat.

**e. Conflicten en twijfels** uit fase 2, met je voorstel — nog niet toegepast.

**f. Validatie** — per controle: `PASS`, `FAIL` (met uitvoer) of `NOT RUN`
(met reden).

## Daarna

1. Ik lever plaatshouders aan; jij vult ze in één ronde in en meldt wat er
   bleef staan.
2. Bij de huisstijl reken je de contrasttabel in `docs/BRAND.md` echt uit en
   pas je de regels eronder aan op de uitkomst.
3. We beantwoorden D-00 en D-01, daarna wat vrijkomt; elk antwoord met reden in
   DECISIONS.
4. Pas als de leverancier-API gemeten is — `docs/api/LEVERANCIER.md` ingevuld
   met `GEMETEN` en data — begint fase 1 uit `CLAUDE.md`.

Wil ik eerder beginnen, wijs me dan op het risico — bouwen op een niet-gemeten
API is precies waar de helft van deze documenten over gaat. Beslis ik het toch,
dan volg je mij (mijn instructie gaat voor, `CLAUDE.md` § Autoriteit) en leg je
de afwijking met reden vast in DECISIONS.
