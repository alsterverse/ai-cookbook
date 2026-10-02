# Dokumentera beslut

Låt agenten skriva ner varje beslut som en fil i repot, och hålla filerna aktuella när projektet ändras. Det kostar några rader i `AGENTS.md`, och resonemanget överlever samtalet det fördes i.

## Problemet

De flesta beslut fattas i chatten, och chatten tar slut. Nästa session har bara koden, och koden visar *vad* som valdes men aldrig *varför*. Den river då upp beslut den inte förstår, eller vågar inte röra något alls. En agent minns inte förra veckan, och den läser dokumentation som styr tillbaka mot de standardval du medvetet valt bort.

## I praktiken

I en anteckningsapp får varje beslut en fil i `docs/decisions/`, ett ämne per fil, och en rad i `INDEX.md`. Efter några dagar fanns ett trettiotal. En av dem:

```markdown
# Expo services

## Decision

The project uses no Expo services and has no Expo account.

## Why

Expo's services require an Expo account, and the project avoids accounts it does not need.

## Consequences

- Expo's documentation often assumes EAS. Its build, submit and update steps are replaced by their local equivalents.
```

Den sista raden finns för att agenten inte ska följa Expos dokumentation in i en molntjänst som projektet valt bort.

## Varför det lönar sig

- **Avgjorda frågor förblir avgjorda.** En ny session läser indexet och arbetar inom besluten, i stället för att föreslå alternativet du redan förkastat.
- **Smak märks som smak.** *"Managed with npm. Preference."* säger att valet fritt kan ändras; *"chosen for its documented passkey PRF support"* säger att det inte kan det. Utan märkningen ser de likadana ut.
- **Underhållet blir gjort.** När ett beslut ändras uppdaterar agenten i samma commit de andra beslut som berörs, och indexet. Det är den delen människor hoppar över.
- **Öppna frågor förblir öppna.** En sektion `Not yet decided` visar var det avgjorda tar slut, så att agenten inte tyst fyller luckan själv.
- **Ändringar syns i granskningen.** Besluten ligger i git, bredvid koden som följer av dem.

## Kom igång

Lägg till i `AGENTS.md` eller `CLAUDE.md`:

```markdown
## Decisions

Decisions: `docs/decisions/`, one file per topic, listed with a one-line summary in `docs/decisions/INDEX.md`. A decision gets a file there when its reasoning is not obvious to a future reader, or when it has no reasoning beyond preference. A preference file says so explicitly. When a decision changes, update its file and every other decision that refers to it, in the same turn.
```

**Be om beslutsfilen före koden.** Ett beslut på tio rader granskas fortare än en diff, och är billigast att rätta innan något bygger på det.
