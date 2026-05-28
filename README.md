# Det Danske Statsbudget 2025 — Interaktiv Visualisering

En browserbaseret visualisering af det danske statsbudget for 2025, bygget med ren HTML, CSS og Canvas API — ingen server, ingen frameworks, ingen bundler.

🔗 **[Se visualiseringen live](https://marurup.github.io/visualisering_af_danmarks_statsbudget_2025/)**

---

## Funktioner

- **Donut-diagram** med udgifter og indtægter (skift via faneblade)
- **Tre-niveau drill-down** — klik på et segment for at åbne underkategorier, og dyk endnu dybere for udvalgte poster; brødkrumme-navigation lader dig gå tilbage ét niveau ad gangen
- **Per-borger-visning** — skift mellem samlet beløb og beløb pr. dansker (5.932.654 indbyggere, jan. 2025)
- **Kildereferencer** — hvert segment har en note og et link til officiel dokumentation
- **Estimat-markering** — poster der er beregnet/estimeret vises med `~estimat`-badge
- **Drill-indikatorer** — hvid bue på yderkanten af segmenter der kan åbnes
- **Responsivt mørkt tema** med dansk typografi (DM Serif Display + DM Sans)
- **Versionsinfo** i footeren — klikbar git-hash linker direkte til det deployede commit

---

## Teknisk stack

| Komponent | Teknologi |
|---|---|
| Diagram | HTML Canvas API (vanilla JS) |
| Stil | Ren CSS med custom properties |
| Typografi | Google Fonts (DM Serif Display, DM Sans) |
| Deployment | GitHub Pages via GitHub Actions |
| Afhængigheder | Ingen (udover Google Fonts CDN) |

Hele applikationen er én enkelt HTML-fil. Den kan åbnes direkte i en browser uden server.

---

## Filstruktur

```
/
├── index.html                    # Hele applikationen (HTML + CSS + JS i én fil)
├── dansk-statsbudget.html        # Meta-refresh redirect → index.html
├── .nojekyll                     # Deaktiverer Jekyll-processing på GitHub Pages
├── .github/
│   └── workflows/
│       └── deploy.yml            # Auto-deploy + versionsinfo-injektion
└── docs/
    ├── hand-over_v1/             # Session 1 (mobil) — basisfunktionalitet
    └── hand-over_v2/             # Session 2 (PC) — teknisk komplet fundament
```

---

## Datastruktur

Al data ligger som JavaScript-arrays øverst i `<script>`-blokken i `index.html` (`const UDGIFTER` og `const INDTAEGTER`). Hvert segment har følgende struktur:

```js
{
  name:  "Social beskyttelse",   // Vises i legende og detalje-panel
  value: 385,                    // Beløb i mia. kr.
  color: "#e05c6b",              // Hex-farve på segmentet
  pct:   41,                     // % af total — justeres manuelt
  icon:  "🏠",                   // Emoji i legende og brødkrumme
  note:  "Folkepension alene…",  // Konteksttekst i detalje-panel
  kilde: {
    tekst: "Finansloven 2025, §§ 15 & 35",
    url:   "https://www.fm.dk/…"
  },
  children: [                    // Niveau 2 — samme struktur rekursivt
    {
      name:     "Folkepension",
      value:    120,
      estimate: false,           // true → viser ~estimat-badge
      note:     "Ca. 782.000…",
      kilde:    { tekst: "…", url: "…" },
      children: [                // Niveau 3
        { name: "Grundpension", value: 78, estimate: true },
        …
      ]
    }
  ]
}
```

**Bemærk:** `pct` beregnes ikke automatisk — justér manuelt ved dataopdatering, eller brug `Math.round(value / total * 100)`.

---

## Lokal brug

Ingen installation nødvendig:

```bash
# macOS
open index.html

# Windows
start index.html

# Linux
xdg-open index.html
```

---

## Deployment

Projektet deployes automatisk til GitHub Pages ved hvert push til `main` via `.github/workflows/deploy.yml`. Workflowet:

1. Henter git-hash og commit-dato
2. Injicerer dem som versionsinfo i footeren (`%%GIT_SHORT%%`, `%%GIT_FULL%%`, `%%GIT_DATE%%`)
3. Uploader og deployer via `actions/deploy-pages`

For at aktivere: **Settings → Pages → Source → GitHub Actions**

---

## Datakilder

Tal dækker den samlede offentlige sektor (stat, kommuner og regioner) og stammer primært fra:

- [Finansministeriet – Finansloven 2025](https://www.fm.dk/oekonomi-og-tal/finanslov/finansloven-2025)
- [Danmarks Statistik – Offentlige finanser](https://www.dst.dk/da/Statistik/emner/oekonomi/offentlige-finanser)
- [Skatteministeriet – Skatte- og afgiftsstatistik](https://www.skm.dk/skattetal/statistik/)
- [Regionernes økonomiaftale 2025](https://www.fm.dk/oekonomi-og-tal/regionernes-oekonomi)

Poster markeret med `~estimat` er beregnet ud fra offentliggjorte deltal og metodebeskrivelser.

---

*Data: Finansministeriet & Danmarks Statistik 2025 · Bygget med [Claude Code](https://claude.ai/code) (Anthropic)*
