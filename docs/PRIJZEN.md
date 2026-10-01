# Prijzen, acties en kortingscodes

Drie dingen verlagen een prijs, en ze kennen elkaar niet vanzelf. Dit bestand
legt de volgorde vast en de grenzen waar ze elkaar raken. Zonder die volgorde
krijg je een winkel die soms met verlies verkoopt en die niet kan uitleggen hoe
een bedrag tot stand kwam.

Het rekenwerk zelf staat in `.claude/rules/geld.md`. Dit gaat over de regels.

---

## De prijsopbouw, in volgorde

```
  inkoopprijs van de leverancier        (excl. btw)
+ prijsregel: opslag van de eigenaar     ← of: adviesprijs van de leverancier
+ btw
= de prijs die op de pagina staat        ← dit is de "van"-prijs bij een actie
− kortingsactie                          ← begrensd door de ondergrens
= de prijs die de klant ziet
− kortingscode                           ← over het totaal, niet per artikel
= wat er betaald wordt
```

**Elke stap heeft één bron en één moment.** Een korting die ergens anders wordt
toegepast dan hier, rekent bij het afrekenen een ander bedrag uit dan de pagina
toonde — en dat is de fout waar klanten over bellen.

---

## 1. Prijsregels (de opslag)

De eigenaar bepaalt zijn marge, per groep. Drie dingen liggen vast:

- **De sleutel waar een prijsregel aan hangt moet overal gelijk zijn.** Hangt
  hij aan de categorie, en komt een artikel via het zoekveld binnen zonder
  categorie, dan rekent de kassa iets anders uit dan de pagina toonde. Kies een
  sleutel die op het artikel zelf zit.
- **Geen prijsregel betekent: de adviesprijs van de leverancier**, zoals die
  binnenkomt. Geen opslag eroverheen, geen ondergrens eroverheen.
- **Geen van beide** betekent: inkoop plus een minimummarge uit de instellingen.
  Dat is een noodgreep, geen strategie.

Eén grens geldt altijd: **nooit onder de inkoopprijs verkopen**, ook niet als
er 0 of onzin is ingevuld.

---

## 2. Kortingsacties

Een actie wijst een **groep** aan met een percentage en een looptijd. Niet een
lijst artikelen: "15% op categorie X" is één rij, geen tweehonderd.

### De ondergrens gaat vóór het percentage

Dit is de belangrijkste regel van dit document.

> **Een actie van 20% op een artikel met 10% opslag levert geen 20% korting op.
> Het wordt 9%.**

Want de korting mag de marge opeten en niet meer dan dat. Met een opslag van
10% is de ondergrens de inkoopprijs, en daar zit maar 9,1% tussen:

```
inkoop                 € 10,00 excl. btw
+ 10% opslag           € 11,00 excl. btw
+ 21% btw              € 13,31   ← staat op de pagina
ondergrens (inkoop + btw)  € 12,10
gevraagde korting 20%  → € 10,65  ligt onder de grens
toegepast              → € 12,10  = 9% korting
```

De maximale korting die een opslag toelaat is `opslag / (100 + opslag)`:

| Opslag | Maximale korting |
|---|---|
| 10% | 9,1% |
| 15% | 13,0% |
| 20% | 16,7% |
| 25% | 20,0% |
| 40% | 28,6% |
| 100% | 50,0% |

**Zonder prijsregel** is de ondergrens de minimummarge uit de instellingen, en
dan hangt de ruimte af van hoe hoog de adviesprijs van de leverancier boven de
inkoopprijs ligt. Die ruimte is meestal groter, en per artikel anders.

### Wat daaruit volgt voor de code en het scherm

- **Reken de korting uit waar de inkoopprijs nog in beeld is** — in de laag die
  de prijs opbouwt, niet ergens achteraf. Buiten die laag is de inkoopprijs weg
  en kun je de grens niet meer bewaken.
- **Geef terug wat er werkelijk is toegepast**, niet wat er gevraagd was.
- **Het beheerpaneel zegt het als de grens ingreep.** Anders staat er 20% in
  het paneel terwijl de klant 9% ziet, en denkt de eigenaar dat er iets stuk is.
- **De vlag op de kaart toont het toegepaste percentage**, naar beneden
  afgerond. Liever 9% tonen dan 10% beloven die de klant niet terugziet.
- Bij goedkope artikelen wiebelt dat percentage door afronding in hele centen:
  twee procent van € 1,11 is twee cent, en dat is 1,8% → er staat 1%. Dat is
  geen fout; het alternatief is een percentage tonen dat niet klopt met het
  bedrag.

### Twee acties op hetzelfde artikel

**De hoogste wint. Niet stapelen.** Twee kortingen over elkaar is voor niemand
na te rekenen, en de ondergrens zou het verschil toch opeten. Leg vast en toon
welke regel gewonnen heeft.

### Stoppen is niet weggooien

Een afgelopen actie blijft staan met een einddatum. Anders is over een half
jaar niet meer te verklaren waarom een oude bestelling die prijs had.

### De "van"-prijs

Een doorgestreepte prijs mag alleen met de **laagste prijs van de afgelopen 30
dagen** als referentie (EU-prijsaanduidingsrichtlijn). Dat betekent:

- Je moet prijzen **meten en bewaren voordat een actie begint**. Een
  geschiedenis die je vandaag niet opbouwt heb je over dertig dagen nog steeds
  niet.
- Meet ook artikelen van acties die **binnenkort beginnen**, niet alleen de
  lopende.
- Is er geen geschiedenis, dan staat er alleen het percentage en geen
  doorgestreepte prijs. Dat is geen tussenoplossing maar de wet.
- Zet erbij wát dat bedrag is ("laagste prijs van de afgelopen 30 dagen"). Een
  doorgestreepte prijs zonder uitleg is precies wat de toezichthouder aanrekent.

---

## 3. Kortingscodes

Een code is iets anders dan een actie: hij geldt over de **bestelling**, niet
over een artikel.

| Eigenschap | Opmerking |
|---|---|
| Percentage | |
| Minimumbedrag | kijkt naar de artikelen, **niet** naar de verzendkosten |
| Looptijd | |
| Maximum aantal keer | optioneel |
| Eén keer per klant | hangt aan een unieke sleutel in de database |

### Drie regels die geld raken

1. **Een code geldt niet op artikelen die al in de actie zijn.** Anders stapelt
   hij alsnog bovenop een korting die de marge al heeft opgegeten. Reken de
   basis voor de code dus uit over de regels zónder eigen actie.
2. **"Eén keer per klant" hangt aan een unieke sleutel**, niet aan een controle
   in code. Tussen lezen en schrijven past een tweede tabblad.
3. **Afgetekend wordt er pas als er betaald is.** Een afgebroken bestelling mag
   de code niet verbruiken.

### De code en de gratis-verzendgrens

De drempel kijkt naar het bedrag **ná** de korting — met één uitzondering:

> **Een korting mag een bestelling nooit duurder maken.**

Een bestelling van € 100,00 met gratis verzending wordt met 5% korting € 95,00,
en dan vallen de verzendkosten er weer bij: € 95,00 + € 7,45 = € 102,45. De
klant betaalt méér door een korting te gebruiken. Vang dat af: zakt het totaal
door de korting onder de drempel terwijl het er zonder korting boven lag, dan
blijft de verzending gratis.

### Opnieuw keuren bij het afrekenen

De code wordt bij het afrekenen opnieuw gekeurd, met prijzen die de server zelf
heeft opgehaald. Wat de browser meestuurt is alleen de tekst van de code.

Is hij niet meer geldig, dan gaat de betaling **niet** door: de klant zag een
bedrag met korting en mag niet zonder waarschuwing het volle bedrag afrekenen.

---

## Rekenen: één functie, twee gebruikers

Het besteloverzicht in de browser en de server moeten hetzelfde bedrag
uitrekenen. Zet dat rekenwerk dus in een functie **zonder database eromheen**,
zodat beide kanten hem kunnen importeren zonder dat de browserbundel een
databasestuurprogramma binnentrekt.

---

## Controlelijst

- [ ] Een actie die de ondergrens raakt wordt getrimd, niet geweigerd — maar
      het paneel zegt het
- [ ] Een gevraagd percentage buiten bereik wordt wél geweigerd
- [ ] Twee acties op één artikel: de hoogste wint, en dat is zichtbaar
- [ ] Geen doorgestreepte prijs zonder 30 dagen geschiedenis
- [ ] Een code werkt niet op artikelen die al in de actie zijn
- [ ] Een code maakt een bestelling nooit duurder
- [ ] De code wordt bij het afrekenen opnieuw gekeurd
- [ ] Pagina, winkelwagen en afrekenscherm tonen hetzelfde bedrag
