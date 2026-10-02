# Förfina en definition

Skriv ner din idé i en intent-fil, låt agenten intervjua dig om den, och låt den sedan skriva om filen. Du får en beskrivning som agenten kan bygga från utan att gissa.

## Problemet

En idé i huvudet är full av luckor man inte ser: beslut man inte vet att man inte har fattat. När agenten bygger måste den ändå fylla varje lucka, och den fyller dem med sina egna standardval. Resultatet ser ut som slumpmässiga beslut, men det är svar på frågor du aldrig fick. Motsägelser syns på samma sätt först när någon försöker bygga båda halvorna.

## Processen

1. **Skriv en intent-fil i repot**, till exempel `intent.md`, med vad du vill bygga och varför. Den får gärna vara ofullständig; det är luckorna intervjun ska hitta.
2. **Be agenten intervjua dig om filen** med [interview-skillen](../skills/interview/SKILL.md), och säg vad intervjun är till för: `/interview läs @intent.md och hjälp mig fylla i de luckor som behövs för att kunna bygga det.` Skillen är byggd för att hitta kärnan i en idé, så utan syftet frågar den om varför du vill bygga det i stället för vad som saknas.
3. **Svara på frågorna.** Skillen ställer en fråga i taget, eftersom nästa fråga beror på hur du svarade på den förra.
4. **Avsluta med:** "Uppdatera intent-filen som om jag hade skrivit det från början."

## Varför det fungerar

- **Frågorna hittar det du inte tänkt på.** Skillen riktar frågan mot den svagaste länken, och den nöjer sig inte med abstrakta ord som "enkel" eller "skalbar" förrän de säger något konkret om just det här fallet.
- **Motsägelser kommer fram före koden.** En motsägelse är billigare att lösa i en mening i en fil än i två moduler som bygger på var sitt antagande.
- **Idén förblir din.** Skillen ställer inga förslag förklädda till frågor. Det som hamnar i filen är det du kom fram till, inte det agenten hade valt.
- **Filen blir en beskrivning, inte ett protokoll.** Den sista meningen i processen gör att filen beskriver vad som ska byggas, i stället för hur samtalet gick. Nästa läsare var inte med i samtalet och behöver bara resultatet.
- **Beskrivningen ligger kvar.** Nästa session, och nästa utvecklare, läser samma fil. Beslut som kom fram under intervjun kan flyttas till beslutsfilerna (se [Dokumentera beslut](decisions-documentation.md)).

## Tips

- **Läs den omskrivna filen.** Kontrollera att den säger det du menar, och inte en rimlig tolkning av det.
- **Spara processen till det som är någorlunda komplext.** För en liten ändring kostar intervjun mer än agentens gissning.
