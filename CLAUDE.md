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
  drinks: [{ id, name, price }],   // typy nápojů s cenou v Kč
  pubs: [{ id, name, drinks: [{ id, name, price }] }]  // uložené hospody s vlastními ceníky
}
```

Stav se ukládá do `localStorage` pod klíčem `beerboard_v2`.  
API klíč se ukládá zvlášť pod klíčem `beerboard_apikey`.  
Migrace: při načtení starého stavu bez `pubs` se přidá `pubs: []` automaticky.

## Klíčové funkce v JS

| Funkce | Popis |
|---|---|
| `render()` | Překreslí celé UI ze state (volá se po strukturálních změnách) |
| `increment(personId, drinkId)` | +1 nápoj — volá pouze `updateDrinkRow` + `updateCardFooter` + `updateSummary` (bez full render) |
| `decrement(personId, drinkId)` | -1 nápoj (min 0) |
| `addPerson(name)` / `removePerson(id)` | Správa osob |
| `addDrink(name, price)` / `removeDrink(id)` | Správa typů nápojů na Boardu |
| `updateDrink(id, name, price)` | Inline editace existujícího nápoje |
| `resetAll()` | Smaže osoby a počty, zachová typy nápojů i hospody |
| `updateSummary()` | Aktualizuje souhrn v headeru |
| `saveState()` / `loadState()` | localStorage persistence |
| `addPub(name)` / `removePub(id)` | Správa hospod |
| `addDrinkToPub(pubId, name, price)` / `removeDrinkFromPub(pubId, drinkId)` | Správa nápojů v hospodě |
| `activatePub(pubId)` | Nahradí `state.drinks` nápoji z hospody, přepne na Board, zobrazí toast |
| `renderPubs()` | Překreslí seznam hospod v tab Hospoda |
| `showTab(name)` | Přepíná mezi taby (board / hospoda / settings) |
| `showToast(msg)` | Zobrazí dočasnou notifikaci nad add barem |
| `analyzeMenuImage(file)` | Claude Vision API — rozpozná nápoje z fotky ceníku |

## Navigace (taby)

Aplikace má tři záložky pod headerem:
- **Board** — hlavní pohled s osobami, nápoji a počítadly
- **Hospoda** — správa uložených hospod a jejich ceníků
- **Nastavení** — Claude API klíč

Tab switching: `showTab(name)` — přidává/odebírá class `active` na `.tab` a `.tab-panel`, přepíná `body.board-active` (ovládá viditelnost fixního add baru).

## AI skenování ceníku

- Endpoint: `https://api.anthropic.com/v1/messages`
- Model: `claude-haiku-4-5-20251001`
- Obrázek komprimován na max 1500px, JPEG 0.82 před odesláním
- Po rozpoznání zobrazí modal s checkboxy + **select „Uložit do"**: Board nebo konkrétní hospoda ze `state.pubs`

## Design

- **Dark mode** jako výchozí, CSS proměnné v `:root`
- Klíčové barvy: `--bg: #0f0f13`, `--surface: #1e1e2e`, `--accent: #f59e0b`, `--green: #22c55e`
- Mobile-first, responzivní grid: 1 → 2 → 3 → 4 sloupce
- Fixní spodní lišta pro přidání osoby — zobrazuje se jen na tab Board (`body.board-active`)
- Používá `env(safe-area-inset-bottom)` pro iPhone notch

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
