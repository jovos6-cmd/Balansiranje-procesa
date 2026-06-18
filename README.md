# TECOR — Balansiranje operacija

Lean time-study i balansiranje linije za rad na podu. Jedan PWA fajl, radi offline, instalira se kao prava aplikacija. Sva merenja, analiza, WIP, norme i izvoz (PDF / Excel / CSV / JSON) su lokalni na uređaju — bez servera, bez naloga, bez troškova.

---

## Šta radi

- **Snimanje** — svaki zahvat ima START/STOP, kroz više iteracija → prosek po zahvatu. CT operacije = zbir prosečnih zahvata.
- **Kategorije** — VA (zelena), SP / support (narandžasta), NVA (crvena). Pripremne operacije = ukupno vreme / broj komada.
- **GAP** — razlika prosečnih CT između operacija (npr. Op1=10 s, Op2=14 s → gap 4 s). Dva lica: razlika prema bottleneck-u (Yamazumi, efikasnost) i razlika između susednih operacija (gomilanje zaliha).
- **WIP** — gde je uzvodna brža od nizvodne, zaliha raste = (brzina_gore − brzina_dole). Queue time po Little-ovom zakonu: WIP / protok.
- **Balansiranje** — identifikacija uskog grla, podopterećenih operacija i operacija iznad takta + predlozi redukcije NVA unutar stanice (bez premeštanja zahvata).
- **Norme & scenariji** — `CT = VA + SP + NVA·(1−nva%) + GAP·(1−gap%) + Prep`
  - **Realna (as-is)**: NVA i GAP u punom iznosu.
  - **Best case**: NVA potpuno neutralisan, GAP na rezidualni prag (default 5% → ~95% efikasnosti).
  - **Scenario**: slajderi NVA i GAP po stepenima 10 / 20 / 50 / 70 / 100%.
- **Pogledi** — Redovne / Pripremne / Kombinovano (prep alociran na prethodnu/narednu operaciju).
- **Jezik** — srpski (latinica) / engleski, prekidač u zaglavlju.
- **Sigurnost rada** — IndexedDB autosave (oporavak posle pada), upozorenje pri osvežavanju/zatvaranju ako nije izvezeno.

---

## Struktura fajlova

```
tecor-balansiranje/
├── index.html              cela aplikacija (HTML + CSS + JS)
├── manifest.webmanifest    PWA manifest (instalacija)
├── sw.js                   service worker (offline)
└── assets/
    ├── logo.png            TECOR logo (providna pozadina)
    ├── icon-192.png        ikonica
    ├── icon-512.png        ikonica
    ├── icon-maskable-512.png
    └── favicon.png
```

---

## Postavljanje (deploy) — sve besplatno

### A) Netlify Drop (najbrže, sa telefona)
1. Otvori **app.netlify.com/drop**.
2. Prevuci **ceo folder `tecor-balansiranje`** u prozor (ne pojedinačne fajlove).
3. Dobiješ link tipa `nesto.netlify.app`. Otvori ga na telefonu → meni pregledača → **Dodaj na početni ekran** → instalirano kao aplikacija.

### B) GitHub Pages
1. Napravi repo (npr. `tecor-balansiranje`) i ubaci sve fajlove iz foldera u koren.
2. **Settings → Pages → Branch: `main` / `(root)` → Save**.
3. Aplikacija je na `https://<korisnik>.github.io/tecor-balansiranje/`.

### C) GitHub Pages + auto-build (Actions, opciono)
Ako kasnije pređeš na modularni izvor, dodaj `.github/workflows/deploy.yml` koji na svaki push gradi i objavljuje na Pages. Za sadašnji jednofajlni app nije potrebno — dovoljno je B).

---

## Offline

Posle prvog otvaranja uz internet, service worker kešira aplikaciju i biblioteke za izvoz (jsPDF, SheetJS), pa sve radi i bez mreže. Sledeća iteracija: spakovati te biblioteke lokalno za 100% offline od prvog pokretanja.

---

## Brzi test na podu (predlog)

1. **Sesija** → unesi klijenta, proces, takt (direktno ili vreme/količina) i trajanje smene.
2. **Podešavanje** → dodaj 2–3 operacije sa po nekoliko zahvata, označi VA/SP/NVA. Po potrebi jedna pripremna.
3. **Snimanje** → izaberi operaciju, START/STOP po zahvatu, sačuvaj iteraciju; ponovi 5–10 puta.
4. **Analiza** → pogledaj Yamazumi, bottleneck, GAP, WIP, pa pomeraj scenario slajdere.
5. **Izvoz** → PDF za klijenta, Excel/CSV za dalju obradu, JSON da nastaviš kasnije.

---

## Napomene o metodologiji

- Efikasnost linije = ukupan rad / (broj operacija × CT uskog grla). Cilj 80–95%, vrhunski ~95%.
- Balansni gubitak = 1 − efikasnost.
- Preklapanje merenja je nemoguće tokom snimanja jer je u datom trenutku aktivan samo jedan zahvat (jedna štoperica).
- Sva vremena se čuvaju u milisekundama, prikazuju na 0.1 s.

— TECOR Lean & Kaizen consulting
