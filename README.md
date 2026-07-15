# Spokojna Siła 🀄

Webowa aplikacja treningowa oparta na tai chi i qigong, zaprojektowana dla kobiet po 50. roku życia. Cała praktyka odbywa się **w pionie** — bez żadnej pozycji horyzontalnej — z wykorzystaniem trzech domowych „przyrządów": krzesła, schodów i ściany.

Statyczna strona bez procesu budowania: **`index.html` zawiera całą aplikację** (~57 KB). Zero frameworków, zero zależności JS, zero plików graficznych i dźwiękowych. Jedyny zasób zewnętrzny to fonty Google (Marcellus + Albert Sans) — bez internetu aplikacja działa w pełni, jedynie z czcionką systemową.

## Struktura projektu

```
├── index.html    – cała aplikacja (HTML + CSS + JS + dane 60 ćwiczeń)
├── favicon.svg   – ikona: enso z cynobrową pieczęcią
├── robots.txt
├── vercel.json   – nagłówki dla Vercel (opcjonalny)
├── .nojekyll     – wyłącza Jekyll na GitHub Pages
└── README.md
```

## Wdrożenie

**GitHub Pages**
1. Utwórz repozytorium i wgraj zawartość tego katalogu do gałęzi `main`.
2. Settings → Pages → Source: *Deploy from a branch* → `main` / `/ (root)`.
3. Strona pojawi się pod `https://<login>.github.io/<repo>/`.

**Vercel**
1. `vercel` w katalogu projektu (CLI) albo import repozytorium na vercel.com.
2. Framework Preset: *Other*, bez komendy build, Output Directory: `./`.
3. Gotowe — Vercel serwuje `index.html` z roota.

**Netlify Drop / dowolny hosting statyczny** — wystarczy przeciągnąć katalog; nic nie wymaga kompilacji.

**Lokalnie** — otwórz `index.html` w przeglądarce (dwuklik). Serwer nie jest potrzebny.

> Uwaga mobilna: przeglądarki odblokowują dźwięk po pierwszej interakcji — gong licznika zabrzmi po naciśnięciu **Start**. iOS Safari nie wspiera wibracji; tam sygnałem końca jest gong i wizualny puls.

## Zawartość merytoryczna

**60 ćwiczeń w trzech kategoriach po 20:**

| Kategoria | Charakter | Przykłady |
|---|---|---|
| Krzesło | siedzące tai chi + krzesło jako asekuracja | Ręce jak chmury, Podpieranie nieba, Wstawanie tai chi, Zhan Zhuang |
| Schody | praca kroku i przenoszenia ciężaru | Krok kota, Złoty kogut, Łuk i strzała, Schodzenie tyłem |
| Ściana | kalibracja postawy + opór | Wu Ji, Anioły przyścienne, Deska stojąca, Pchanie An |

**Priorytety zdrowotne 50+:** równowaga i prewencja upadków, postawa (kifoza piersiowa), siła nóg (sarkopenia), mobilność bioder i barków, nadgarstki, szyja, łydki, pośladki.

**Dwa poziomy** dla każdego ćwiczenia. Progresja w duchu tai chi: *wolniej znaczy trudniej* — poziom zaawansowany to zwykle dłuższa faza ruchu, głębsza pozycja i odjęta asekuracja, nie tylko więcej powtórzeń.

Każde ćwiczenie zawiera: dwufazowy diagram, instrukcję krok po kroku, wskazówkę oddechową, dawkowanie dla obu poziomów oraz przeciwwskazania (osteoporoza, kolana, nadciśnienie, bark, szyja, endoproteza biodra, zaburzenia równowagi, nadgarstki).

## Funkcje

- **Filtry:** kategoria, partia ciała (13 opcji), globalny przełącznik poziomu — wpływa na karty, szczegóły i licznik.
- **Diagramy generowane z danych** — każda figura to zestaw współrzędnych stawów renderowany do SVG w stylu pociągnięć pędzla. 120 póz, jeden silnik rysujący.
- **Licznik z odliczaniem** — czas startowy wyliczany automatycznie z dawki ćwiczenia dla aktywnego poziomu (serie sekund z 10-sekundowymi przerwami, minuty, oddechy ×9 s, powtórzenia ×6 s, „na stronę / na nogę" ×2; dolny próg 15 s). Korekta ±15 s, Pauza, Wyzeruj.
- **Gong misy na koniec** — trzy zanikające alikwoty (329,6 / 523,2 / 784 Hz) syntezowane przez Web Audio API + wibracja (Android) + wizualny puls. Bez plików audio.
- **Dostępność:** obsługa klawiatury, focus-visible, `aria-label` na diagramach, `prefers-reduced-motion`.

## Architektura `index.html`

```
├── <style>      – design system „tusz i papier”
├── <header>     – hero z enso i tytułem
├── <nav>        – filtry (kategoria / partia / poziom)
├── <main>       – siatka kart generowana z danych
├── <section>    – zasady bezpieczeństwa
├── modal        – szczegóły ćwiczenia + licznik
└── <script>
    ├── CATS / TAGS / CONTRA        – słowniki
    ├── SIT / STA / sp / st / shift – bazy póz i pomocniki
    ├── figure() / prop() / diagram() – silnik SVG
    ├── EX[60]                      – dane ćwiczeń
    ├── render() + filtry           – interfejs
    └── durFor() / bowl() / openModal() – licznik i gong
```

**Format ćwiczenia:**

```js
{ c:'k',                    // kategoria: k / s / w
  n:'Nazwa',
  t:['row','nog'],          // tagi partii ciała
  d:'Opis jednym zdaniem.',
  st:['Krok 1','Krok 2','Krok 3'],
  br:'Wskazówka oddechowa.',
  b:'6 powt. · wolno',      // dawka: początkujący
  a:'12 powt. · tempo 6 s', // dawka: zaawansowany
  ct:['knee'],              // klucze przeciwwskazań
  ps:[poza1, poza2] }       // dwie fazy ruchu
```

**Format pozy:** `h` głowa, `n` szyja/bark, `hp` biodro, `ra`/`la` ramiona `[[łokieć],[dłoń]]`, `rl`/`ll` nogi `[[kolano],[stopa]]`. Pomocniki `sp()` (siedząca) i `st()` (stojąca) nadpisują tylko zmienione kończyny; `shift(poza, dx, dy)` przesuwa całą figurę. Kadr fazy: 0–160 × 0–150.

**Dodanie ćwiczenia:** dopisz obiekt do `EX` — karta, diagram, filtry i licznik zbudują się same.

## Design system

| Token | Wartość | Rola |
|---|---|---|
| `--paper` | `#ECEDE5` | tło — chłodny papier |
| `--ink` | `#262A20` | tusz — tekst i figury |
| `--jade` / `--jade-deep` | `#69805F` / `#4C5E45` | jadeit — akcenty, aktywna kategoria |
| `--seal` | `#AC3B2A` | cynobrowa pieczęć — aktywny poziom, numer fazy, alarm |
| `--stone` | `#A5A79A` | kamień — rekwizyty na diagramach |

Typografia: **Marcellus** (nagłówki) + **Albert Sans** (tekst). Motyw przewodni: enso i estetyka malarstwa tuszem (shuǐmò).

## Zastrzeżenie

Aplikacja ma charakter edukacyjny i nie zastępuje porady medycznej. Przy osteoporozie, endoprotezach, nadciśnieniu lub zaburzeniach równowagi plan treningowy warto skonsultować z lekarzem lub fizjoterapeutą.
