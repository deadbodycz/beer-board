# Beer Board

Webová PWA aplikace pro sledování počtu vypitých nápojů pro skupinu lidí. Jeden soubor `index.html` bez závislostí.

## Architektura

Celá aplikace žije v `index.html` — HTML, CSS i JS inline. Žádný build tool, žádné npm balíčky.

**Soubory:**
- `index.html` — celá aplikace
- `manifest.json` — PWA manifest
- `sw.js` — service worker (cache-first, offline podpora)
- `icon.svg` — ikona aplikace

## Datový model

```js
state = {
  people: [{ id: string, name: string, drinks: { [drinkId]: number } }],
  drinks: [{ id, name, price }]   // typy nápojů s cenou v Kč
}
```

Stav se ukládá do `localStorage` pod klíčem `beerboard_v2`.

## Klíčové funkce v JS

| Funkce | Popis |
|---|---|
| `render()` | Překreslí celé UI ze state (volá se po strukturálních změnách) |
| `increment(personId, drinkId)` | +1 nápoj — volá pouze `updateDrinkRow` + `updateCardFooter` + `updateSummary` (bez full render) |
| `decrement(personId, drinkId)` | -1 nápoj (min 0) |
| `addPerson(name)` / `removePerson(id)` | Správa osob |
| `addDrink(name, price)` / `removeDrink(id)` | Správa typů nápojů |
| `resetAll()` | Smaže osoby a počty, zachová typy nápojů |
| `updateSummary()` | Aktualizuje souhrn v headeru |
| `saveState()` / `loadState()` | localStorage persistence |

## Design

- **Dark mode** jako výchozí, CSS proměnné v `:root`
- Klíčové barvy: `--bg: #0f0f13`, `--surface: #1e1e2e`, `--accent: #f59e0b`, `--green: #22c55e`
- Mobile-first, responzivní grid: 1 → 2 → 3 → 4 sloupce
- Fixní spodní lišta pro přidání osoby (`position: fixed; bottom: 0`) — používá `env(safe-area-inset-bottom)` pro iPhone

## Spuštění / PWA instalace

Service worker vyžaduje HTTP/HTTPS — nelze spustit přes `file://`.

**Lokálně:** VS Code Live Server → `http://localhost:5500`  
**Produkce:** https://deadbodycz.github.io/beer-board

---

## 🎯 KONTEXT PROJEKTU

Webová PWA aplikace pro sledování počtu vypitých nápojů pro skupinu lidí.

**URL:** https://deadbodycz.github.io/beer-board  
**Cílová skupina:** Návštěvníci hospod a restaurací, kteří chtějí mít přehled o tom, kolik toho vypili.  
**Klíčový princip:** Mobile-first. Uživatelé jsou v hospodě s telefonem v ruce.

## 🚀 GIT WORKFLOW

**Po každé změně kódu vždy automaticky:**
udělej zálohu

```bash
git add -A
git commit -m "stručný popis změny v češtině"
git push
```

**Pravidla:**
- **NIKDY nečekej na pokyn k pushnutí** — push prováděj automaticky po dokončení každé úpravy
- Commit message piš česky, stručně a výstižně
- Po úspěšném push oznam uživateli, že změny jsou na GitHubu

## 📝 OBECNÉ POKYNY

- **Jazyk UI:** čeština, české chybové hlášky a toasty
- **Datum:** DD.MM.YYYY (česká konvence)
- **Timezone:** Europe/Prague
- **Pokud narazíš na nejasnost:** rozhodni se sám a pokračuj
