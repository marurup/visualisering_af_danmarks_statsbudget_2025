# Hand-over: Dansk Statsbudget Visualisering 2025

**Oprettet:** 27. maj 2026  
**Konversation afsluttet på mobil — fortsættes på PC**

---

## 1. Hvad er dette projekt?

En interaktiv, browserbaseret visualisering af det danske statsbudget for 2025. Formålet er at give borgere et intuitivt overblik over, hvad deres skattepenge bruges til — og hvorfra de kommer.

Visualiseringen har to sider (udgifter / indtægter) med et klikbart donut-diagram, tegnforklaring og et detalje-panel der folder ud ved klik.

---

## 2. Nuværende status

Version 1.0 er færdigbygget og klar til brug. Leveret som én enkeltstående HTML-fil.

| Felt | Værdi |
|---|---|
| Filnavn | `dansk-statsbudget.html` |
| Filtype | Enkeltstående HTML (ingen server krævet) |
| Datakilde | Finansministeriet + Danmarks Statistik 2025 |
| Udgiftsdata | 9 kategorier, ~938 mia. kr. samlet |
| Indtægtsdata | 7 kategorier, ~980 mia. kr. samlet |
| Afhængigheder | Kun Google Fonts (DM Serif Display + DM Sans) |
| Browserkompatibilitet | Alle moderne browsere |

---

## 3. Funktioner i v1.0

- Donut-diagram med 9 udgifts- og 7 indtægtskategorier
- Klik på segment eller legendelinje åbner detalje-panel med underposter
- Hover-effekt: segment hæves og centerlabel opdateres dynamisk
- Faner øverst skifter mellem Udgifter og Indtægter
- Opsummerings-kort viser totaler og største poster
- Mørkt tema, dansk typografi, responsivt layout
- Ingen backend — fungerer blot ved at åbne HTML-filen i en browser

---

## 4. Mulige next steps

### Datakvalitet
- Hent faktiske tal direkte fra Finanslovsdatabasen på fm.dk (API eller CSV-eksport)
- Adskil statsbudgettet fra kommuner/regioner for mere præcision
- Tilføj note-felt per kategori med link til kilden

### UX & interaktion
- "Per borger"-visning (del med ~5,9 mio. indbyggere) — f.eks. "9.500 kr./person til sundhed"
- Sammenlign med tidligere år (2023, 2024) via slider eller dropdown
- Tilføj søgefunktion i legendelisten
- Mobiloptimering: skift til vertikalt layout på små skærme

### Teknisk
- Erstat Canvas-tegning med D3.js for nemmere vedligeholdelse og animation
- Del op i en JSON-datafil + HTML/JS for lettere dataopdatering
- Byg som React-komponent hvis det skal indgå i en større hjemmeside
- URL-deling: `?view=udgifter&segment=sundhed` loader direkte til et segment

---

## 5. Sådan redigerer du data

Al data ligger øverst i `<script>`-blokken som to JavaScript-arrays:

```js
const UDGIFTER = [
  { name: '...', value: 385, color: '#e05c6b', pct: 41, icon: '🏠', children: [...] },
  ...
]
const INDTAEGTER = [
  { name: '...', value: 432, color: '#4e9fff', pct: 44, icon: '👤', children: [...] },
  ...
]
```

Hvert objekt har følgende felter:

| Felt | Beskrivelse |
|---|---|
| `name` | Kategoriets navn (vises i legende og detalje-panel) |
| `value` | Beløb i mia. kr. |
| `color` | Hex-farvekode for segmentet |
| `pct` | Procentandel (opdateres ikke automatisk — justér manuelt) |
| `icon` | Emoji der vises i legende og detalje-overskrift |
| `children` | Array af underposter: `{ name, value }` — vises ved klik |

For at skifte farvetema: CSS-variablerne øverst i `<style>`-blokken styrer alt. Ændr `--bg`, `--surface` og `--accent`.

---

## 6. Prompt til at genoptage konversationen

Kopier dette ind i en ny chat:

> Jeg har en eksisterende HTML-visualisering af det danske statsbudget 2025 (donut-chart med udgifter og indtægter, klik-til-detalje funktionalitet). Jeg vil gerne videreudvikle den. [Beskriv hvad du ønsker — f.eks. "Tilføj en per-borger-visning" eller "Opdater tallene fra finansloven for 2026"]

---

*Oprettet med Claude (Anthropic) · claude.ai*
