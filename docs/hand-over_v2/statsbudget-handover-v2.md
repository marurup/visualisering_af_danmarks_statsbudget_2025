# Hand-over: Dansk Statsbudget Visualisering 2025 — Version 2

**Opdateret:** 28. maj 2026  
**Status:** Teknisk fundament komplet — næste fokus er datakvalitet

---

## 1. Hvad er nyt siden v1?

Version 1 (mobilsession) leverede en basisfunktionel visualisering med donut-diagram, ét niveau drill-down og per-borger-toggle.

Version 2 tilføjer:

| Funktion | Beskrivelse |
|---|---|
| **3-niveau drill-down** | Kategori → underkategori → under-underkategori med brødkrumme-navigation |
| **Estimat-badges** | `~estimat` markerer poster der er beregnet frem for direkte kildehentet |
| **Kildereferencer på alle niveauer** | Note + klikbart link til officiel kilde pr. segment |
| **Drill-indikatorer på canvas** | Hvid bue på yderkanten af segmenter med underkategorier |
| **GitHub Pages + Actions** | Automatisk deploy med versionsinfo (git-hash + dato) i footeren |

---

## 2. Live status

| Felt | Værdi |
|---|---|
| Live URL | https://marurup.github.io/visualisering_af_danmarks_statsbudget_2025/ |
| Repository | https://github.com/marurup/visualisering_af_danmarks_statsbudget_2025 |
| Deployment | GitHub Actions → Pages (automatisk ved push til `main`) |
| Arkitektur | Én enkelt HTML-fil — ingen server, ingen build-step |
| Drill-dybde | 3 niveauer for udvalgte kategorier, 2 for resten |

---

## 3. Teknisk arkitektur (komplet)

### Diagram-engine (`buildPie`)
- Tegner på HTML Canvas API med manuel geometriberegning og hit-testing
- **To-pas-tegning**: fills i pas 1, drill-indikatorer i pas 2 (aldrig overdrawet)
- `drillStack[]` holder den aktuelle navigationssti — understøtter ubegrænset dybde
- `drillDown(idx)` bevarer `children`, `note`, `kilde` og `estimate` fra child-objektet

### Drill-indikatorer (hvide buer)
- Vises kun på niveauer med en **blanding** af drillable og leaf-segmenter
- Skjules på topniveauet (alle segmenter er drillable — ingen distinktion)
- Tegnes `OUTSET = 0.008 rad` **bredere** end segmentet (aldrig inverteret ved små segmenter)
- Kun den hovered ring er synlig under hover; øvrige springes over

### Per-borger-toggle
- `BEFOLKNING = 5_932_654` (Danmarks Statistik, jan. 2025)
- CSS-klassen `body.per-borger` styrer farveoverganges via custom properties
- Alle visninger (legende, center-label, detalje-panel, summary-kort) opdateres synkront

### GitHub Actions deploy
```yaml
# .github/workflows/deploy.yml (forkortet)
- name: Indsæt versionsinfo
  run: |
    SHORT=$(git rev-parse --short HEAD)
    FULL=$(git rev-parse HEAD)
    DATE=$(git log -1 --format='%cd' --date='format:%Y-%m-%d %H:%M UTC')
    sed -i "s|%%GIT_SHORT%%|${SHORT}|g" index.html
    sed -i "s|%%GIT_FULL%%|${FULL}|g"   index.html
    sed -i "s|%%GIT_DATE%%|${DATE}|g"   index.html
```

### Kendte design-valg (ikke bugs)
- `pct`-feltet beregnes ikke automatisk — skal justeres manuelt ved dataopdatering
- Farverne på niveau 2+ genereres automatisk via HSL-rotation fra forældrefarven
- Segmenter med `sweep < 2 × OUTSET` vises uden ring-indikator (legenden viser stadig `›`)

---

## 4. Dataoversigt og -kvalitet

### Udgifter — 938 mia. kr. (samlet offentlig sektor)

| Kategori | Mia. kr. | Niveau 2 | Niveau 3 | Kilde |
|---|---|---|---|---|
| Social beskyttelse | 385 | ✅ | ✅ delvist | FM FL 2025, §§ 15 & 35 |
| Sundhedsvæsen | 169 | ✅ | ✅ delvist | FM + Regionernes aftale |
| Uddannelse | 113 | ✅ | ✅ delvist | UVM |
| Generelle tjenester | 113 | ✅ | ✅ delvist | FM Statens regnskab |
| Forsvar & politi | 60 | ✅ | ✅ delvist | FMN |
| Infrastruktur & transport | 47 | ✅ | ❌ | Transportministeriet |
| Erhverv & arbejdsmarked | 28 | ✅ | ❌ | BM |
| Miljø & klima | 15 | ✅ | ❌ | KEFM |
| Kultur & fritid | 8 | ✅ | ✅ fuldt | Kulturministeriet |

### Indtægter — 980 mia. kr.

| Kategori | Mia. kr. | Niveau 2 | Niveau 3 | Kilde |
|---|---|---|---|---|
| Personskatter & AM-bidrag | 432 | ✅ | ❌ | SKAT |
| Moms (25%) | 216 | ✅ | ❌ | SKAT |
| Selskabs- & kapitalskatter | 147 | ✅ | ❌ | SKAT |
| Afgifter | 127 | ✅ | ✅ delvist | SKAT + SKM |
| Bidrag & gebyrer | 39 | ✅ | ❌ | DST |
| Ejendomsskatter | 12 | ✅ | ❌ | Vurderingsstyrelsen |
| Øvrige indtægter | 7 | ✅ | ❌ | FM |

**Topniveau-tallene er officielle.** Underkategorier (niveau 2–3) er i mange tilfælde estimater — markeret med `~estimat` i UI'et.

---

## 5. Næste fokus: Datakvalitet og -præcision

Det tekniske fundament er komplet. Næste skridt handler om at gøre tallene mere præcise og dækkende.

### Højprioriteret

**1. Verificér og opdatér niveau-2 tal fra Finanslovsdatabasen**
- FM stiller en søgbar database til rådighed med § og bevillingsnummer
- URL: https://www.fm.dk/oekonomi-og-tal/finanslov/finanslovsdatabasen
- Mål: erstat de estimerede niveau-2 tal med direkte FL-linjer

**2. Tilføj niveau-3 for de resterende kategorier**
- Infrastruktur & transport, Erhverv & arbejdsmarked, Miljø & klima, Bidrag & gebyrer
- Personskatter (stat vs. kommuneskat er allerede opdelt — overvej yderligere)

**3. Adskil stat vs. kommuner/regioner tydeligere**
- Nuværende tal dækker den samlede offentlige sektor (~938/980 mia.)
- Statens egne udgifter (finansloven) er ca. 600 mia.
- Mulig løsning: toggle "Finansloven alene / Hele den offentlige sektor"

### Middelprioriteret

**4. Sammenlign med 2023/2024**
- Kræver at tidligere års data indsamles med samme kategorisering
- Mulig UI: slider eller dropdown med årstal

**5. Revidér kategorigrænser**
- "Generelle tjenester" (113 mia.) er en bred samlepost — overvej opsplitning
- "Erhverv & arbejdsmarked" (28 mia.) kan evt. slås op i separate kategorier

**6. Mobiloptimering**
- Nuværende layout er responsivt men ikke optimalt på < 480px
- Overvej vertikalt layout (diagram over legende) på telefoner

---

## 6. Sådan redigerer du data

Al data ligger øverst i `<script>`-blokken i `index.html`:

```js
const UDGIFTER = [
  {
    name: "...", value: 385, color: "#e05c6b", pct: 41, icon: "🏠",
    note: "...",
    kilde: { tekst: "...", url: "https://..." },
    children: [
      {
        name: "...", value: 120, estimate: false,
        note: "...",
        kilde: { tekst: "...", url: "..." },
        children: [
          { name: "...", value: 78, estimate: true },
        ]
      }
    ]
  }
]
```

**Husk ved opdatering:**
- Justér `pct` manuelt (beregnes ikke automatisk)
- Sæt `estimate: true` på alle poster der ikke er direkte FA-linjer
- Opdatér `note` og `kilde` med den korrekte kilde

---

## 7. Prompt til næste session

Kopier dette ind i en ny chat:

> Jeg har en interaktiv HTML-visualisering af det danske statsbudget 2025 med 3-niveau drill-down, per-borger-visning, estimat-badges og kildereferencer. Det tekniske fundament er komplet og sitet kører live på GitHub Pages (https://marurup.github.io/visualisering_af_danmarks_statsbudget_2025/). Nu vil jeg fokusere på datakvaliteten. [Beskriv hvad du vil arbejde med — f.eks. "Hent korrekte niveau-2 tal fra Finanslovsdatabasen for Social beskyttelse" eller "Tilføj niveau-3 data for Infrastruktur & transport" eller "Lav en toggle der adskiller statens budget fra kommuner/regioner"]

---

*Oprettet med [Claude Code](https://claude.ai/code) (Anthropic)*
