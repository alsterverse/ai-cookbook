## Så inför du den i ditt projekt

Upplägget är tre delar, medvetet uppdelade. Allt i CLAUDE.md tynger varje session; allt i en skill betyder att ingenting fångas av sig självt.

1. Skillen — hela proceduren
    En fil, .claude/skills/knowledgebase/SKILL.md, som laddas bara när den används och därför kostar nästan ingenting i övrigt. Den är projektoberoende: den läser projektets CLAUDE.md för att hitta var basen ligger. Kopiera den rakt av till ~/.claude/skills/ så gäller den alla dina projekt.
2. Utlösaren — några rader i CLAUDE.md
    Regeln som får den att faktiskt köras: innan en tur avslutas som fattade ett beslut, ändrade ett delsystem, rörde infrastruktur, upptäckte ett problem eller etablerade en konvention — kör avstämningen. Utan den här biten är skillen bara en manual ingen slår upp.
3. Motiveringen — docs/knowledge-system.md
    Ett dokument som förklarar varför systemet ser ut som det gör. Det är vad som gör att nästa person förbättrar metoden i stället för att kringgå den.

På ett befintligt projekt börjar du med init-läget, som skannar repot och lägger upp strukturen med det som redan går att belägga i källan. Har du projektkunskap liggande i personligt minne flyttar migrate-memory över den — den frågar innan något raderas.

```
knowledgebase/
├── INDEX.md          en rad per post — den skannbara kartan
├── decisions/        beslut + varför (append-only; ersätt, skriv inte om)
├── architecture/     hur icke-uppenbara delar fungerar (levande)
├── issues/           problem (öppen / löst / workaround)
└── shared/           tvärgående premisser — en sanning, en plats
```

Gränsdragningen mot personligt minne är enkel: committat och om projektet → basen; privat och om dig → minnet.
