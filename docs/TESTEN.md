# Hoe je weet dat het werkt

Er is (nog) geen testrunner. Dat is een keuze die je bewust moet maken en niet
moet laten gebeuren: een winkel met een handvol schermen en veel externe
koppelingen heeft meer aan controlescripts en echte doorlopen dan aan honderd
tests over code die je toch wel ziet werken.

Wat dan wél, per soort risico.

---

## 1. Rekenwerk met geld — hier horen echte tests

Dit is het enige deel waar een testrunner zich meteen terugverdient. Prijs
berekenen, korting toepassen, btw eruit halen, verzendkosten en drempels: dat
zijn pure functies zonder database en zonder netwerk, en de fouten zijn
onzichtbaar tot een klant ze ziet.

Test in elk geval de randen, want daar zitten ze:

- een bedrag van één cent
- een korting die precies de ondergrens raakt
- een korting die eroverheen gaat (moet geweigerd worden, niet afgekapt)
- een bestelling precies op de drempel voor gratis verzending
- afronding: een percentage dat op een oneven bedrag valt
- een ontbrekende adviesprijs van de leverancier

**Reken één echt geval met de hand na** en zet dat met bedragen in het
commentaar. Dat is meer waard dan tien gegenereerde gevallen.

## 2. Externe koppelingen — controlescripts

Voor elk systeem buiten de winkel een script dat los van de site draait:

| Script | Controleert |
|---|---|
| database | verbinding én of het schema klopt met de migraties |
| mail | of er echt een bericht aankomt |
| betaaldienst | welke sleutel actief is en welke methodes aanstaan |
| leverancier | of het token werkt en wat een artikel teruggeeft |

**Waarom los van de site:** als er iets niet werkt wil je binnen een minuut
weten of het aan de winkel ligt of aan de koppeling. Een winkel die op mockdata
draait ziet er compleet uit, en een beheerpaneel zonder database ook.

Draai het databasescript na élke deploy en na élke migratie.

## 3. Schermen — in de browser, niet in je hoofd

Elk scherm dat je af noemt is minstens één keer echt geopend:

- [ ] op telefoonbreedte **en** op een echt toestel
- [ ] in donkere modus
- [ ] met het toetsenbord, van boven naar beneden, met zichtbare focus
- [ ] met een trage verbinding nagebootst — wat staat er dan?
- [ ] met de vier toestanden: laden, leeg, fout, gevuld

Bekijk ook de console. Een waarschuwing die je wegklikt is er morgen nog.

## 4. Dingen die alleen fout gaan bij twee tegelijk

Een controle in code tussen lezen en schrijven houdt niets tegen. Of je fix
werkt is **meetbaar**, en dat is de moeite waard bij alles wat geld of een
nummer uitdeelt.

**Hoe je het bewijst:** open twee verbindingen met de database naast elkaar,
laat allebei dezelfde handeling beginnen, en kijk wat er gebeurt. Bij een
werkende vergrendeling wacht de tweede tot de eerste klaar is en schrijft dan
niets. Zet die meting in de documentatie, met de datum.

Doe dit minstens voor: factuurnummers, "één keer per klant", en alles wat een
terugbetaling kan verdubbelen.

## 5. De keten die je niet durft te testen

Elke winkel heeft handelingen die echt geld verplaatsen: een betaling, een
terugbetaling, een inkooporder bij de leverancier. Die worden daarom nooit
getest — en dat betekent dat ze op de dag dat het moet voor het eerst draaien.

**Doe ze één keer echt, met een klein bedrag, vóór de livegang.** Alles
eromheen testen bewijst niet dat de keten werkt. Zet in de documentatie wanneer
het gedaan is; zolang dat er niet staat, is het niet gebeurd.

## 6. Teksten

- Alle talen hebben dezelfde sleutels. Controleer dat met een script, niet met
  het oog — een ontbrekende sleutel hoort de bouw te laten falen.
- Lees de teksten één keer hardop. Knoppen die zeggen wat ze doen, fouten die
  zeggen wat de klant kan doen.

## 7. Prestaties

Meet op een nagebootste trage verbinding, niet op je eigen glasvezel. Let op
het grootste beeld boven de vouw en op wat er verspringt tijdens het laden.

Tel één keer hoeveel verzoeken een paginaweergave bij de leverancier kost. Die
meting vindt altijd iets.

---

## Wat "af" betekent

- [ ] typecheck en lint groen
- [ ] de bouw slaagt op de machine waar hij straks draait
- [ ] `pnpm audit` zonder meldingen
- [ ] in de browser bekeken, met de lijst uit punt 3
- [ ] nieuwe beslissing in `DECISIONS.md`, mét reden
- [ ] nieuw persoonsgegeven of nieuwe browseropslag in `PRIVACY.md`
- [ ] een meting die je deed staat in het commentaar, met de datum

**"Het werkt bij mij" is geen bevinding.** Zeg wat je hebt gedaan, waar, en wat
je zag. En wat je niet hebt kunnen controleren hoort er net zo goed bij.
