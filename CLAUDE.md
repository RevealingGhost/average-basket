# CLAUDE.md

Ez a fájl a Claude Code munkamenetek kontextusa ehhez a repóhoz.

## A projekt

Átlagkosár-elemzés egy magyar kiskereskedelmi hálózat számára. A cél egy elemzői portál, ahol a vezetői (high-level) áttekintés és a részletes elemzés ugyanott érhető el.

- **Nyelv:** minden felhasználói szöveg, commit-üzenet és dokumentáció **magyarul** készül.
- **Számformátum:** `hu-HU` (szóköz ezreselválasztó, tizedesvessző), pénz: `Ft` / `M Ft`, százalék előjellel (`+0,7%`, `−1,4%`, valódi mínuszjellel).

## Fájlok

| Fájl | Mi ez |
|---|---|
| `index.html` | Az eredeti átlagkosár-dashboard. Önálló HTML, a D3 be van ágyazva (nagy fájl, ~400 KB). Fülek: vezetői (`exec`), részletes (`detail`), adatminőség (`quality`). CSV-t lehet betölteni (`.csv`, `.txt`), van beépített mintaadat (2026. és 2025. szeptember). |
| `portal-koncepcio/index.html` | **Kosárpulzus** portál-koncepció. Önálló HTML, külső könyvtár nélkül (csak Google Fonts), a grafikonok kézzel írt SVG-k. Generált mintaadattal dolgozik. |
| `docs/munkamenet-*.md` | Munkamenet-összefoglalók: mi készült, miért, mi a következő lépés. |

Az `index.html` túl nagy ahhoz, hogy egyben olvasd. Keress benne (`grep`), vagy részletekben olvasd.

## Adatmodell

Egyetlen havi ténytábla, ebből számol minden mutató:

| Oszlop | Jelentés |
|---|---|
| `period` | Hónap, `ÉÉÉÉ-HH` |
| `basket_size` | Kosárméret-kategória: `XS` (1–2 tétel), `S` (3–5), `M` (6–10), `L` (11–20), `XL` (21+) |
| `transactions` | Vásárlások (blokkok) száma |
| `items` | Eladott tételek száma |
| `revenue_huf` | Forgalom forintban, kerekítés nélkül |

A portál-koncepció ezen felül ezeket a dimenziókat használja (az eredeti adatban még nincsenek benne): `store_id`, áruháznév, formátum (Hipermarket / Szupermarket / Proxi), régió. Ezek tervezett, de még nem bekötött dimenziók: árrés, ügyfélcsoport, árindex.

## Mutatódefiníciók

Mindig ezekből a képletekből számolj, és a portál minden pontján ugyanazt a definíciót használd:

| Mutató | Képlet |
|---|---|
| Forgalom | `SUM(revenue_huf)` |
| Vásárlásszám | `SUM(transactions)` |
| Átlagkosár (Ft) | `SUM(revenue_huf) / SUM(transactions)` |
| Tétel / kosár | `SUM(items) / SUM(transactions)` |
| Átlagár / tétel (Ft) | `SUM(revenue_huf) / SUM(items)` |

Az összehasonlítás alapértelmezése: előző év azonos hónapja.

**Forgalombontás:** forgalom = vásárlásszám × tétel/kosár × átlagár. A változást LMDI-módszerrel (logaritmikus, sorrendfüggetlen) bontjuk három hatásra. A három hatás összege pontosan kiadja a teljes változást:
`L = (R1 − R0) / (ln R1 − ln R0)`, hatás = `L · ln(x1 / x0)`.

## A portál-koncepció tervezési elvei

- **Egy vászon, három mélység:** a fókuszszint-kapcsoló (Vezetői / Elemzői / Adat) ugyanazt a nézetet sűríti vagy bontja ki, nem külön oldalakra visz.
- **A részlet oldalpanelben nyílik meg**, a fő nézet közben megmarad.
- **Egy fő mutató vezérel mindent:** a kiválasztott KPI-ra vált a trend, a hőtérkép és a tábla is.
- **Előbb az összefoglaló, aztán a részlet.** Az állapotot ne csak szám jelezze, hanem címke is, ikonnal (például „▼ Figyelendő”).
- **Színek:**
  - A kosárméretek egyetlen kék skála fokozatai (sorrendi skála).
  - Változás: kék = pozitív, piros = negatív, a közép semleges szürke.
  - A státuszszínek (zöld, piros) csak a jelzésekhez vannak fenntartva, adatsorhoz nem használjuk őket.
- **Grafikonok:**
  - Soha nem használunk két y-tengelyt.
  - Ha a tengely nem nulláról indul, kiírjuk.
  - Minden grafikonhoz tartozik tooltip.
- **Téma és mobil:** világos és sötét téma CSS-tokenekkel. Mobilon (~400 px) se legyen vízszintes görgetés.
- **Mintaadat:** a mintaadatot mindig jelöljük („Mintaadat, nem valós számok”). Kitalált számot soha nem mutatunk valósként.

## Munkamódszer

- **Ellenőrzés:** HTML-módosítás után Playwrighttal nyisd meg az oldalt (`require('playwright')`, `chromium.launch()`), és nézd meg desktopon (világos és sötét téma) és 400 px szélességen. Figyelj a konzolhibákra és a vízszintes túlcsordulásra.
- **Ág:** fejlesztés a kijelölt `claude/...` ágon. Pull requestet csak kérésre nyiss.
- **Publikálás claude.ai artifactként:** a fájlból a `<!doctype>`, `<html>`, `<head>`, `<body>` és a charset/viewport meta sorokat ki kell venni. A repóban viszont a teljes HTML-dokumentum marad.
- A `[hidden]` attribútumhoz kell a `[hidden] { display: none !important; }` szabály, különben a `display: grid` stb. felülírja.

## Hivatkozások

- Kosárpulzus élő prototípus: https://claude.ai/artifact/MoK3YFUhXmiJc4pjXwncdq
- Az első munkamenet összefoglalója: `docs/munkamenet-2026-11-01-portal-koncepcio.md`

## Nyitott kérdések

- A claude.ai-os projekt context fájljai még nincsenek a repóban.
- Melyik portálirány legyen a végleges? Ezután jöhet a valós adat bekötése.
