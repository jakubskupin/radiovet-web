# radioVET · web na jednu stránku

Návrh nového webu radiovet.cz pro MVDr. Laca Stehlíka, Ph.D. Jedna statická stránka: HTML, CSS a čistý JavaScript, bez knihoven, bez buildu a bez závislostí. Celý kód je v `index.html`.

Náhled online: https://jakubskupin.cz/foto/radiolog/web

## Spuštění

Stránku otevírejte přes lokální server, ne dvojklikem. Masky v hero (`mask-image` s PNG) Chrome přes `file://` nenačte.

```bash
python3 -m http.server 8080
```

Pak http://localhost:8080.

## Struktura

```
index.html              celá stránka (CSS v <style>, JS v <script> na konci)
img/hero/               vrstvy hero scény
img/web/                podpis, profilovka, pozadí sekce Otázky
fonts/                  Montserrat a Bebas Neue jako WOFF2 + licence OFL
```

Sekce: navigace, hero (`#top`), pás údajů, Jak to funguje (`#jak`), Služby (`#sluzby`), Otázky (`#otazky`), Závěr (`#zaver`), patička.

## Hero: jak efekt funguje

**Scéna.** Hero je pevná scéna 1440 × 800 px (`#stage`). Všechny vrstvy mají souřadnice v tomhle prostoru a funkce `heroFit()` celou scénu škáluje na šířku okna (`transform: scale`). Text vedle scény (`.hero-txt`) používá stejné měřítko přes proměnnou `--s`. Pod 760 px se text přesune nad scénu a zobrazí se levých 880 px scény (Laco).

**Vrstvy (odspodu):** `st-film` Lacův rentgen psa ztlumený na 36 % → `st-shade` tmavý přechod pod textem vpravo → `st-laco` Laco (PNG s průhledností) → `st-shade2` přechod dole → `lens` hledáček → nález, štítky a jméno.

**Hledáček.** Rámeček 240 × 240 px jede za kurzorem s doběhem (lineární interpolace 0,25 na snímek v `tick()`). Uvnitř je kopie celé scény (`.lens-in`) posunutá o zápornou polohu hledáčku, takže hledáček funguje jako průhled. Na dotykových zařízeních jede za prstem, po zvednutí prstu zmizí. Blíž než 70 px k tlačítkům CTA se schová, aby nepřekážel kliknutí.

**Nález.** Když je hledáček v kruhu o poloměru 120 px kolem bodu (700, 598), rozsvítí se oranžový rámeček `#find`, štítek „Volná tekutina“ a popisek Lacovými slovy.

**Laco.** Nad hlavou a rameny (elipsa kolem bodu 320, 245 a obdélník ramen ve funkci `onLaco`) se hledáček přichytí na 400 × 450 px se středem (320, 285), pozadí ztmavne (`st-blk`) a ukáže se rentgen Laca (`laco-rentgen.jpg`). Rentgen ořezávají dvě masky přes sebe (`mask-composite: intersect`): obrys postavy `laco-maska.png` a svislý přechod, který ho od klíčních kostí dolů rozplyne do barevné fotky. Při druhém najetí lebka jednou mrkne (`laco-rentgen-mrk2.png`, 60 ms náběh, 110 ms drží, 90 ms zpět).

**Výměna snímku psa.** Snímek je `img/hero/snimek.jpg` (1600 × 1084, zobrazený přes celou scénu). Při výměně za jiný posuňte i nález: bod v `onFind`, `left` a `top` u `.st-find`, `.st-cap` a štítku `#tagFind`.

## Další interakce

- Kroky v Jak to funguje se rozsvěcují podle scrollu (`krUpd`).
- Bloky s třídou `.rv` vyjíždějí při vstupu do okna (`IntersectionObserver`).
- Otázky jsou nativní `<details>`, bez JavaScriptu.
- Při `prefers-reduced-motion` se animace vypnou.

## Před spuštěním doplnit

Šedé texty (třída `.ph`) a hranaté závorky jsou místa, která doplní Laco:

- telefon (hero, závěr, patička)
- lhůta popisu, běžná a akutní (hero, Služby, Otázky)
- cena od a co v ní je (hero, Služby, Otázky)
- zástup a dovolená (Otázky)
- fakturace klinice, ověřit (Otázky)
- IČO (patička)
- cíl tlačítek „Poslat první snímek“, „Přihlásit“ a „Domluvit workshop“ (dnes kotvy na stránce)

Technicky:

- smazat `<meta name="robots" content="noindex, nofollow">`
- doplnit favicon a obrázek pro sdílení (`og:image`)

## Zdroje obrázků

| Soubor | Co to je | Původ |
|---|---|---|
| `img/hero/snimek.jpg` | boční snímek břicha psa s volnou tekutinou | Lacův snímek z článku „Tuk, kamarád radiologa“, https://zobrazkar.cz/blog/tuk-kamarad-radiologa (17. 6. 2025) |
| `img/hero/laco-svetlo.png` | Laco v haleně, výřez s průhledností | portrét vytvořený z Lacových fotografií (AI), barva a světlo dorovnané |
| `img/hero/laco-rentgen.jpg`, `laco-rentgen-mrk2.png` | rentgen Laca, klidný a s mrknutím | vytvořeno AI z téže fotky, ilustrace |
| `img/hero/laco-maska.png` | maska obrysu pro rentgen | odvozeno z fotky |
| `img/hero/wordmark-radiovet-bily.png` | logo radioVET v bílé | podle loga radioVET |
| `img/web/profilovka-K15.jpg` | profilovka | portrét vytvořený z Lacových fotografií (AI) |
| `img/web/podpis-stehlik.png` | podpis s hledáčkem | navržená značka, ne Lacův skutečný podpis |
| `img/web/pozadi-pater.jpg` | ztlumený rentgen páteře za sekcí Otázky | ilustrační obrázek; před spuštěním ověřit licenci, nebo nahradit snímkem z databáze radioVET |

Rentgeny zvířat používáme jen Lacovy skutečné. Vygenerovaná anatomie dělá chyby, které radiolog pozná.

## Písma a barvy

- Bebas Neue (nadpisy) a Montserrat 400, 500, 600, 700 (text), obě pod licencí SIL Open Font License, hostované lokálně.
- Oranžová `#DF6A2F`, tmavá `#050505`, text `#F4F4F2`, šedá `#B4AFAB`, světle šedá `#E6E6E6`. Proměnné jsou v `:root` na začátku stylů.

## Dotazy

Návrh připravil Jakub Skupin (https://jakubskupin.cz). Na jedno kolo dotazů k předání odpovím, posílejte je přes Laca.
