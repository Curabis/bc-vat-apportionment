# Splitmoms i Business Central: kravspecifikation

Udkast 0.1, 19. november 2026. Til faglig og teknisk gennemlæsning.
Momslovens § 38, stk. 1, og momsbekendtgørelsens § 17.

Denne fil er kilden. Word- og PDF-udgaven i CURABIS-layout bygges ud fra samme indhold.

## 1. Formål

En virksomhed, der har både momspligtig og momsfri omsætning, må kun trække en del af momsen på sine fællesomkostninger fra. Det gælder fx husleje, el og it, som bruges til begge dele. Andelen kaldes fradragsprocenten. Den beregnes ud fra omsætningen, bruges foreløbigt i løbet af året og reguleres, når året er gjort op.

Typiske virksomheder er lægeklinikker og tandlæger, ejendomsselskaber med både bolig- og erhvervsudlejning, private undervisningssteder, foreninger, museer og finansielle virksomheder. Det er kandidater. Ikke alle virksomheder i de brancher er omfattet.

Business Central kan i dag trække en fast procent af købsmomsen fra med funktionen Ikke-fradragsberettiget moms. Business Central beregner ikke procenten ud fra omsætningen, skifter den ikke pr. år og laver ikke årsreguleringen. Det gør virksomheden i dag uden for systemet. Microsoft har bygget den samme mekanik til den tjekkiske lokalisering, men ikke til den danske.

Denne app lukker hullet for danske virksomheder. Den bygger på Business Centrals egen beregning af ikke-fradragsberettiget moms og leverer det, der mangler: procenten pr. år, opgørelsen og reguleringen.

## 2. Sådan virker det

- **Klassificér omsætningen.** Hver kombination i momsbogføringsopsætningen får en rolle: fradragsberettiget omsætning, momsfri omsætning, udelades eller fællesomkostning.
- **Beregn procenten.** Appen beregner fradragsprocenten ud fra årets bogførte omsætning.
- **Brug den foreløbige procent.** Sidste års endelige procent bliver dette års foreløbige, og den lægges automatisk på alle køb af fællesomkostninger efter momsdatoen.
- **Gør året op.** Når regnskabsåret er slut, beregnes den endelige procent.
- **Regulér.** Forskellen mellem foreløbig og endelig procent bliver til kladdelinjer, som du godkender og bogfører. Reguleringen kommer med i den rigtige momsangivelse.

## 3. Begreber

| Begreb | Betydning |
|---|---|
| Fradragsprocent | Fradragsberettiget omsætning delt med samlet omsætning, begge uden moms, rundet op til helt tal. |
| Foreløbig procent | Den procent, der bruges i løbet af året. Normalt sidste års endelige procent. |
| Endelig procent | Den procent, årets faktiske omsætning giver, når året er slut. |
| Fællesomkostning | Køb, der bruges til både momspligtig og momsfri aktivitet. Kun her bruges fradragsprocenten. |
| Direkte henførbart køb | Køb, der kun vedrører den ene aktivitet. Giver fuldt fradrag eller intet fradrag. |
| Momsdato | Den dato, der afgør, hvilken momsperiode et bilag hører til. I Business Central: Momsdato (VAT Reporting Date). |
| Udelades | Omsætning, der hverken tæller i tæller eller nævner, fx salg af driftsmidler og bitransaktioner. |
| Årsregulering | Rettelsen af årets fradrag fra foreløbig til endelig procent. |

## 4. Forretningsregler

Hver regel har et nummer, som går igen i testsuiten. Status viser, om testen findes og er grøn. Planlagt betyder, at testen skrives, før koden bygges, og først skal fejle.

### 4.1 Fradragsprocenten

| # | Regel | Eksempel | Status |
|---|---|---|---|
| 1 | Fradragsprocenten er fradragsberettiget omsætning delt med samlet omsætning, begge uden moms. | 600.000 af 1.000.000 giver 60 %. | Planlagt |
| 2 | Procenten rundes altid op til nærmeste hele tal. | 60,01 % giver 61 %. | Planlagt |
| 3 | Et helt tal rundes ikke. | 60,00 % giver 60 %. | Planlagt |
| 4 | Eksport og andet salg med 0-sats tæller som fradragsberettiget omsætning. | Eksportsalg indgår i både tæller og nævner. | Planlagt |
| 5 | Omsætning med rollen Udelades tæller ikke med. | Salg af en bil fra driften indgår hverken i tæller eller nævner. | Planlagt |
| 6 | Kreditnotaer på salg trækker omsætningen ned. | Faktura 100.000 og kreditnota 10.000 giver 90.000. | Planlagt |
| 7 | Kun momsfri omsætning giver 0 %. | Klinik uden momspligtigt salg. | Planlagt |
| 8 | Kun momspligtig omsætning giver 100 %. | Intet momsfrit salg i året. | Planlagt |
| 9 | Et år uden omsætning giver en fejl, og ingen procent gemmes. | Tomt regnskabsår. | Planlagt |
| 10 | Salg uden momsposter vises i en kontrolrapport, så du kan se, om omsætningen er komplet. | Salg bogført i finanskladde uden bogføringsgrupper. | Planlagt |

### 4.2 Den foreløbige procent

| # | Regel | Eksempel | Status |
|---|---|---|---|
| 11 | Sidste års endelige procent bliver årets foreløbige. | 2026 endelig 34 % giver 2027 foreløbig 34 %. | Planlagt |
| 12 | Er sidste år ikke endeligt opgjort, når årets første momsangivelse indsendes, bruges året før. | 2025 endelig 31 %, 2026 ikke opgjort: 2027 foreløbig 31 %. | Planlagt |
| 13 | Samme foreløbige procent gælder hele året. Den kan ikke ændres, når der er bogført bilag med den. | Rettelse efter første bogføring afvises. | Planlagt |
| 14 | Procenten i momsbogføringsopsætningen kan ikke rettes manuelt for splitmoms-kombinationer. Procenten kommer fra året. | Manuel rettelse afvises med en forklaring. | Planlagt |

### 4.3 Bogføring af køb

| # | Regel | Eksempel | Status |
|---|---|---|---|
| 15 | Et køb af en fællesomkostning får årets foreløbige procent. | Foreløbig 34 % giver 66 % ikke-fradragsberettiget moms. | Grøn |
| 16 | Momsdatoen afgør, hvilket års procent der bruges. | Momsdato december 2026, bogført januar 2027: 2026-procenten. | Grøn |
| 17 | En købsordre, der oprettes i ét år og faktureres i det næste, får procenten for momsdatoens år. | Ordre fra 2026 med momsdato i 2027 får 2027-procenten. | Planlagt |
| 18 | Et køb, der kun vedrører momspligtigt salg, giver fuldt fradrag. | Varer til videresalg. | Planlagt |
| 19 | Et køb, der kun vedrører momsfrit salg, giver intet fradrag. | Forbrug kun til behandling. | Planlagt |
| 20 | Finanskladden følger samme regel som købsfakturaen. | Kladdelinje med splitmoms-kombination. | Planlagt |
| 21 | Kreditnota på en fællesomkostning: procent fra den oprindelige faktura eller den aktuelle? | Se åbne spørgsmål. | Åben |

Grønne test: regel 15 er `PostPurchInvoice_CommonCost_UsesProvisionalPctOfYear`, regel 16 er `PostPurchInvoice_VATDateInPreviousYear_UsesPreviousYearPct` (codeunit `CURVATApportionPosting`).

### 4.4 Nyetablerede virksomheder

| # | Regel | Eksempel | Status |
|---|---|---|---|
| 22 | I det første regnskabsår beregnes den foreløbige procent pr. momsperiode ud fra periodens omsætning. | Første kvartal: 40 % momspligtig giver 40 %. | Åben |
| 23 | Andet år bruger første års endelige procent. | Første år endelig 37 % giver år 2 foreløbig 37 %. | Planlagt |

### 4.5 Årsregulering

| # | Regel | Eksempel | Status |
|---|---|---|---|
| 24 | Er den endelige procent højere end den foreløbige, får virksomheden ekstra fradrag. | Foreløbig 34 %, endelig 38 %: købsmomsen stiger. | Planlagt |
| 25 | Er den endelige procent lavere, betaler virksomheden fradrag tilbage. | Foreløbig 34 %, endelig 30 %: købsmomsen falder. | Planlagt |
| 26 | Er der ingen forskel, laves der ingen regulering, og året markeres som reguleret. | Foreløbig og endelig 34 %. | Planlagt |
| 27 | Reguleringen hører til den momsperiode, der omfatter 6. måned efter regnskabsårets udløb. | Kalenderår 2026 giver perioden med juni 2027. | Planlagt |
| 28 | Samme regel gælder et forskudt regnskabsår. | Regnskabsår 1/7 til 30/6 giver perioden med december. | Planlagt |
| 29 | Reguleringen kommer med i momsangivelsen for perioden som købsmoms. | Beregnet momsangivelse viser beløbet. | Planlagt |
| 30 | Et år kan kun reguleres én gang. | Anden regulering afvises. | Planlagt |

### 4.6 Ændringer efter opgørelsen

| # | Regel | Eksempel | Status |
|---|---|---|---|
| 31 | Bogføres der salg med momsdato i et år, der er endeligt opgjort, får brugeren en advarsel. | Salg i 2026 bogført efter opgørelsen af 2026. | Planlagt |

## 5. Kanttilfælde, der testes

Situationer, hvor standardens egne bogføringsveje kan gå uden om appens procent. De testes mod rigtig bogføring.

| # | Situation | Hvorfor den testes |
|---|---|---|
| 32 | Periodisering på en splitmoms-linje | Standarden har en egen vej for periodiserede bilag. |
| 33 | Køb af ydelser fra EU (omvendt betalingspligt) | Ikke-fradragsberettiget moms beregnes også her. |
| 34 | Faktura i fremmed valuta | Afrunding af den ikke-fradragsberettigede del i kroner og valuta. |
| 35 | Delvis modtagelse og fakturering over et årsskifte | Én ordre, to år og to procenter. |
| 36 | Forudbetalingsfaktura | Egen bogføringsvej. |
| 37 | Varegebyr og anlægsanskaffelse | Ikke-fradragsberettiget moms lægges på kostprisen. |
| 38 | Kopiér bilag fra sidste år | Linjen kan arve sidste års procent. |
| 39 | Skudår og regnskabsår 1/3 til 28/2 | Datogrænser. |
| 40 | To brugere bogfører, mens procenten beregnes | Låse og ens læsning af data. |

## 6. Designvalg

- Appen bruger Business Centrals egen beregning af ikke-fradragsberettiget moms. Den svarer med årets procent gennem standardens hændelser, samme mønster som Microsofts tjekkiske lokalisering.
- Procenten slås op pr. momsdato i en tabel pr. regnskabsår. Momsbogføringsopsætningen ændres ikke ved årsskiftet.
- Rollen på en momskombination er ét felt med faste værdier, så umulige kombinationer ikke kan gemmes.
- Reguleringen bliver til kladdelinjer, der bogføres gennem standardkladden. Appen skriver aldrig direkte i momsposterne.
- Appen kalder aldrig Commit midt i en proces.
- Designet er gennemgået i CURABIS' arkitekturreview før bygning.

## 7. Sådan testes

| Lag | Regler | Kører mod |
|---|---|---|
| Hurtige test uden database | 1-3, 7-9, 11-12, 22-28 (beregning og datoer), 39 | Erstatningsdata bag interfaces |
| Integrationstest | 4-6, 10, 13-21, 29-38 | Rigtige momsposter og standardbogføring |
| Ydelse | 40 og et skaleret år | Telemetri |

- Hver test har én handling og skal fejle, før koden bygges.
- Testdata er en fodterapeut med ejer og to ansatte, ca. 3.000 bilag om året. Data dannes ud fra et frø, så samme frø giver samme år, og alle kan genskabe tallene.
- Bogføringen erstattes ikke i test. Risikoen ligger i standardens egen beregning, så den testes mod rigtig bogføring.
- Kodedækning måles og lægges frem. Målet er 0 advarsler fra CodeCop, UICop, AppSourceCop og LinterCop.

## 8. Uden for denne version

- Regulering af investeringsgoder over flere år (momsbekendtgørelsens § 84).
- Ophørte virksomheder og fællesregistrering.
- Virksomheder etableret i flere lande, hvor procenten beregnes pr. etablering.

## 9. Åbne spørgsmål

| # | Spørgsmål | Afgør |
|---|---|---|
| A | Skal en kreditnota på en fællesomkostning bruge procenten fra den oprindelige faktura eller den aktuelle? | Regel 21 |
| B | Skal bitransaktioner genkendes i denne version, eller markeres de manuelt som Udelades? | Regel 5 |
| C | Skal reguleringen være kladdelinjer til godkendelse eller bogføres automatisk? Anbefalet: kladde til godkendelse. | Regel 24-30 |
| D | Virksomheden skal én gang om året oplyse den foreløbige procent og regnskabsårets slutdato i momsangivelsen. Kan Business Centrals momsangivelse bære felterne? | Ikke undersøgt |
| E | Skal nyetablerede virksomheder med i denne version? | Regel 22 |
| F | Lander reguleringen i momsangivelsen som en momsbærende kladdelinje eller via momsangivelsens linjer for ikke-fradragsberettiget moms? | Regel 29 |

## 10. Status

Appen er bygget til Business Central 29. Den første test har bevist, at standardens hændelse kan bruges fra en app, og to integrationstest er grønne (regel 15 og 16). Koden og denne specifikation lægges offentligt i repoet, før appen indsendes til Microsofts bidragsspor for Business Central.

## 11. Kilder

- Momsloven § 38, stk. 1, og momsbekendtgørelsen § 17.
- Den juridiske vejledning D.A.11.4.2.1 Delvis fradragsret og D.A.11.4.2.2 Opgørelse af omsætning, Skattestyrelsen.
- Microsoft Learn: Set up nondeductible VAT og Design details: Nondeductible VAT.
- Microsoft BCApps, Core Localization Pack for Czech: datostyret koefficient og årsregulering.
