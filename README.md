# Parketvloer App — Parket Specialist Gouda

Interne web-app van Parket Specialist Gouda voor het opmeten van parketvloeren bij de klant, het plannen van het werk en het bijhouden van klanten, taken, offertes en Parketmeester-schades. De app is gemaakt voor gebruik op de telefoon of tablet op locatie, maar werkt ook op de computer.

De hele app staat in **één HTML-bestand** (`parketvloer_app_XXX.html`). Gegevens, inloggen en bestanden lopen via **Supabase**.

> Deze README beschrijft versie **135**.

---

## Inhoud

1. [Functies](#functies)
2. [Techniek](#techniek)
3. [Installeren en in gebruik nemen](#installeren-en-in-gebruik-nemen)
4. [Database (Supabase)](#database-supabase)
5. [Parketmeester en CUF-import](#parketmeester-en-cuf-import)
6. [E-mails automatisch bij de klant (mailsync)](#e-mails-automatisch-bij-de-klant-mailsync)
7. [Wijzigingen doorvoeren](#wijzigingen-doorvoeren)
8. [Beveiliging](#beveiliging)
9. [Bekende beperkingen](#bekende-beperkingen)

---

## Functies

### Beginscherm
- **Vandaag**: de datum, de afspraken en agenda-items van vandaag, en de taken die vandaag (of eerder) moeten gebeuren.
- Tegels: **Nieuwe opmeting**, **Klanten**, **Agenda**, **Taken** en **Overzicht**.
- **Tandwiel** rechts in de zwarte balk: gebruikersinstellingen (wie ben je, profiel, thema licht/donker, **schermbreedte**, meldingen).
- **Schermbreedte** (per apparaat, onthouden in de browser): *Automatisch* (op schermen vanaf 1100 px worden agenda, klantenlijst en overzicht beeldvullend; formulieren blijven 860 px), *Computer (breed)* (alles tot 1600 px breed) of *Telefoon (smal)* (480 px, zoals op een telefoon).
- **⟳** in de balk: pagina verversen. Het bolletje ernaast laat zien of er automatisch is opgeslagen (✓), of het opslaan bezig is (…) of is mislukt (!).

### Overzicht
Alles wat aandacht nodig heeft op één scherm:
- **Vandaag**: agenda en taken van vandaag.
- **Agenda**: de komende 7 dagen, per dag gegroepeerd.
- **Taken**: alle openstaande taken, direct af te vinken.
- **Checklist niet compleet vóór legdatum**: klanten met een legdatum binnen 7 dagen (of al voorbij) waarvan de checklist nog niet af is.
- **Offerte nog te versturen**: met een X om een klant als "niet doorgegaan" weg te halen.
- **Openstaand bij Parketmeester**: dossiers die nog niet op "Afgerond" staan.

### Klanten
- Zoeken op naam, adres, plaats, dossiernummer of type opdracht.
- **Filteren op type opdracht**: Nieuwe vloer, Renovatie, Aanheling, Schade, Schoonmaken en oliën, Parketmeester of "Geen type ingevuld".
- Sorteren op naam of datum, route openen in Google Maps, klant verwijderen.
- Blokken met laatste wijzigingen, openstaande offertes en checklist-waarschuwingen.

### Opmeting (tabbladen bij een klant)
| Tabblad | Inhoud |
|---|---|
| **Klantgegevens** | Bezoekdatum en -tijd, bezoeker, naam/adres/contact, **contactpersonen** (partner, huurder, aannemer, … met naam, rol, telefoon en e-mail; bellen/mailen met één tik), type woning, lift/trappen, ruimtes, **type opdracht**, notities, en één regel **✉ e-mails** (aantal + laatste mail) die doorlinkt naar een aparte pagina met alle e-mails van de klant (automatisch opgehaald uit de mailbox, zie [mailsync](#e-mails-automatisch-bij-de-klant-mailsync)) |
| **Type vloer** | Type, houtsoort, sortering, leverancier, legwijze, patroon, afmetingen, bewerkingen, afwerking |
| **Controle** | Ondergrond, vochtmetingen, vloerverwarming, scheuren, krastest, vlakheid, enz. |
| **Vloer** | Ruimtes per verdieping (L × B), aftrek, afvalpercentage, netto en totaal inclusief afval |
| **Extra materiaal** | Alleen zichtbaar bij Renovatie/Aanheling/Schade als bij "Hout nodig?" **Ja** is gekozen. Zelfde opbouw als Vloer, plus een omschrijving van het materiaal |
| **Plinten / Afdeklatten / Profielen** | Lengtes per ruimte, specificaties, afvalpercentage |
| **Foto's** | Meerdere foto's tegelijk uploaden (worden automatisch verkleind), met omschrijving en ruimte |
| **Samenvatting** | Alles op één pagina, ook te exporteren als PDF (afdrukken) |
| **Checklist** | Bestellingen (vloer, plinten, ondervloer, lijm, afdeklatten, olie/lak) met leverancier en datum, **planning legger** (naam, legdatum, aantal dagen), bezorgschema, offerte-PDF |
| **Offerte** | Diensten kiezen en berekenen; concept-offerte aanmaken in **Moneybird** |
| **Tarieven** | Diensten en tarieven beheren (gedeeld voor alle klanten) |
| **Parketmeester** | Alleen zichtbaar bij type opdracht Parketmeester: dossiernummer, status en samenvatting van het CUF-bestand |

Alles wordt **automatisch opgeslagen**, ongeveer 2,5 seconde na een wijziging. Opslaan begint pas als er een naam of adres is ingevuld.

### Agenda
Dag-, week- en maandweergave. De weekweergave kan **naast elkaar** (rooster met tijden, dagen als kolommen, zoals Outlook; op de telefoon zijwaarts scrollen) of **onder elkaar** (lijst per dag). De keuze wordt per apparaat onthouden. In de agenda staan:
- bezoeken (kleur per bezoeker: Raoul blauw, Mireille rood);
- verwachte leveringen uit de checklist;
- **vloer leggen**: een klus van meerdere dagen staat op elke werkdag als "dag 1/3", "dag 2/3", enz. Zaterdag en zondag worden overgeslagen;
- bezorgingen;
- losse afspraken (met **wie gaat** — Raoul blauw, Mireille rood, anders groen — en adres, telefoon en e-mailadres; bij het kiezen van een klant worden deze automatisch ingevuld, en telefoon/e-mail zijn aantikbaar).

### Taken
Eigen takenlijst met optionele klant en datum. Taken die te laat zijn, worden rood gemarkeerd.

---

## Techniek

- **Frontend**: één los HTML-bestand met HTML, CSS en JavaScript erin. Er is geen build-stap en er zijn geen externe libraries nodig.
- **Backend**: [Supabase](https://supabase.com)
  - **Auth**: inloggen met e-mail en wachtwoord. De sessie wordt automatisch ververst.
  - **Database** (PostgREST): tabellen `klanten`, `afspraken`, `taken`, `gebruikers`, `tarief_items`, `tarief_instellingen` en `klant_emails`.
  - **Storage**: bucket `Documenten` voor foto's (`fotos/<klant-id>/…`), offerte-PDF's en profielfoto's.
  - **Edge Function**: `moneybird-offerte` maakt een concept-offerte aan in Moneybird op basis van een klant-id.
- **Mailsync** (PHP op de Antagonist-hosting, naast de mailer): haalt elke 15 minuten de mails van en aan klanten op en zet ze in `klant_emails`.
- De Supabase-URL en de publieke (publishable) sleutel staan bovenaan het script als `SB_URL` en `SB_KEY`.
- In de browser (`localStorage`) worden alleen het gekozen thema (`pvThema`) en de gekozen gebruiker (`pvGebruiker`) bewaard. Alle andere gegevens staan in Supabase.

---

## Installeren en in gebruik nemen

1. Zet het HTML-bestand op een webserver, bijvoorbeeld bij de hosting van de website, op GitHub Pages of Netlify. Het moet via **https** bereikbaar zijn.
2. Open de link op de telefoon en log in met je Supabase-account.
3. Tip: kies in de browser **"Zet op beginscherm"** (iPhone: deelknop → Zet op beginscherm). De app opent dan als een gewone app.
4. Nieuwe gebruikers maak je aan in het Supabase-dashboard (Authentication → Users). Een wachtwoord resetten gaat ook daar.

---

## Database (Supabase)

### Kolommen die later zijn toegevoegd
Sommige functies hebben kolommen nodig die er bij een oudere database nog niet zijn. Voer dit **één keer** uit in de **SQL Editor** van Supabase. Het is veilig om dit vaker te doen, omdat bestaande kolommen worden overgeslagen.

```sql
alter table klanten
  add column if not exists checklist_legger_dagen text,   -- aantal dagen legger
  add column if not exists schade_dossiernummer  text,    -- Parketmeester dossiernummer
  add column if not exists schade_status         text,    -- Parketmeester status
  add column if not exists schade_cuf            text,    -- ingelezen CUF-begroting (JSON)
  add column if not exists hout_nodig            text,    -- 'ja' / 'nee'
  add column if not exists extra_materiaal       text,    -- opmeting extra materiaal (JSON)
  add column if not exists emails                text,    -- (oud) geplakte e-mails; overgezet naar klant_emails
  add column if not exists contactpersonen       text;    -- extra contactpersonen per klant (JSON)

alter table afspraken
  add column if not exists email                 text,    -- e-mailadres bij handmatige afspraak
  add column if not exists bezoeker              text;    -- wie gaat (bepaalt de kleur)
```

**Vangnet:** ontbreekt een van deze kolommen nog, dan blijft de app gewoon werken. Je krijgt dan één keer een melding dat dat onderdeel nog niet wordt bewaard.

### Opslag van lijsten
Opmetingen, foto's, vochtmetingen, gekozen diensten, de CUF-begroting, het extra materiaal en de contactpersonen staan als **JSON-tekst** in één kolom per onderdeel, bijvoorbeeld `vloer_opmeting`, `fotos` en `extra_materiaal`.

---

## Parketmeester en CUF-import

Schades van Parketmeester komen binnen via het portaal **pop.mkb-logic.nl**. Daar kun je een schade exporteren als **CUF** (Calculatie Uitwissel Formaat, CUF-XML versie 4.003).

**Werkwijze:**
1. Exporteer de schade in het portaal als CUF.
2. Kies in de app bij **Klantgegevens → Type opdracht** de optie **Parketmeester**.
3. Klik op **⇪ CUF-bestand uploaden** en kies het bestand.
4. In het tabblad **Parketmeester** zie je dan het dossiernummer, de status en de samenvatting van de begroting: arbeid, materiaal & overig, totaal excl. en incl. btw, extra uren, en beide tabellen met subtotalen.
5. Vul naam, adres en telefoon zelf in. Die staan niet in het CUF-bestand.

**Wat de app uit het CUF-bestand leest:**
- `PROJECTGEGEVENS/@PROJECTNUMMER` wordt het dossiernummer;
- `PROJECTGEGEVENS/@AANMAAKDATUM` wordt de datum van de begroting;
- elke `BEGROTINGSREGEL` levert omschrijving, hoeveelheid, eenheid, prijs en btw;
- `STAARTGEGEVENS/@AANNEEMSOM` wordt het totaal excl. btw.

Regels die beginnen met "Arbeid" of "Uurtarief" tellen als **arbeid**, de rest als **materiaal & overig**.

**Statussen:** Ontvangen → Afspraak gepland → Hersteld → Teruggemeld in portaal → Afgerond.

Parketmeester-klanten verschijnen niet bij "Offerte nog te versturen", omdat de offerte via het portaal loopt.

---

## E-mails automatisch bij de klant (mailsync)

Mails van en aan een klant komen vanzelf in de app te staan. Bij Klantgegevens staat een regel met het aantal e-mails en de laatste mail; **Bekijk →** opent de pagina met alle e-mails.

**Hoe het werkt**
- `mailsync.php` staat op de Antagonist-hosting (`public_html/mailer/mailsync.php`) en wordt elke 15 minuten gestart door een cronjob.
- Het script leest **alleen-lezen** de **Inbox** en de **Verzonden**-map via IMAP. Mails blijven ongelezen en worden niet verplaatst.
- Elke mail van of aan een e-mailadres dat bij een klant in de app staat (het hoofdadres **of** het adres van een contactpersoon), komt in de tabel `klant_emails`. Mails van niet-klanten worden overgeslagen. Bijlagen worden niet opgeslagen.
- Bij antwoorden wordt het geciteerde vorige bericht weggelaten, zodat elke mail alleen zijn eigen tekst toont.
- Dubbele mails worden voorkomen via de Message-ID (uniek per klant).
- De eerste keer worden de mails van de afgelopen 90 dagen opgehaald. Daarna alleen nieuwe.

**Bestanden**
| Bestand | Plaats |
|---|---|
| `mailsync.php` | `domains/parketspecialistgouda.nl/public_html/mailer/` |
| `config.php` (uit `config.voorbeeld.php`) | `domains/parketspecialistgouda.nl/mailsync/` — **buiten** public_html |
| `state.json`, `mailsync.log` | worden automatisch aangemaakt in dezelfde map als `config.php` |
| `klant_emails.sql` | één keer uitvoeren in de Supabase SQL Editor |

**Testen:** open `https://parketspecialistgouda.nl/mailer/mailsync.php?token=<web_token>&test=1`. Je ziet dan of inloggen op de mailbox lukt, welke mappen worden gelezen en of Supabase en de tabel bereikbaar zijn. Er wordt dan niets opgeslagen.

**Cronjob (elke 15 minuten)** via DirectAdmin → *Geavanceerde functies* → *Cronjobs* → *Cronjob aanmaken*: Minuut `*/15`, de overige velden `*`, en als commando (met je eigen DirectAdmin-gebruikersnaam op de plaats van `debXXXXX`):
```
/usr/local/bin/php -q -f /home/debXXXXX/domains/parketspecialistgouda.nl/public_html/mailer/mailsync.php > /dev/null 2>&1
```

**PHP-extensie imap:** het script heeft de PHP-extensie `imap` nodig. Meldt de test dat die ontbreekt, zet hem dan aan in de PHP-instellingen van je hosting. Vanaf PHP 8.4 zit imap niet meer standaard in PHP. Kies dan eventueel PHP 8.3 voor dit domein, of vraag Antagonist-support om hulp.

**Beveiliging**
- `config.php` bevat het mailboxwachtwoord en de **service_role-sleutel** van Supabase. Die mag nooit in `public_html` of in de app staan.
- Zonder het juiste token geeft `mailsync.php` via het web een 404.
- Gebruikers in de app kunnen mails alleen **lezen** en **weghalen**. Toevoegen gebeurt alleen door het script.

**Goed om te weten**
- Krijgt een klant (of contactpersoon) pas later een e-mailadres, dan worden alleen de mails **vanaf dat moment** gekoppeld, niet eerdere.
- Werkt het niet? Kijk in `mailsync.log`, of draai de test-URL hierboven.
- Wil je alles opnieuw laten inlezen, verwijder dan `state.json`. Dubbele mails worden overgeslagen.

---

## Wijzigingen doorvoeren

- **Let op:** de regels `apple-touch-icon` en `apple-mobile-web-app-title` bovenin `<head>` zorgen voor het icoon en de naam "Opmeten" op het beginscherm. Pas je `index.html` op GitHub zelf aan, geef die wijziging dan ook door, zodat een volgende versie hem niet overschrijft.
- **Versienummer:** staat bovenin het script als `const APP_VERSIE = '135'` (en `APP_VERSIE_DATUM`). Het is te zien in de zwarte balk (bijv. *v134*), op het inlogscherm en onderaan het beginscherm. Verhoog het bij elke nieuwe versie. De app kijkt elke 30 minuten (en bij terugkeren naar de app) of er op de server een hoger nummer staat en toont dan een balk *"Versie … is beschikbaar"* met een knop **Verversen**.
- Elke nieuwe versie krijgt een hoger nummer: `parketvloer_app_125.html` → `parketvloer_app_126.html`. Zo kun je altijd terug naar een vorige versie.
- Nieuwe functies zijn zo veel mogelijk **los toegevoegd**. Bestaande functies (`bouwOpslaanData`, `laadKlant`, `resetFormulier`, `switchTab`, `sbFetch`, enz.) worden "ingepakt" in plaats van herschreven, zodat de oude werking blijft bestaan.
- Een nieuwe functie die een databasekolom nodig heeft, krijgt:
  1. een regel in het SQL-blok hierboven;
  2. een vangnet in `sbFetch`, zodat de app blijft werken als de kolom nog ontbreekt.
- Na het vervangen van het bestand op de server: druk in de app op **⟳** of ververs de pagina.

---

## Beveiliging

De sleutel in het bestand (`SB_KEY`) is de **publieke** sleutel van Supabase. Het is normaal dat die in het bestand staat. De echte beveiliging zit in het inloggen en in **Row Level Security (RLS)**.

### Huidige instellingen (gecontroleerd op 24 oktober 2026)
| Onderdeel | Instelling |
|---|---|
| Zelf een account aanmaken | **Uit** (Authentication → Sign In / Providers → "Allow new users to sign up") |
| `klanten`, `afspraken`, `taken`, `gebruikers` | RLS aan, alleen voor ingelogde gebruikers |
| `tarief_items`, `tarief_instellingen` | RLS aan, één regel per tabel: alles toegestaan voor `authenticated` |
| `klant_emails` | RLS aan: lezen en verwijderen voor `authenticated`. Toevoegen alleen via het mailsync-script (service_role) |
| Storage-bucket `Documenten` | Lezen, uploaden en bijwerken alleen voor `authenticated`. Verwijderen kan niet via de app |

Een test vanaf buiten (zonder inlog, met alleen de publieke sleutel) gaf **niets** terug uit de tabellen en de bestandslijst.

### Afspraken
- **Nieuwe gebruiker:** maak het account zelf aan via Authentication → Users → *Add user*. Zet het zelf aanmelden niet weer aan.
- **Nieuwe tabel:** zet direct RLS aan en geef alleen `authenticated` toegang, nooit `anon` of `public`:
  ```sql
  alter table public.<tabel> enable row level security;
  create policy "<tabel> ingelogd" on public.<tabel>
    for all to authenticated using (true) with check (true);
  ```
- **Snel controleren** of er nog ergens te ruime regels zijn:
  ```sql
  select schemaname, tablename, policyname, roles, cmd
  from pg_policies
  where 'anon' = any(roles) or 'public' = any(roles);
  ```
  Dit hoort **geen rijen** op te leveren.
- Foto's en PDF's zijn bereikbaar via **openbare links**, zodat ze in de app en de PDF-samenvatting te zien zijn. Wie een link heeft, kan het bestand openen, maar een lijst met bestanden opvragen kan niet meer. Deel die links dus niet onnodig.
- Zet de **service role key** nooit in het HTML-bestand. Die hoort alleen in de Edge Function (bijvoorbeeld voor Moneybird). Die functie heeft geen last van RLS.

---

## Bekende beperkingen

- E-mails worden ongeveer elke 15 minuten opgehaald, dus niet direct.
- Een CUF-bestand bevat geen klantgegevens. Die moet je met de hand invullen.
- Een nieuwe opmeting wordt pas opgeslagen als er een naam of adres is ingevuld.
- Voor de app is internet nodig. Zonder verbinding wordt er niet opgeslagen (het bolletje bovenin wordt dan rood).
- Bij een klus van meerdere dagen telt de agenda geen weekenden mee.
- De samenvatting als PDF loopt via de afdrukfunctie van de browser. Kies daar "Opslaan als PDF".
