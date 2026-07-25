# Automatisering af denne fork

Alt kører af sig selv. Du behøver ikke trykke på noget.

## Sådan hænger det sammen

| Workflow | Starter | Gør |
| --- | --- | --- |
| `sync-upstream-build.yml` | Hver 3. time (automatisk) + manuelt | Henter nye ændringer fra originalen `NuvioMedia/NuvioMobile` (branch `cmp-rewrite`), pusher dem til forken og starter et nyt IPA-byg – men kun hvis der faktisk var noget nyt. |
| `build-ios-unsigned.yml` | Hvert push til `cmp-rewrite` + manuelt | Bygger en ny IPA af min egen kode. |
| `build-ipa.yml` | Kaldes af de to ovenfor | Selve byg-opskriften: bygger unsigned IPA og udgiver den som GitHub Release. Ligger ét sted, så de to workflows aldrig kommer ud af trit. |

## Hvad kommer der ud af det

Hvert byg udgiver den samme IPA to steder:

- **`ios-latest`** – rullende release der altid peger på det nyeste byg. Brug det link, hvis du bare vil have den nyeste version.
- **`v<version>-<build>`** (fx `v0.2.12-84`) – én release pr. app-version, så en bestemt version kan hentes igen senere. Versionen læses automatisk fra `iosApp/Configuration/Version.xcconfig`.

IPA'en ligger også som build-artifact i 14 dage.

## Hvis noget går galt

Fejler synken eller bygget, opretter automatikken et issue med titlen
**"Automatisk synk/byg fejlede"** og et link til kørslen. Sker det igen, kommer
der bare en ny kommentar på det samme issue – ikke en stak nye issues.

Den typiske årsag er en merge-konflikt mellem forken og originalen. Den skal
løses manuelt én gang, så kører resten videre af sig selv bagefter.

## Detaljer værd at vide

- Synken rører aldrig `.github/workflows`. Ændrer originalen en workflow-fil,
  beholdes vores egen udgave – GitHubs robot-token må ikke skrive i workflows.
- Push lavet af robotten udløser ikke `build-ios-unsigned.yml`. Derfor bygger
  synk-workflowen selv, og du får ikke dobbelte byg.
- Der bygges `Debug` og ikke `Release`: Release-optimeringen af Kotlin/Native
  bruger 8–12 GB RAM og løber tør på den gratis byggeserver.
- GitHub slår planlagte workflows fra, hvis der ikke har været aktivitet i
  repoet i 60 dage. Sker det, får du en mail – tryk "Enable workflow" i
  Actions-fanen, så kører den videre.
