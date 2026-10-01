# Lijst voor livegang — <<SHOP>>

Niet afvinken wat je niet zelf gecontroleerd hebt.

## Juridisch — dit moet er staan vóór de eerste bestelling

- [ ] **Bedrijfsgegevens** op de site: handelsnaam, adres, mailadres, KvK- en
      btw-nummer. Een webwinkel zonder deze gegevens is in overtreding, en het
      is ook het eerste waar een klant naar zoekt als hij twijfelt.
- [ ] **Algemene voorwaarden**, bereikbaar vóór het afrekenen.
- [ ] **Privacyverklaring** die klopt met `docs/PRIVACY.md`, met een datum.
- [ ] **Herroepingsrecht**: 14 dagen, duidelijk benoemd, met het modelformulier
      beschikbaar.
- [ ] **Prijzen inclusief btw** voor consumenten, en verzendkosten zichtbaar
      vóór de laatste stap.
- [ ] De bestelknop zegt wat er gebeurt ("Bestellen en betalen"), niet
      "Versturen".
- [ ] Bevestiging per mail met: wat er besteld is, het bedrag, de btw, het
      herroepingsrecht en de bedrijfsgegevens.
- [ ] Is er een "van/voor"-prijs? Dan moet de referentie de laagste prijs van
      de afgelopen 30 dagen zijn, en die moet je dus al meten.
- [ ] Beoordelingen? Dan geen selectie op gunstigheid, en alleen van mensen die
      echt gekocht hebben.

## Prijzen en kortingen

- [ ] Een actie die de marge raakt wordt getrimd, en het paneel zegt dat
- [ ] Twee acties op één artikel: de hoogste wint
- [ ] Geen doorgestreepte prijs zonder 30 dagen prijsgeschiedenis
- [ ] Een code werkt niet op artikelen die al in de actie zijn
- [ ] Een code maakt een bestelling nooit duurder (gratis-verzendgrens)
- [ ] Pagina, winkelwagen en afrekenscherm tonen hetzelfde bedrag

## Betalen

- [ ] Testbetaling gedaan met een echte methode, in de live omgeving.
- [ ] Webhook komt aan en wordt twee keer verwerkt zonder dubbele mail.
- [ ] Een afgebroken betaling laat geen half afgeronde bestelling achter.
- [ ] **Een terugbetaling één keer echt uitgevoerd.** Dit is de stap die
      iedereen overslaat omdat hij geld kost; doe hem met een klein bedrag.
      Alles eromheen testen bewijst niet dat de keten werkt.
- [ ] Het bedrag dat de betaaldienst ziet komt van de server.

## Mail

- [ ] SPF, DKIM en DMARC ingesteld voor het domein.
- [ ] Een echte mail ontvangen in Gmail én in Outlook, niet in de map
      ongewenst.
- [ ] Afzender is een adres op het eigen domein.
- [ ] De beheerder krijgt een bericht bij elke bestelling, met alles wat hij
      nodig heeft om in te kopen.

## Factuur

- [ ] Alle wettelijke velden staan erop (zie `docs/FACTUUR.md`)
- [ ] Het nummer volgt op het vorige, zonder gat
- [ ] Een naam met accenten en een bedrag met een euroteken komen goed door
- [ ] De btw op de factuur is gelijk aan die in de bevestigingsmail
- [ ] De link naar de PDF is niet te raden

## Beveiliging

- [ ] `pnpm audit` zonder meldingen.
- [ ] Geen sleutel in Git, ook niet in de geschiedenis. Ooit toch gestaan?
      Roteren.
- [ ] Beheerpaneel op noindex en uit de sitemap en robots.
- [ ] Inloggen met tweede factor.
- [ ] Beveiligingsheaders ingesteld (clickjacking, mimetype-raden,
      verwijzerbeleid).
- [ ] Een limiet op formulieren die mail of externe calls veroorzaken.
- [ ] Database niet open voor het hele internet.

## Techniek

- [ ] `pnpm build` slaagt op de machine waar hij straks draait.
- [ ] 404 geeft echt status 404, geen 200 met een foutpagina.
- [ ] sitemap.xml en robots.txt kloppen.
- [ ] Taalvarianten verwijzen naar elkaar.
- [ ] Werkt zonder JavaScript voor het belangrijkste pad? Minstens: zoeken en
      bladeren.
- [ ] Getest op een echte telefoon, niet alleen in een versmald venster.
- [ ] Donkere modus doorlopen.
- [ ] Toetsenbord: van het begin tot het afrekenen, zonder muis.
- [ ] Een trage verbinding nagebootst en gekeken wat er dan op het scherm staat.

## Beheerpaneel

- [ ] Een bestelling van begin tot eind afgehandeld: bekijken, inkopen
      afvinken, factuur openen.
- [ ] Een retour doorlopen tot en met de terugbetaling.
- [ ] Een mislukt formulier houdt zijn waarden.
- [ ] Werkt op een telefoon voor de drie dingen die haast hebben: bestelling
      bekijken, als ingekocht markeren, retour afhandelen.
- [ ] Het logboek toont wie wat deed.

## Na de eerste echte bestelling

- [ ] Kwam de bevestiging aan bij de klant?
- [ ] Kwam het bericht aan bij de beheerder?
- [ ] Klopt het factuurnummer, en volgt het op het vorige?
- [ ] Klopt het bedrag met wat de betaaldienst meldt?
- [ ] Is de inkoop bij de leverancier gelukt tegen de prijs waarmee gerekend is?

Die laatste is de belangrijkste. Zit er verschil tussen de prijs waarop je
verkocht hebt en de prijs waarvoor je kunt inkopen, dan zit er iets fout in de
koppeling tussen prijs en aanbieding — en dat kost bij elke bestelling geld
tot het gevonden is.
