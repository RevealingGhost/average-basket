# Munkamenet-összefoglaló: elemzői portál koncepció

**Dátum:** 2026. november 1.
**Repó:** `RevealingGhost/average-basket`
**Ág:** `claude/analyst-portal-concept-dgr5k3`
**Élő prototípus (artifact):** https://claude.ai/artifact/MoK3YFUhXmiJc4pjXwncdq

## A kérés

Egy gyors tesztként mutassuk meg, hogyan nézne ki egy ideális, trendeknek megfelelő, intuitív és high-tech elemzői portál, ahol a részletekkel és a high-level dashboardokkal is ugyanott lehet dolgozni.

## Kiindulópont

A repóban már volt egy átlagkosár-dashboard (`index.html`) ezekkel:

- **Kosárméret-szegmensek:** XS, S, M, L, XL.
- **Mutatók:** forgalom, tranzakció, átlagkosár, tétel/tranzakció, átlagár.
- **Háromhatásos forgalombontás.**

A dashboardban felsorolt hiányzó dimenziók a következők voltak: boltformátum, régió, ügyfélcsoport és árrés. A koncepció erre épül.

## Az elkészült prototípus: Kosárpulzus

Fájl: `portal-koncepcio/index.html`. Önálló HTML, böngészőben közvetlenül megnyitható, külső függőség nélkül (csak Google Fonts).

**Minden szám mintaadat:** 20 áruház, 3 formátum, 5 régió, 36 hónap és 5 kosárméret, generált értékekkel.

### Az alapelv: egy vászon, három mélység

Egy **fókuszszint-kapcsoló** (gyorsbillentyűk: 1 / 2 / 3) ugyanazt a nézetet sűríti vagy bontja ki:

| Szint | Mit mutat |
|---|---|
| Vezetői | KPI-csempék, automatikus összefoglaló, 12 havi trend, forgalmi híd, kosárméret-mix |
| Elemzői | az előzőek, plusz régió × kosárméret hőtérkép és rendezhető áruháztábla |
| Adat | az előzőek, plusz nyers ténytábla-sorok, mutatódefiníciók és a nézet mögötti, élőben generált SQL |

### Fő funkciók

- **Részletező oldalpanel:**
  - Megnyílik, ha boltra, hőtérkép-cellára, mixszeletre vagy hídelemre kattintasz.
  - A fő nézet közben megmarad.
  - Tartalma:
    - 5 mutató az előző évhez képest,
    - trend és kosármix,
    - hatásbontás,
    - jegyzet,
    - „Szűrés erre” gomb.
- **Egy fő mutató vezérel mindent:**
  - Ha egy KPI-csempére kattintasz, a trend, a hőtérkép és a tábla is arra a mutatóra vált.
  - Ha a trendben egy hónapra kattintasz, a teljes portál arra az időszakra vált.
- **Globális szűrősor:** időszak, formátum, régió és kosárméret. A morzsamenü a lefúrás útját mutatja, és vissza is lehet lépni rajta.
- **Parancspaletta (Ctrl+K vagy „/”):**
  - Kereshetsz benne boltra, régióra, formátumra és mutatóra.
  - Természetes nyelvű kérdést is írhatsz, például: „Miért esett Szeged?”.
- **Automatikus összefoglaló:**
  - Az aktuális szűrésből generált mondatok.
  - Kiemeli a forgalom fő hajtóerejét, az XL kosarak arányának változását és a kilógó boltokat.
- **Anomáliajelzés:** a „Figyelendő” és „Kiemelkedő” címke annak alapján dől el, mennyire tér el egy bolt vásárlásszám-változása a többi bolt átlagától (z-score). A riasztásszám az oldalsávon jelenik meg.
- **Szemantikus réteg:** minden mutatónak egy definíciója és képlete van, és ugyanez számol a portál minden pontján.
- **Kinézet:**
  - Világos és sötét téma.
  - Mobilnézet.
  - Billentyűzettel is kezelhető.

### Ami csak szimulált

- A kérdésekre adott válasz szabályalapú, nincs mögötte valódi nyelvi feldolgozás.
- A „Riasztás beállítása” és a „Rögzítés a táblámra” gomb csak egy értesítést mutat, valójában nem csinál semmit.
- A jegyzet csak a saját böngésző helyi tárolójában marad meg.

## Technikai jegyzetek

- **Forgalombontás:** LMDI (logaritmikus, sorrendfüggetlen) módszer. A forgalom = vásárlásszám × tétel/kosár × átlagár, és a három hatás összege pontosan kiadja a teljes változást.
- **Színek:**
  - A kosárméretek egyetlen kék skála fokozatai.
  - A változások színe kék, ha pozitív, és piros, ha negatív.
  - A státuszszínek (zöld, piros) csak a jelzésekhez vannak fenntartva.
- **Ellenőrzés:** Playwrighttal készítettem képernyőképeket desktopon világos és sötét témában, valamint mobilon. Konzolhiba nem volt, vízszintes görgetés sem.

## Git-történet

1. A commit lokálisan elkészült, de az első push 403-as hibával elutasítva visszajött, mert a Claude GitHub App nem volt telepítve a repóra.
2. Az App telepítése után a push sikerült.
3. Pull request nem készült, mert a koncepció még átnézésre vár.

## Javasolt következő lépések

1. **Visszajelzés az irányról:** a fókuszszint-koncepció, az oldalpanel és a parancspaletta közül mi működik, és mi nem.
2. **Valós adatstruktúra bekötése:** a meglévő dashboard nyers táblájának (`basket_size`, `items`, `revenue_huf`, `transactions`, `period`) kiegészítése bolt-, formátum- és régióoszlopokkal.
3. **Hiányzó dimenziók:** árrés kosárméretenként, ügyfélcsoport és árindex.
4. **Egy `CLAUDE.md` a repóba**, hogy a következő munkamenetek automatikusan megkapják ezt a kontextust.
5. **Pull request nyitása**, ha a koncepció véglegesnek tekinthető.

## Nyitott kérdés

A claude.ai-os projekt context fájljai ebből a munkamenetből nem érhetők el. Ha kellenek, töltsd fel vagy másold be őket, és bekerülnek a repóba.
