---
paths:
  - "src/lib/admin/**"
  - "src/app/<<beheerpad>>/**"
  - "src/app/api/**"
  - "src/middleware.ts"
  - "src/proxy.ts"
---

# Beveiliging — beheerpaneel, webhooks en wat van buiten komt

Laadt bij werk aan het beheerpaneel, aan webhooks en aan alles wat invoer van
buiten accepteert.

## De grondregel

**Alles wat van buiten komt is onbetrouwbaar tot het gekeurd is.** Dat is niet
alleen een formulier: ook de winkelwagen uit localStorage, een querystring, een
webhook, een antwoord van de leverancier, en een bestand dat iemand uploadt.

Keuren doe je aan de rand, met een schema. Daarbinnen is alles getypeerd.

## Beheerpaneel

- **Buiten de taalstructuur** en op een eigen pad. Niet tweetalig; de beheerder
  is één persoon.
- **Op noindex**, en uit de sitemap en robots.
- **Inloggen met een tweede factor.** Mailadres, wachtwoord én de code uit een
  authenticator-app, in **één** formulier — een tweetrapsscherm vraagt om een
  half-ingelogde toestand die je nergens voor nodig hebt en die je wél moet
  bewaken.
- **Wachtwoorden met een trage hash** (`scrypt` of `argon2`). Gebruik wat in de
  standaardbibliotheek zit; een native module valt bij de deploy om.
- Het tweestapsgeheim staat **versleuteld** in de database, met een sleutel uit
  de omgeving. Staat het er onversleuteld, dan is een databaselek genoeg om in
  te loggen.
- **Herstelcodes** bij het aanmaken, één keer te tonen en gehasht opgeslagen.
  Zonder dat is een kwijtgeraakte telefoon het einde van het account.
- **Sessies in de database**, niet alleen in een cookie. Dan kun je ze
  intrekken. Cookie met `httpOnly`, `secure`, `sameSite`.
- Verlopen sessies opruimen in de dagelijkse taak — en controleer dat die
  functie ook écht wordt aangeroepen.
- **De eerste beheerder** komt uit een opzetpagina die zichzelf sluit zodra er
  één is. Daarna gaat het met uitnodigingen.

## Rechten

Zelfs met één beheerder: leg rechten per onderdeel vast, niet één
"is-beheerder". Zodra er een tweede persoon bijkomt wil je kunnen zeggen dat
die wel bestellingen mag zien en geen prijzen mag veranderen.

**Alles wat geld of zichtbaarheid raakt komt in een logboek**: wie, wat,
wanneer, en waarop. Dat is geen controledrang maar het enige dat een vraag als
"wie heeft die prijs veranderd" kan beantwoorden.

## Webhooks

- **Controleer dat het bericht echt van de afzender komt.** Een handtekening
  als de dienst die levert; anders minstens: de status opnieuw opvragen bij de
  bron in plaats van geloven wat er binnenkomt.
- **Geloof nooit het bedrag uit de webhook.** Haal de betaling op en vergelijk
  met wat jij had opgeslagen.
- **Een webhook kan twee keer komen.** De afhandeling moet dat overleven.
- Antwoord snel. Lang werk achteraan de rit, niet in het antwoord.

## Geplande taken

Een adres dat door een cron-taak wordt aangeroepen is voor de buitenwereld
gewoon een URL. Zet er een token in de header op, en vergelijk dat in
constante tijd.

Laat de taak zichzelf claimen in de database, zodat twee aanroepen niet twee
keer hetzelfde werk doen.

## Formulieren en misbruik

- **Een limiet per IP** op alles wat mail verstuurt of een externe call doet:
  contactformulier, retouraanvraag, adres opzoeken.
- **Geen oracle.** Een formulier dat op "bestaat niet" anders reageert dan op
  "bestaat wel maar klopt niet" vertelt een aanvaller welke nummers of
  mailadressen bestaan. Geef één melding voor beide.
- Een link met een token erin is een sleutel: lang genoeg, willekeurig, en
  nooit in een logregel.
- Honeypot-veld werkt beter dan een captcha en kost de klant niets.

## Headers

Zet in elk geval:

| Header | Waarvoor |
|---|---|
| `X-Frame-Options: DENY` | je pagina's horen niet in een iframe van derden |
| `X-Content-Type-Options: nosniff` | de browser mag het bestandstype niet raden |
| `Referrer-Policy` | de volledige URL niet naar buiten sturen |
| `Cross-Origin-Opener-Policy` | vensterisolatie |
| `Permissions-Policy` | camera, microfoon, locatie: uit |

Een Content-Security-Policy is waardevol en een eigen klus: hij vraagt meestal
een nonce per verzoek, en verkeerd ingesteld breekt hij de winkel stil. Doe hem
bewust, niet als bijvangst.

## Secrets

- `.env` niet in Git. `.env.example` wél — dus daar nooit een echte waarde in.
- **Een sleutel die ooit in een commit heeft gestaan is gelekt.** Roteren, niet
  verwijderen.
- Nooit een secret in een logregel, een foutmelding of een URL.
- Een testsleutel op de ontwikkelmachine. Staat er een echte, dan verplaatst
  elke druk op een knop echt geld — en dan is "even testen" een transactie.

## Persoonsgegevens

- Niet in een URL en niet in een logregel. Een querystring staat in de
  geschiedenis van de browser, in serverlogs en in de verwijzer naar derden.
- Alleen opslaan wat je nodig hebt, en met een termijn erbij.
- Zie `docs/PRIVACY.md` — nieuwe kolom met een persoonsgegeven betekent: daar
  een regel bij.

## Afhankelijkheden

- `pnpm audit` hoort bij "af" zodra je `package.json` aanraakt.
- Geen library erbij zonder te vragen. Elke afhankelijkheid is iets dat straks
  een melding kan geven en dat iemand moet bijwerken.
- Zit een kwetsbaarheid diep in de boom en is er geen opwaardering, dan een
  override binnen hetzelfde hoofdnummer — met een comment erbij waarom, en de
  afspraak dat hij weggaat zodra het tussenliggende pakket bij is.
