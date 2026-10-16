# radioVET · web na jednu stránku

Návrh nového webu radiovet.cz pro MVDr. Laca Stehlíka, Ph.D. Jedna statická stránka: HTML, CSS a čistý JavaScript, bez knihoven, bez buildu a bez závislostí. Celý kód je v `index.html`.

Náhled online: https://jakubskupin.cz/foto/radiolog/web

**Cokoli budete k předání potřebovat, rádi doděláme.** Jiný formát, rozdělení do komponent, podklady navíc, úpravy. Stačí napsat přes Laca.

## Spuštění

Stránku otevírejte přes lokální server, ne dvojklikem. Masky v hero Chrome přes `file://` nenačte.

```bash
python3 -m http.server 8080
```

Pak http://localhost:8080.

## Co je v repozitáři

```
index.html          celá stránka (CSS v <style>, JS v <script> na konci, JSON-LD v hlavičce)
img/hero/           vrstvy hero scény (WebP)
img/web/            podpis, profilovka, pozadí sekce Otázky
fonts/              Montserrat a Bebas Neue jako WOFF2, jen latinka s češtinou a slovenštinou, licence OFL
og.jpg              náhled pro sdílení 1200 × 630
favicon.ico, icon-192.png, icon-512.png, apple-touch-icon.png, site.webmanifest
robots.txt          povoleno všem včetně AI asistentů, odkaz na sitemap
sitemap.xml         jedna adresa, https://www.radiovet.cz/
llms.txt            shrnutí služby pro AI asistenty (formát llmstxt.org)
```

Sekce stránky: navigace, hero (`#top`), pás údajů, Jak to funguje (`#jak`), Služby (`#sluzby`), Otázky (`#otazky`), Závěr (`#zaver`), patička. Obsah je v `<main>`.

## Vyhledávače, sdílení a AI asistenti

- `<title>`, popis, kanonická adresa `https://www.radiovet.cz/`, autor, barva motivu.
- Open Graph a Twitter karta s `og.jpg`.
- JSON-LD v hlavičce: `VeterinaryCare` (radioVET, služby, oblast ČR a SR, jazyky), `Person` (Laco, kvalifikace, LinkedIn a Zobrazkár), `WebSite` a `FAQPage`. Ve FAQ jsou jen otázky s vyplněnou odpovědí.
- `robots.txt` povoluje vyhledávače i AI asistenty (GPTBot, ClaudeBot, PerplexityBot, Google-Extended a další), ať kliniky najdou odpověď i přes ně.
- `llms.txt` shrnuje službu, postup, kvalifikaci a kontakt v jednom textu.
- Klíčové pojmy ve struktuře: „veterinární teleradiologie“ a „popisy RTG a CT“ v titulku a popisu, „Popisy RTG a CT pro veterinární kliniky“ v H1, „teleradiologie“, „na dálku“ a „veterinární kliniky“ v nadpisech sekcí, jméno a kvalifikace v textu i v JSON-LD.
- Hledanost oborových frází je v Česku a na Slovensku malá (našeptávač Googlu k nim nic nenabízí). Kliniky radioVET najdou hlavně podle jména, přes odkazy a přes odpovědi AI asistentů. Pomůže proto profil Google Firmy, odkazy ze Zobrazkáru, LinkedInu a od partnerů a samostatné podstránky (níž).
- Žádné cookies, žádná analytika, písma hostovaná lokálně: web nedělá žádný požadavek mimo vlastní doménu, cookie lišta proto není potřeba.

Lighthouse (15. 10. 2026, lokální server bez komprese): mobil výkon 93, přístupnost 100, osvědčené postupy 100, SEO 100; počítač 99, 100, 100, 100.

## Hero: jak efekt funguje

**Scéna.** Snímek psa je v původní orientaci, hlavou vlevo, jak se boční rentgen čte. Hero je pevná scéna 1440 × 800 px (`#stage`). Všechny vrstvy mají souřadnice v tomhle prostoru a funkce `heroFit()` celou scénu škáluje na šířku okna (`transform: scale`). Text vedle scény (`.hero-txt`) používá stejné měřítko přes proměnnou `--s`. Pod 760 px se text přesune nad scénu a zobrazí se pravých 880 px scény (Laco).

**Vrstvy (odspodu):** `st-film` Lacův rentgen psa ztlumený na 36 % → `st-shade` tmavý přechod pod textem vlevo → `st-laco` Laco (WebP s průhledností) → `st-shade2` přechod dole → `lens` hledáček → nález, štítky a jméno.

**Hledáček.** Rámeček 240 × 240 px jede za kurzorem s doběhem (lineární interpolace 0,25 na snímek v `tick()`). Uvnitř je kopie celé scény (`.lens-in`) posunutá o zápornou polohu hledáčku, takže hledáček funguje jako průhled. Na dotykových zařízeních jede za prstem, po zvednutí prstu zmizí. Blíž než 70 px k tlačítkům CTA se schová, aby nepřekážel kliknutí.

**Nález.** Když je hledáček v kruhu o poloměru 120 px kolem bodu (700, 598), rozsvítí se oranžový rámeček `#find`, štítek „Volná tekutina“ a popisek Lacovými slovy.

**Laco.** Nad hlavou a rameny (elipsa kolem bodu 1120, 245 a obdélník ramen ve funkci `onLaco`) se hledáček přichytí na 400 × 450 px se středem (1120, 285), pozadí ztmavne (`st-blk`) a ukáže se rentgen Laca (`laco-rentgen.webp`). Rentgen ořezávají dvě masky přes sebe (`mask-composite: intersect`, pro Safari `-webkit-mask-composite: source-in`): obrys postavy `laco-maska.webp` a svislý přechod, který ho od klíčních kostí dolů rozplyne do barevné fotky. Při druhém najetí lebka jednou mrkne (`laco-rentgen-mrk2.webp`, 60 ms náběh, 110 ms drží, 90 ms zpět).

**Výměna snímku psa.** Snímek je `img/hero/snimek.webp` (1600 × 1084, zobrazený přes celou scénu). Při výměně za jiný posuňte i nález: bod v `onFind`, `left` a `top` u `.st-find`, `.st-cap` a štítku `#tagFind`.

## Další interakce

- Kroky v Jak to funguje se rozsvěcují podle scrollu (`krUpd`).
- Bloky s třídou `.rv` vyjíždějí při vstupu do okna (`IntersectionObserver`).
- Otázky jsou nativní `<details>`, bez JavaScriptu.
- Při `prefers-reduced-motion` se animace vypnou.

## Napojení na dnešní radiovet.cz (Nuxt)

Dnešní web běží na Nuxtu a má podstránky `/teleradiologie/`, `/workshop/`, `/laco/`, `/kontakt/`, `/gdpr/` a `/obchodni-podminky/`.

- **Stránku do komponenty:** HTML do šablony, styly jako `scoped` nebo s prefixem tříd (krátké názvy jako `.nav`, `.btn`, `.wrap` by se mohly potkat se stávajícími styly), obrázky a písma do `public/`.
- **Skript jen v prohlížeči:** obsah `<script>` spustit v `onMounted` (nebo obalit `<ClientOnly>`), při odchodu ze stránky zrušit posluchače `resize`, `scroll`, `mousemove` a smyčku `requestAnimationFrame`.
- **Hlavičku přes `useHead`:** title, popis, kanonická adresa, Open Graph a JSON-LD z `index.html`.
- **GDPR a obchodní podmínky zůstávají,** patička na ně odkazuje.
- **Podstránky `/teleradiologie/` a `/workshop/` doporučujeme ponechat a rozšířit,** ne zrušit: každá odpovídá jinému hledání (popis snímků proti vzdělávání) a jedna stránka obě nepokryje. Úvodní stránka na ně může odkazovat ze sekce Služby.
- **Přesměrovat jen adresy, které opravdu zaniknou.** Návrh do `nuxt.config`:

```ts
routeRules: {
  '/teleradiologie/': { redirect: { to: '/#jak', statusCode: 301 } },
  '/workshop/': { redirect: { to: '/#sluzby', statusCode: 301 } },
  '/laco/': { redirect: { to: '/#otazky', statusCode: 301 } },
  '/kontakt/': { redirect: { to: '/#zaver', statusCode: 301 } },
}
```

## Premortem: co by se mohlo pokazit a co už je ošetřené

| Riziko | Co je hotové | Co zbývá na vás |
|---|---|---|
| Web po spuštění nikdo nenajde | bez `noindex`, kanonická adresa, sitemap, robots, JSON-LD | odeslat sitemap do Google Search Console a Bing Webmaster Tools |
| Staré odkazy skončí na 404 | seznam adres a přesměrování výš | podstránky ponechat, zaniklé adresy přesměrovat přes `routeRules` |
| Zmizí GDPR a obchodní podmínky | odkazy v patičce na stávající stránky | stránky ponechat |
| Pomalé načtení na mobilu | WebP, písma ořezaná na 37 kB, přednačtený hlavní obrázek, CLS 0 | na hostingu zapnout kompresi (brotli nebo gzip) a dlouhou cache pro `img/` a `fonts/` |
| Safari a iOS | masky s prefixem `-webkit-`, žádné experimentální API | jednou projít v Safari a na iPhonu (testovali jsme v Chrome na 1440, 1024, 768 a 375 px) |
| Kolize stylů a pád skriptu při SSR | vše v jednom souboru, bez knihoven a globálních závislostí | scoped styly, skript v `onMounted` |
| Cookie lišta kvůli písmům nebo analytice | písma lokálně, žádné cookies ani externí požadavky | souhlas řešit až při přidání analytiky |
| Šedá místa ve vyhledávání a v AI odpovědích | FAQ v JSON-LD jen s vyplněnými odpověďmi | po doplnění aktualizovat `llms.txt` a FAQ v JSON-LD |
| Odkaz se sdílí bez náhledu | `og.jpg` bez šedých míst, Open Graph a Twitter karta | po větší změně hero náhled vyrenderovat znovu |
| Přístupnost | `<main>`, popis hero pro čtečky, alt texty, větší cíle dotyku, `prefers-reduced-motion`; Lighthouse 100 | nic |
| Licence obrázků a písem | tabulka zdrojů níž, písma OFL | ověřit pozadí sekce Otázky |
| Tlačítka vedou jen na kotvy | seznam cílů níž | doplnit odkazy |
| Na web se vrátí telefonní číslo | číslo není na stránce, v JSON-LD (`telephone`) ani v `llms.txt`, kontakt začíná e-mailem | nepřidávat ho ani do hlavičky a patičky Nuxtu: Laco nechce hovory od majitelů zvířat, kliniky číslo mají v patě každé zprávy |

## Před spuštěním doplnit

Šedé texty (třída `.ph`) a hranaté závorky jsou místa, která doplní Laco:

- lhůta popisu, běžná a akutní (hero, Služby, Otázky, `llms.txt`)
- cena od a co v ní je (hero, Služby, Otázky, `llms.txt`)
- zástup a dovolená (Otázky)
- fakturace klinice, ověřit (Otázky)
- IČO (patička)
- cíl tlačítek „Poslat první snímek“, „Přihlásit“ a „Domluvit workshop“ (dnes kotvy na stránce)

Po doplnění přidat vyplněné otázky do `FAQPage` v JSON-LD a datum do `sitemap.xml`. Pokud web nepoběží na `https://www.radiovet.cz/`, změnit adresu v kanonickém odkazu, Open Graph, JSON-LD, `robots.txt` a `sitemap.xml`.

## Zdroje obrázků

| Soubor | Co to je | Původ |
|---|---|---|
| `img/hero/snimek.webp` | boční snímek břicha psa s volnou tekutinou | Lacův snímek z článku „Tuk, kamarád radiologa“, https://zobrazkar.cz/blog/tuk-kamarad-radiologa (17. 6. 2025) |
| `img/hero/laco-svetlo.webp` | Laco v haleně, výřez s průhledností | portrét vytvořený z Lacových fotografií (AI), barva a světlo dorovnané |
| `img/hero/laco-rentgen.webp`, `laco-rentgen-mrk2.webp` | rentgen Laca, klidný a s mrknutím | vytvořeno AI z téže fotky, ilustrace |
| `img/hero/laco-maska.webp` | maska obrysu pro rentgen | odvozeno z fotky |
| `img/hero/wordmark-radiovet-bily.png` | logo radioVET v bílé | podle loga radioVET |
| `img/web/profilovka-K15.webp` | profilovka | portrét vytvořený z Lacových fotografií (AI) |
| `img/web/podpis-stehlik.png` | podpis s hledáčkem | navržená značka, ne Lacův skutečný podpis |
| `img/web/pozadi-pater.webp` | ztlumený rentgen páteře za sekcí Otázky | ilustrační obrázek; před spuštěním ověřit licenci, nebo nahradit snímkem z databáze radioVET |
| `og.jpg` | náhled pro sdílení | snímek hero této stránky |

Rentgeny zvířat používáme jen Lacovy skutečné. Vygenerovaná anatomie dělá chyby, které radiolog pozná.

## Písma a barvy

- Bebas Neue (nadpisy) a Montserrat 400, 500, 600, 700 (text), obě pod licencí SIL Open Font License, hostované lokálně a ořezané na latinku s češtinou a slovenštinou.
- Oranžová `#DF6A2F`, tmavá `#050505`, text `#F4F4F2`, šedá `#B4AFAB`, světle šedá `#E6E6E6`. Proměnné jsou v `:root` na začátku stylů.
