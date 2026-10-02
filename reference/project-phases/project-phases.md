# Projektfaser

Berätta för agenten vilken fas projektet är i, och vad varje fas tillåter. Det kostar en fil och en rad i `AGENTS.md`, och agenten tar rätt mängd risk för var projektet faktiskt befinner sig.

## Problemet

En agent har en och samma försiktighetsnivå, oavsett om projektet är tre dagar gammalt eller har tusen användare. Utan att veta mer lappar den hellre en dålig struktur än river den, eftersom det är det säkra valet i ett projekt med användare. Innan några användare finns är just det fel: varje lapp blir en skuld som är billigast att bli av med nu. Åt andra hållet behandlar en agent som fått höra "skriv om fritt" ett driftsatt projekt lika vårdslöst som ett nytt.

Vad som är rätt beror på fasen, och fasen syns inte i koden.

## I praktiken

I en anteckningsapp beskriver `project-phases.md` fem faser, var och en med vad som uppmuntras och principen bakom:

1. **Concept** — ingen kod, bara syftet och användningsfallen.
2. **Planning** — plattformar, arkitektur och teknikval, nedskrivna som beslut (se [Dokumentera beslut](../decisions-documentation.md)).
3. **Early development** — inga användare än, så allt får rivas och bytas: kod, beslut, bibliotek. Strukturproblem löses med omstrukturering, inte med lappar. Sänkt säkerhet är okej om det är ett nedskrivet beslut.
4. **Pre-deploy** — kritisk teknisk skuld löses och kodkvaliteten bekräftas, innan de första användarna kommer.
5. **Deployed** — användarna har data som måste skyddas. Stora omskrivningar bara när de bevisligen behövs, och ny kod återanvänder befintlig.

Överst i `AGENTS.md` står den aktuella fasen, som första rad agenten läser:

```markdown
**[Project](project-phases.md)**: Early development
```

## Varför det lönar sig

- **Rätt försiktighet på rätt plats.** I fas 3 föreslår agenten omstruktureringar den annars hade avstått från, och i fas 5 avstår den från dem.
- **Genvägar får ett slutdatum.** Anteckningsappen kör tidigt med en hårdkodad användare och nyckeln i vanlig applagring. Beslutsfilen säger att genvägarna gäller fas 3 och ska vara lösta före fas 4, så de kan inte glömmas kvar.
- **Frågor kan skjutas upp till rätt fas.** Om ett paket med en enda underhållare ska användas eller byggas om avgörs i fas 4, när det faktiskt behövs. Beslutet säger det, i stället för att frågan blir hängande.
- **Arbetet sorteras efter fas.** Board:en har en kolumn för det som måste vara klart före fas 4 men inte tidigare.

## Kom igång

Kopiera [project-phases-template.md](project-phases-template.md) till projektet som `project-phases.md`, anpassa faserna, och lägg den aktuella fasen överst i `AGENTS.md` eller `CLAUDE.md`. Principen är det viktiga. "Allt får skrivas om, eftersom det ännu inte kan skada någon" låter agenten avgöra fall som ingen regel förutsåg.

**Byt fas själv.** Det är utvecklarens ansvar att raden i `AGENTS.md` stämmer; agenten ska inte flytta projektet till nästa fas på eget initiativ.
