# ZXRTOS

FreeRTOS ZX Spectrum 128K kompiuteryje **vietoj originalaus ROM**. Įjungus
iškart startuoja FreeRTOS; task'ai naudoja 128K bankus per 0x7FFD, vėliau
appsai bus kraunami nuo juostos ir SD, o vietoj BASIC veiks ZX Lua REPL.

ROM 0 - branduolys, ROM 1 - resursų saugykla (šriftas, programos, bankų
moduliai), iš kurios įkrovos metu kopijuojama į RAM ir RAM bankus. Planas (21 etapas) - `../inbox.txt`, būsena - `CHANGELOG.txt`.

Versija **1.0.0**: baigti visi plano etapai 0-20 (ant geležies neišbandyta - tik libzxemu emuliatoriuje).

| Etapas | Kas | Patikra |
|---|---|---|
| 0 | z88dk + zsdcc, FreeRTOS 11.1.0 Z80 portas | kompiliuojasi |
| 1 | savas crt0: reset, IM1 per RAM vektorių, NMI | `make check-s1` |
| 2 | 8x8 šriftas su LT raidėmis, konsolė su langais, klaviatūra | `make check-s2` |
| 3 | FreeRTOS, tick 50 Hz iš ULA, 3 task'ai | `make check-s3` |
| 4 | ekranas kaip servisas: mutex'as, langai su antraštėmis | `make check-s4`, `check-s4n` (be mutex'o - turi nepavykti) |
| 5 | bankų sluoksnis: select, alokatorius, read / write, 6 x 16 KB testas | `make check-s5`, `check-s5n` (be užrakto - nuo 6 etapo nereikia) |
| 6 | bankas task'o kontekste, xTaskCreateBanked: stekas ir duomenys savo banke | `make check-s6`, `check-s6n` (be bankų - turi nepavykti) |
| 7 | kodas bankuose: moduliai 0xC000, ROM 1 -> bankai įkrovos metu, module_start | `make check-s7`, `check-s7n` (be bankų - turi nepavykti) |
| 8 | sistemos konsolė (ps, banks, free, kill, drv), klaidų kabliai, tvarkyklių modelis | `make check-s8`, `check-s8b`, `-s8k`, `-s8f`, `-s8s`, `-s8a` |
| 9 | klaviatūra (matrica, atšokimai, kartojimas, LT raidės) ir Kempston - bendra įvykių eilė | `make check-s9`, `check-s9r`, `check-s9j` (be Kempston) |
| 10 | AY grotuvas fone (banko tvarkyklė, 3 kanalai, triukšmas, gaubtinė), beeper signalai | `make check-s10`, `check-s10s` (+ WAV garso patikra) |
| 11 | I2C bit-bang prievadu 0x3F, LM75 temperatūra kas sekundę, komandos temp ir i2c | `make check-s11`, `check-s11s` (clock stretching), `check-s11n` (be sąsajos) |
| 12 | SPI bit-bang tuo pačiu prievadu, SD kortelė, Petit FatFs: sd, dir, type, log | `make check-s12`, `-s12w` (rašymas), `-s12d` (pakatalogis), `-s12s` (SDSC), `-s12n` (be kortelės) |
| 13 | moduliai iš SD kortelės; FT81x EVE video per SPI: 24 bitų testinis vaizdas iš task'o | `make check-s13` (+ FT81x PNG patikra), `-s13n` (be FT81x), `-s13a` (AY iš SD groja) |
| 14 | VS1053 MP3 iš SD srauto task'u; 2 SPI variantai: bit-bang (16 kbps) ir aparatinis registras 0xBF (128 kbps) | `make check-s14`, `check-s14h` (+ WAV be trūkių), `-s14b` (bit-bang su 128 kbps - turi trūkinėti) |
| 15 | savas juostos loaderis: standartinis blokas nuo .tap į banką, tick'ai pataisomi | `make check-s15` (16 KB realiu laiku), `-s15b` (sugadintas blokas) |
| 16 | appsai nuo juostos (`load`) ir SD (`run`), `unload`, `apps`; šablonas `apps/hello.c` | `make check-s16` (abu kartu), `-s16u` (unload), `-s16n` (ne appsai) |
| 17 | ZX Lua branduolys: leksikas, kompiliatorius į baitkodą, VM; skriptai banko task'e | `make check-s17` (17 testų ZX iš SD), `make lua-check` (PC), `make lua-ref` (Lua 5.4) |
| 18 | ZX Lua lentelės, mark-and-sweep GC, REPL; sistema įsijungia į REPL (LUA.SYS iš SD) | `make check-s18`, `-s18n` (be SD kortelės) |
| 19 | ZX Lua sistemos moduliai, skriptai kaip task'ai savo bankuose (`task.run`), AUTORUN.LUA | `make check-s19` (task.run), `-s19g` (temp + FT81x + AY vienu metu) |
| 20 | išleidimas: ROM failai EPROM'ui, SD kortelė, I/O plokštės aprašas, stabilumo testas | `make release`, `make soak` (3 val. ZX laiko) |

![19 etapas: ZX Lua task.run](shots/etapas19.png) ![18 etapas: ZX Lua REPL](shots/etapas18.png) ![17 etapas: ZX Lua](shots/etapas17.png) ![16 etapas: appsai](shots/etapas16.png) ![15 etapas: juosta](shots/etapas15.png) ![14 etapas: MP3 128 kbps](shots/etapas14h.png) ![14 etapas: MP3 16 kbps](shots/etapas14.png)

![13 etapas: FT81x](shots/etapas13-ft81x.png)

![13 etapas: konsolė](shots/etapas13.png) ![12 etapas: SD kortelė](shots/etapas12.png) ![11 etapas: LM75](shots/etapas11.png) ![10 etapas: muzika fone](shots/etapas10.png) ![9 etapas: events](shots/etapas9.png) ![8 etapas: ps](shots/etapas8.png) ![8 etapas: banks, beep, drv](shots/etapas8b.png)
![8 etapas: steko perpildymas](shots/etapas8s.png) ![7 etapas](shots/etapas7.png)

## Išleidimas (20 etapas)

`make release` -> `dist/zxrtos-VERSIJA-release.tar.gz`: `zxrtos-128.rom`
(32 KB, 27C256 - ROM 0 + ROM 1 su krovėju), `zxrtos-0.rom` / `zxrtos-1.rom`,
SD kortelės atvaizdas (`LUA.SYS`, tvarkyklės, pavyzdžiai), aprašai.
Naudojimas - `docs/RELEASE.md`, išorinė I/O plokštė - `docs/HARDWARE.md`
(prievadai, dekodavimas su A7 - dėl 0xBF, lygių keitimas, maitinimas;
ant geležies neišbandyta).

Užimtumas: ROM 0 - 16 384 / 16 384 B (FreeRTOS tasks 5,5 KB, queue 3,5 KB,
konsolė 1,8 KB, heap_4 1,4 KB ...; `make stats`), ROM 1 (krovėjas) - ~10 KB
iš 16 KB.

Stabilumo testas `make soak` (`scripts/soak.lua` kaip AUTORUN.LUA): be
pertraukos `task.run("WORK.LUA")` (skriptas savo banke: lentelės, eilutės,
GC, rezultato patikra), bankų atlaisvinimas, AY muzika fone, LM75 kas
sekundę; pabaigoje - ciklų skaičius, klaidos 0, Lua atmintis.

## ZX Lua sistemos moduliai (19 etapas)

| Modulis | Funkcijos |
|---|---|
| `screen` | `at(x, y, s)` (žymeklis nepajuda), `cls()` |
| `key` | `get()` - klavišo kodas arba nil (nelaukia) |
| `joy` | `get()` - Kempston bitai arba nil |
| `task` | `sleep(t)`, `ticks()`, `run(failas)` - skriptas iš SD savo banke (grąžina banką) |
| `temp` | `get()` - LM75, dešimtosios °C (nil - nėra) |
| `ay` | `play(n [, kartoti])`, `stop()` |
| `gfx` | `cls(0xRGB)`, `text(x, y, 0xRGB, s)`, `show()` - FT81x |
| `bank` | `free()` - laisvų bankų skaičius |

Įgyvendinta nepilnai (vieta ir bankai): `snd` (VS1053), `sd` (tik skriptų krovimas
`task.run` / AUTORUN.LUA), `app`, skriptų įrašymas į juostą / SD - nėra.

Kaip veikia:

- **Tarpininkas „sys“** (`src/sys/luagw.h`, ne banko task'as): Lua task'ai
  banke negali kviesti banko tvarkyklių nei `bank_read` / `bank_write`
  (stekas - savo banke), todėl užklausą (tvarkyklė, ioctl / read / write,
  failo krovimas, naujas task'as, banko atlaisvinimas) perduoda per
  fiksuotos zonos bloką ir semaforus.
- **`task.run`**: tarpininkas skiria banką, nukopijuoja ZX Lua banko dalį iš
  šablono (banke 7), įrašo skriptą iš SD (`LUA_SRC`) ir sukuria banko
  task'ą; baigęs skriptas prašo atlaisvinti banką.
- **Tvarkyklės**: krovėjas (18b) įkelia `MOD/*.ZXM` į bankus, ROM 0
  (`boot_img_t.mods`) bankus pažymi skirtais, Lua sistema juos paleidžia.
- **AUTORUN.LUA** (jei yra SD) vykdomas prieš REPL.
- 19 etapo Lua banko išdėstymas (`src/sys/luasys.h`): 0xC000 banko kodo
  dalis (biblioteka su `ZL_LIB_SMALL`, GC, lentelės, moduliai, REPL), 0xE700
  Lua blokas, 0xF850 skripto tekstas (768 B), viršuje stekas; skriptui
  ~3 KB Lua atminties.

Bankai (6): sd, šablonas, po vieną kiekvienai SD tvarkyklei, REPL, skriptai.
19g: sd, šablonas, AY, I2C, FT81x, REPL (AUTORUN.LUA - REPL task'e).

## ZX Lua REPL (18 etapas)

Sistema įsijungia tiesiai į ZX Lua REPL. ROM 1 - šriftas, krovėjas (`18b`)
ir SD tvarkyklė; krovėjas iš SD kortelės įkelia `LUA.SYS` (antraštė „ZL“,
programa 0x5D00, ZX Lua banko dalis) į bankus 3 / 7, palieka aprašą
įkrovos steko dugne (0xA400) ir šoka į 0; ROM 0 branduolys programą
nukopijuoja iš bankų (Donato sprendimas - Lua sistema iš SD). Be kortelės -
pranešimas.

REPL: eilutė pirma bandoma kaip `return eilutė` (išraiškos reikšmė
spausdinama), tada kaip sakiniai; nebaigta konstrukcija (`function f()`)
- tęsinys su `>>`; klaida - `eilutė N: ...`. Klaviatūra: `{` `}` `[` `]`
`~` `|` `\` - SYMBOL + F G Y U A S D.

Kalba (18 etapas): lentelės (`{1, 2, x = 3, [k] = v}`, `t.k`, `t[k]`,
`#t`, `o:m()`, `function t.f()` / `t:m()`), `for k, v in pairs(t)` /
`ipairs(t)`, kelių tikslų priskyrimas su indeksais; bibliotekos `table`
(insert, remove, concat), `string` (len, sub, rep, byte, char, upper,
lower - tik `string.f(s, ...)`), `math` (abs, max, min, random), `error`,
`collectgarbage`. Sveikieji - **16 bitų** (planas leidžia 16 / 32): 3 B
reikšmė, mažesnis ir greitesnis Z80 kodas; perpildymas apgaubiamas
(`ZL_INT32` - 32 bitai). Upvalue'ų nėra.

Atmintis: mark-and-sweep GC bloko ribose (`src/lua/zl_gc.c`): blokai su
antrašte, pirmo tinkamo paieška nuo pirmo laisvo bloko, gretimų sujungimas;
šaknys - globalūs, VM stekas, kvietimų rėmai; kompiliuojant GC
išjungtas, laikini buferiai grąžinami iš karto.

**ZX Lua padalinta** (fiksuota zona netalpina ~22 KB): leksikas,
kompiliatorius, VM - programoje (0x5D00...), biblioteka, GC, lentelės -
kiekvieno Lua task'o banke nuo 0xC000 (zsdcc `--codeseg CODE_LUAB`,
`app_crt_lua.o`, `tools/mkrom.py luab`); Lua būsena ir heap - nuo
`ZL_STATE_ADDR` (0xE000). REPL banke: ~5 KB Lua atminties.

| Dalis (Z80) | Dydis |
|---|---|
| programa (Lua sistema, 18 etapas) | 17 532 B iš 18 176 |
| ZX Lua banko dalis | 8 015 B iš 8 192 |
| Lua heap REPL banke (po paleidimo) | ~5,1 KB |

**Rasta (zsdcc / z88dk):** (1) `-SO3` peephole sugadino 16 bitų palyginimą
`zl_alloc` (dingo `ld a,(ix-2)`) - heap rašė už bloko; ZX Lua dabar `-SO2`
(+1,7 % kodo). ROM 0 ir programos lieka `-SO3` (anksčiau rasta `win_clear`
klaida - tos pačios rūšies) - rizika, žr. CHANGELOG. (2) 16 bitų ženklinė
liekana neigiamam dalinamajam neteisinga (`-7 % 3 = 1`) - liekana
skaičiuojama `x - q*y`. Abi rastos emuliatoriuje sekant instrukcijas.

## ZX Lua (17 etapas)

Lua poaibio interpretatorius `src/lua/` (C, portabilus: ZXRTOS - zsdcc, PC -
gcc): leksikas (`zl_lex.c`), vieno praėjimo kompiliatorius į steko baitkodą
(`zl_comp.c`, pirmumas kaip Lua 5.4), VM, atmintis, klaidos (`zl_vm.c`),
`print`, `type`, `tostring`, `tonumber` (`zl_lib.c`).

Kalba: 32 bitų sveikieji (`/` ir `//` - dalyba žemyn, `%` - kaip Lua),
bitų operacijos, eilutės (`..`, `#`, palyginimas, `"..."`, `'...'`, `[[...]]`),
`nil`, `true` / `false`, `local`, globalūs, funkcijos (rekursija per globalų
vardą), `if` / `elseif` / `else`, `while`, `repeat`, skaitinis `for`, `break`,
`return`, `and` / `or` / `not`, komentarai. Klaidos - `eilutė N: ...`.

Skirtumai nuo Lua 5.4 (17 etapas): be float; funkcija grąžina vieną reikšmę
(be `return` - `nil`, Lua 5.4 - nulis reikšmių); upvalue'ai, lentelės ir GC -
18 etape.

Architektūra (19 etapui - skriptas = task'as savo banke): interpretatoriaus
kodas - sistemos programoje (fiksuota zona), visa Lua būsena ir atmintis -
skripto task'o banke nuo `ZL_STATE_ADDR` (0xCC00); pastovus adresas
sutrumpina Z80 kodą (leksikas 1512 -> 1151 B). Banko task'as SD tvarkyklės
kviesti negali, todėl skriptą į jo banką įrašo ne banko task'as
(`bank_write`). Z80: ~10 KB kodo.

17 etapo demonstracija (0.18.0): 17 testų `tests/lua` (8 teigiami, 8 klaidų,
16 bitų `w1wrap`) - SD kortelėje `TESTS/`, programa - `LUA.SYS` per 18b
krovėją (ROM 1 su Lua nebetelpa); task'as „runner“ (ne banko) skaito
testus iš SD, banko task'as „lua“ vykdo, išvestis lyginama su `*.out`. Tie
patys testai: `make lua-check` (PC, ASan / UBSan) ir `make lua-ref` (tikras
Lua 5.4 - antras šaltinis teigiamiems testams).

| Testas (ZX) | Laikas | VM instrukcijų (PC) |
|---|---|---|
| `t4func` (`fib(20)`, faktorialas, ...) | 4610 tick'ų (92 s) | 273 934 |
| `t5algo` (pirminiai iki 1000, Collatz) | 2985 tick'ai (60 s) | 112 941 |

VM lėtas (~1200-1800 T instrukcijai: 32 bitų aritmetika ir 5 B reikšmės per
zsdcc) - optimizuoti vėliau.

**Atminties žemėlapis 0.17.0.** ZX Lua (~10 KB) netilpo į 13 KB programos
sritį: programai dabar 0x5D00-0xA3FF (18,25 KB), įkrovos stekas
0xA400-0xA5FF, heap 0xA600-0xBBFF (5,5 KB; išmatuota: 14 / 16 etapai
naudoja daugiausia ~4,3 KB). Lua duomenys - bankuose.

**ROM 1 ir Lua (Donato sprendimas).** Šriftas + Lua sistema + SD tvarkyklė
suspausti netelpa į 16 KB, todėl galutinė Lua sistema bus kraunama iš SD
kortelės (ROM 1 - šriftas, konsolė, SD tvarkyklė); didesnis ROM - tik ZX128
klone su CPLD. 17 etapo ROM 1 - šriftas, programa su Lua ir testų blokas (be
SD tvarkyklės).

**Rasta:** `xSemaphoreTake(s, portMAX_DELAY)` gali grįžti anksčiau
(`INCLUDE_vTaskSuspend = 0`, 16 bitų tick'ai) - laukiama cikle (kaip ROM 0
`con_os_lock`). Pirmoji 17 etapo versija dėl to „praleido“ `t5algo` (rodė
ankstesnio testo rezultatą).

## Appsai (16 etapas)

Appsas - banko modulis (`src/module.h`, `src/mod_crt.asm`), linkintas 0xC000:
antraštė „ZM“ (įėjimas, aprašas, atvaizdo ir BSS galas) ir `module_info_t`
(vardas, steko gylis, prioritetas, tipas `MODULE_TASK`, nauja - `version`).
Tas pats atvaizdas - SD faile `APPS/*.APP` ir juostoje (`z88dk-appmake +zx
--noloader`, kodo blokas 49152).

| Komanda | Kas |
|---|---|
| `load` | juosta: antraštė (tipas 3), blokas į naują banką, task'as |
| `run FAILAS` | SD: `mod_load` į naują banką, task'as (pvz. `run apps/spin.app`) |
| `unload VARDAS` | task'as sunaikinamas, bankas atlaisvinamas, langas išvalomas |
| `apps` | langas, vardas, versija, bankas, šaltinis |

Appsas gauna langą (slotą, `src/sys/app.h`: 2 langai eilutėse 16-23) - task'o
argumentas. Jis kviečia tik ROM 0 funkcijas (konsolė `con.h`, FreeRTOS).

Savo appsas: nukopijuoti šabloną `apps/hello.c` (komentaruose - žingsniai),
įrašyti vardą į Makefile `APPS`, `make apps` -> `build/apps/VARDAS.app` ir
`VARDAS.tap`. Pavyzdžiai: `hello` (veikimo laikas sekundėmis), `spin`
(sukutis).

**Rasta klaida ROM 0 (zsdcc `-SO3`).** `win_clear()` cikle `r < win->h`
sugeneruotas kodas kiekviename žingsnyje vėl padidindavo rodyklę (skaitė
`win + 3`, `+ 6`, `+ 9` ...), todėl valydavo eilutes už lango - lange nuo 16
eilutės pasiekdavo 24-ą, t. y. 0x5800-0x5FFF (branduolio kintamieji,
`pxCurrentTCB`) - panika po `unload`. Pataisa: `h = win->h` prieš ciklą
(`src/con.c`); rasta sekant instrukcijas emuliatoriuje, kol 0x5B00 tapo 0.

## Juostos loaderis (15 etapas)

Savas ROM LD-BYTES analogas (`src/sys/tape_asm.asm`, ne ROM kodo kopija):
EAR - prievado 0xFE bitas 6, pilot tonas (256 impulsų porų), sync, bitai
pagal impulsų porų trukmę (59 T paieškos ciklas, slenksčiai apskaičiuoti
standartiniams laikams 2168 / 667 + 735 / 855 / 1710 T), flag baitas,
duomenys, kontrolinis XOR. Rėmelis mirga (pilot - raudona / žydra, duomenys -
mėlyna / geltona), SPACE nutraukia. Bandomoji juosta - `z88dk-appmake +zx
--noloader` (antraštė + blokas).

- **Kodas nekontenduotoje atmintyje.** ROM 0 laisva tik 161 B, todėl
  loaderis - programos gale (`rodata_user` - paskutinė CODE dalis), RAM 2
  virš 0x8000; `tools/mktaptest.py addr` po susiejimo tikrina adresą
  (mažesnis - `app.bin` pervadinamas `app.bin.bad`, klaida).
- **Planuoklis.** `tape_load` (`src/sys/tape.c`): `vTaskSuspendAll()`,
  banko įjungimas, skaitymas, `xTaskResumeAll()`. Pertraukimai neišjungiami:
  IM1 vektorius laikinai - trumpa paprogramė, kuri tik skaičiuoja kadrus
  (~140 T vėlinimas - toli nuo slenksčių), po to `xTaskCatchUpTicks(kadrai)`.
  `vTaskStepTick()` šiame FreeRTOS yra tik su `configUSE_TICKLESS_IDLE`.
- **`xTaskDelayUntil` po pauzės.** Task'as su periodu, kurio terminas jau
  praėjęs, nebeblokuoja ir vejasi kiekvieną praleistą periodą: po 100 s
  krovimo klaviatūros task'as 4832 kartus iš eilės kvietė `kbd_poll`
  (~1000 kadrų „užšalimas“). Dabar `pdFALSE` - atskaita perkeliama į dabar.
- Konsolė: `tape [BANKAS]` - antraštė (vardas, ilgis), blokas į banką nuo
  0xC000 (be argumento - laisvas greitas bankas), baitų suma, trukmė tick'ais.
  Bankas lieka skirtas (16 etapas - appsai).

| Matavimas (`check-s15`, 16 KB) | Reikšmė |
|---|---|
| juostos signalas (antraštė + blokas, 128K kadrais) | 5083 |
| `tape` trukmė tick'ais (nuo komandos, su laukimu ir pauze) | 5123 |
| IM1 kadrai duomenų bloko metu | 4832 |
| tick skaitiklis po krovimo - kadras | ~ paleidimo poslinkis (42), nešoka |

## VS1053 MP3 (14 etapas)

VS1053 tame pačiame išoriniame prievade: OUT bitas 6 - /XCS (SCI, registrai),
bitas 7 - /XDCS (SDI, duomenys), IN bitas 6 - DREQ (FIFO priims >= 32 B).
Tvarkyklė `audio` (`src/mod/vs1053.c`, modulis iš SD `MOD/VS1053.ZXM`):
inicializacija (programinis SM_RESET, versija 4, CLOCKF), `write` - siunčia po
32 B, kol DREQ = 1, grąžina nusiųstų baitų skaičių; `ioctl` VOL / STATUS
(DECODE_TIME, AUDATA, HDAT0 / 1) / RESET.

Srauto task'as `mp3` (`src/sys/mp3play.c`): failas per `sd` tvarkyklę po 512 B
į fiksuotos zonos buferį ir į VS1053; kai DREQ = 0 - `vTaskDelay(1)`, todėl kiti
task'ai (konsolė, būsena) veikia. Pradžioje VS1053 FIFO užpildomas visas (2 KB).
Konsolė: `mp3 play FAILAS`, `mp3 stop`, `mp3` (būsena), `spi hw|bb`.

Du SPI variantai (Donato pasirinkimas), skiriasi tik ROM 0 primityvai
`ext_spi`, `ext_spi_tx`, `ext_spi_rx` (`ext_spi_hw` vėliavėlė):

| Variantas | Kaip | Srautas | Patikra |
|---|---|---|---|
| 2 - bit-bang (`s14`) | SCK / MOSI per latch 0x3F, 8 bitai išskleisti be šakų | ~6 KB/s - MP3 16 kbps | `check-s14` |
| 1 - aparatinis (`s14h`, `-DZXRTOS_SPI_HW`) | poslinkio registras prievade 0xBF: OUT išstumia baitą per pasirinktą įrenginį, IN - priimtas baitas; OUTI / INI | ~20 KB/s - MP3 128 kbps | `check-s14h` |

SD tvarkyklė (0.14.0): visas 512 B sektorius skaitomas tiesiai į kvietėjo
buferį, kiti (FAT, katalogai) - per 2 sektorių podėlį; 14 etapo kortelė - 32 MB,
2 KB klasteriai (`mkfat.py --cluster 4`). LM75 (I2C) modulio 14 etapo kortelėje
nėra: lėta I2C transakcija laiko prievado mutex'ą ir stabdytų srautą.

Emuliatoriuje - `zxe-run --vs1053 --vs-wav vs.wav [--spi-hw]` (libzxemu
0.17.0: MP3 vartojamas realiu laiku pagal kadrų antraštes, PCM - libmpg123);
`tools/wavcheck.py --nogap` tikrina, kad kiekvienas 20 ms kadras skamba
(melodija `tools/mkode.py` be pauzių, MP3 - `media/`, sukurta ffmpeg).

## Moduliai iš SD ir FT81x video (13 etapas)

ROM 1 pilnas, todėl nuo 13 etapo jame - tik šriftas, programa ir SD
tvarkyklė. Task'as `init` prijungia kortelę ir `src/sys/modload.c` įkelia
visus `MOD/*.ZXM` (tas pats formatas kaip ROM 1 moduliai) į laisvus bankus
ir juos paleidžia: `BEEP`, `AY`, `I2C`, `FT81X`. Konsolės komanda
`load FAILAS` įkelia modulį vykdymo metu.

Išorinio prievado primityvai (latch, SPI baitas, prievado magistralė `ext`)
dabar ROM 0 (`src/extport.asm`), todėl I2C, SD ir FT81x moduliai gali būti
skirtinguose bankuose, o mutex'as lieka vienas.

FT81x (FT813, 800 x 480) - CS bitas 5. Tvarkyklė `video`: inicializacija
(ACTIVE, REG_ID 0x7C, laikai, šriftas į RAM_G, DISP, PCLK, apšvietimas),
atvaizdavimo sąrašas (`write`, `FT_*` makrokomandos), tekstas sistemos 8x8
šriftu su LT raidėmis, duomenys į RAM_G (bitmap'as `IMG/LOGO.565` iš SD).
ULA ekranas lieka konsolei. Task'as `video` kas 25 tick'us piešia testinį
vaizdą: 8 spalvų juostas, laužtę, taškus, bitmap'ą, tekstą ir judantį tašką.
Emuliatoriuje - `zxe-run --ft81x` (libzxemu 0.16.0), kadras - `ftdump`;
`tools/pngcheck.py` tikrina pikselius. Koprocesorius nenaudojamas.

ULAplus (plano klono variantas) - neįgyvendinta.

## SPI ir SD kortelė (12 etapas)

SPI - bit-bang per tą patį išorinį prievadą 0x3F: OUT bitas 2 - SCK, 3 - MOSI,
4 = 1 - SD pasirinkta (/CS = 0), 5 / 6 - video / audio CS (rezervuota); IN
bitas 7 - MISO (kai CS). Aparatūra: 74LVC125 į 3,3 V pusę, 74HCT buferis
MISO, atskiras 3,3 V stabilizatorius >= 200 mA.

Banko modulis `extio` (bankas 4) dabar turi I2C + LM75 ir SPI + SD + Petit
FatFs R0.03a (`deps/tarballs/pff3a.zip`, sava `src/mod/pffconf.h`). Abi
magistralės dalijasi vienu latch registru, todėl jas saugo vienas prievado
mutex'as `ext`. SD inicializacija SPI režimu (CMD0, CMD8, ACMD41, CMD58 -
SDHC / SDSC, SD v1, MMC), sektoriaus skaitymas (CMD17) ir rašymas (CMD24);
512 B sektoriaus buferis - heap'e (fiksuota zona); SPI baitas - asembleriu.

Konsolė: `sd` (kortelė ir FAT), `dir [kelias]`, `type FAILAS`, `log TEKSTAS`
(rašo į esamą LOG.TXT). Patikroms `tools/mkfat.py` iš katalogo `sd/` sukuria
FAT16 atvaizdą su MBR; emuliatoriuje `zxe-run --sd build/sd.img` (libzxemu
0.15.0).

ROM 1 įrašai nuo 0.12.0 suspaudžiami (savas LZ, `tools/mkrom.py`,
išskleidžia ROM 0 `rom1_unlz`); programa gali užimti 0x5D00-0x8BFF,
įkrovos stekas - 0x8C00-0x8FFF, heap - 0x9000-0xBBFF.

## I2C ir LM75 (11 etapas)

Aparatūra: išvesties latch ir įvesties buferis prievade 0x3F (dalinis
dekodavimas A7 = 0, A6 = 0, A5 = 1, A1 = 1, A0 = 1 - nesikerta su ULA, 0x7FFD,
AY, Kempston, Fuller, DivMMC ir SPI registru 0xBF; 1.0.0, žr. `docs/HARDWARE.md`;
iki tol aprašyta tik A0, A1, A5), atviro kolektoriaus
SDA / SCL su 4,7 kΩ pull-up. OUT: bitas 0 / 1 = 1 traukia SDA / SCL į 0;
IN: bitas 0 / 1 - linijų lygiai, bitai 2-7 = 0.

Banko modulis `i2c` (bankas 4) duoda dvi tvarkykles:

- `i2c` - bit-bang: START, pakartotinis START, STOP, ACK / NACK, clock
  stretching (laukiama, kol SCL tikrai pakils, su riba); pertraukimų
  neišjungia; magistralės mutex'as; ioctl - perdavimas ir adresų paieška;
- `lm75` - task'as kas sekundę skaito temperatūrą (0,5 °C), min / max,
  klaidos; `read` grąžina būseną.

Konsolė: `temp`, `i2c`. Emuliatoriuje sąsają ir LM75 imituoja libzxemu
0.14.0 (`zxe-run --lm75 23.5`, `--i2c-stretch T`, scenarijuje `lm75 -5.5`).

Konsolės komandos nuo 0.11.0 - atskiruose `src/sys/cmd_*.c`; programa
apibrėžia lentelę `shell_cmds` ir linkeris paima tik joje esančias komandas.

## Garsas (10 etapas)

AY-3-8912 tvarkyklė `ay` - banko modulis (bankas 4): kodas, melodijos ir
grotuvo task'as `ay` (prioritetas 3) gyvena banke, programa jį valdo per
`drv_write` (komandų eilė) ir `drv_ioctl` (būsena). Grotuvas kas tick'ą:
A - melodija, B - bosas, C - triukšmo būgnas su gaubtine; natos ataka ir
slopimas, pasikartojančios natos atskiriamos. Melodijos: L. van Beethoven
„Odė džiaugsmui“ (vieša nuosavybė) ir sava ZXRTOS fanfara.

Konsolė: `ay` (būsena), `ay play 0|1`, `ay stop`; nežinoma komanda -
beeper signalas (banko tvarkyklė `beep`). Kol groja muzika, task'as
`pirmin.` be pauzių skaičiuoja pirminius skaičius - muzika nestringa.
`make check-s10` įrašo garsą (`zxe-run --wav`) ir `tools/wavcheck.py`
tikrina garsumą: melodija skamba, po `ay stop` - tyla, signalas girdisi.

## Klaviatūra ir džoistikas (9 etapas)

Task'as `input` kas tick'ą nuskaito visą klaviatūros matricą (ULA
magistralė) ir Kempston prievadą 0x1F; abi tvarkyklės (`kbd`, `joy`) deda
įvykius į bendrą eilę: paspaudimas, atleidimas, džoistiko būsena.

| Klavišai | Rezultatas |
|---|---|
| CAPS + raidė / CAPS LOCK (CAPS + 2) | didžiosios raidės |
| SYMBOL + klavišas | ženklai kaip ZX (`"` `;` `<` ...) |
| CAPS + SYMBOL + 1..9 | ą č ę ė į š ų ū ž (su CAPS LOCK - didžiosios) |
| CAPS + 0 / 5..8 / SPACE | trinti, rodyklės, BREAK |

Atšokimai: būsena priimama, kai dvi apklausos sutampa; kartojimas po
0,5 s kas 80 ms. Džoistikas be sąsajos aptinkamas (laisva magistralė
0xFF). Konsolės komanda `events` rodo įvykius, kol paspaustas BREAK.

## Sistemos konsolė (8 etapas)

Komandos renkamos klaviatūra (tvarkyklė `kbd`, įvykių eilė), išvestis -
per tvarkyklę `scr`:

| Komanda | Ką rodo |
|---|---|
| `ps` | task'ai: vardas, lango bankas, būsena (X R B D), prioritetas, steko pradžia, niekada nenaudota steko dalis |
| `banks` | 8 bankų paskirtis: moduliai, tvarkyklės, heap, ekranas, laisvi |
| `free` | heap laisva / minimumas, malloc klaidos, laisvi bankai, tick |
| `kill VARDAS` | sunaikina task'ą (ne IDLE, ne save, ne laikantį mutex'ą); banko task'o bankas atlaisvinamas |
| `drv` | tvarkyklės: magistralė, bankas, kvietimai, įvykių eilė |
| `beep` | tonas per banko tvarkyklę `beep` (bankas 3) |
| `test stekas / malloc / assert` | klaidų kablių bandymai |

Klaidų kabliai (ROM 0, `src/panic.h`): steko perpildymas ir `configASSERT` -
panika (raudonas rėmelis, apačioje priežastis, task'as, PC), malloc
nesėkmė - skaitiklis. Tvarkyklių modelis (`src/sys/drv.h`): `init` / `read` /
`write` / `ioctl`, magistralės mutex'as (ULA - `kbd` ir `beep`), įvykių eilė;
tvarkyklės kodas gali būti banke - kvietimo metu įjungiamas jos bankas.

ROM 0 užimtas 96 %, todėl `ps` / `kill`, tvarkyklių registras ir konsolė yra
sistemos programoje (ROM 1 -> RAM 5); ROM 0 duoda tik FreeRTOS vidaus lentelę
`zx_kernel` (sąrašai, TCB poslinkiai) ir kablius.

## Bankų moduliai (7 etapas)

Modulis - task'as, kurio kodas, duomenys ir stekas viename RAM banke:

1. linkinamas adresui 0xC000 su `src/mod_crt.asm` (antraštė „ZM“: įėjimas,
   aprašas, atvaizdo ir BSS galas) prie ROM 0 simbolių;
2. `tools/mkrom.py module` patikrina antraštę ir ar stekas neuždengs BSS;
3. ROM 1 įraše nurodomas bankas: `0x10:0xC000@3:modulis.bin`;
4. branduolys įkrovos metu išskleidžia visus modulius (`rom1_load_banks()`,
   bankai pažymimi skirtais), programa paleidžia `module_start(bankas, arg)`.

Modulis kviečia ROM 0 funkcijas (FreeRTOS, konsolė) ir naudoja fiksuotos
zonos objektus; bankų perjungti negali. Visų modulių adresai vienodi
(`mod_main` 0xC097, SP 0xFFCC) - skiriasi tik bankas.

## Atmintis

| Adresai | Bankas | Paskirtis |
|---|---|---|
| 0x0000-0x3FFF | ROM 0 | branduolys: crt0, FreeRTOS, konsolė, bankai, ROM 1 krovėjas |
| 0x0000-0x3FFF | ROM 1 | saugykla: šriftas, programa, bankų moduliai (suspausti LZ, tik kopijuojant) |
| 0x4000-0x5AFF | RAM 5 | ekranas |
| 0x5B00-0x5CFF | RAM 5 | branduolio DATA, BSS (IM1 vektorius, 0x7FFD šešėlis, trampolinas) |
| 0x5D00-0xA3FF | RAM 5 / 2 | programa iš ROM 1 (0.17.0; iki 0.9.0 - nuo 0x6000, iki 0.12.0 - iki 0x7DFF, 0.12.0 - iki 0x8BFF, 0.13.0 - iki 0x8FFF) |
| 0xA400-0xA5FF | RAM 2 | įkrovos ir panikos stekas (0.13.0 - 0x9000, 0.12.0 - 0x8C00, iki tol 0x7E00) |
| 0xA600-0xBBFF | RAM 2 | FreeRTOS heap_4 (TCB'ai, stekai, eilės, SD buferis), 5,5 KB (0.13.0 - 0x9200, 10,5 KB) |
| 0xBC00-0xBF8F | RAM 2 | šriftas |
| 0xC000-0xFFFF | RAM 0, 1, 3, 4, 6, 7 | bankai: kiekvieno task'o langas (kontekste), modulių kodas, banko task'ų duomenys ir stekai |

## Kompiliavimas

```sh
make -j4        # zxrtos-0.rom, zxrtos-1.rom, build/sN/zxrtos-1.rom
make check      # visų etapų patikros (zxe-run)
make run        # Fuse 128K su ZXRTOS ROM'ais
make run-s2     # Fuse su 2 etapo ROM 1 (run-s4n, run-s6n, run-s7n - neigiami variantai)
make run        # 8 etapas: konsolė - surinkite help, ps, banks, beep ...
make stats      # ROM 0 užimtumas
```

Fuse rankiniu būdu:

```sh
fuse-sdl --machine 128 --rom-128-0 zxrtos-0.rom --rom-128-1 zxrtos-1.rom
```

## Failai

- `src/crt0.asm` - reset, IM1, NMI, DATA / BSS, main()
- `src/kernel.c` - ROM 0 main(): šriftas, bankų moduliai ir programa iš ROM 1
- `src/rom1.c`, `src/rom1_asm.asm`, `src/rom1.h` - ROM 1 saugykla
- `src/bank.c`, `src/bank_asm.asm`, `src/bank_os.c`, `src/bank.h` - bankų sluoksnis
- `src/app_crt.asm` - programos paleidimas (0x6000)
- `src/module.c`, `src/module.h`, `src/mod_crt.asm` - bankų moduliai (0xC000)
- `src/mod/mod_demo.c`, `src/mod/moddemo.h` - 7 etapo moduliai (6 upės)
- `src/mod/beep.c` - banko tvarkyklė (MODULE_DRIVER)
- `src/mod/ay.c`, `src/mod/ay.h` - AY tvarkyklė ir melodijų grotuvas (banke)
- `src/extport.asm`, `src/extport.h` - išorinio prievado primityvai ROM 0 (latch, SPI, magistralė)
- `src/mod/extio.c`, `src/mod/extio.h` - prievado modulių bendra dalis (magistralė `ext`)
- `src/mod/i2c_main.c`, `src/mod/sd_main.c` - moduliai i2c ir sd
- `src/mod/ft81x.c`, `src/mod/ft81x.h` - FT81x video tvarkyklė (modulis iš SD)
- `src/mod/vs1053.c`, `src/mod/vs1053.h` - VS1053 audio tvarkyklė (modulis iš SD)
- `src/sys/mp3play.c`, `mp3play.h` - MP3 srauto task'as; `cmd_mp3.c` (mp3, spi)
- `media/ode16.mp3`, `media/ode128.mp3` - bandomoji melodija (`tools/mkode.py` + ffmpeg)
- `src/sys/tape_asm.asm`, `tape.c`, `tape.h` - juostos loaderis; `cmd_tape.c` (tape)
- `tools/mktaptest.py` - bandomoji juosta, suma, trukmė, loaderio adreso patikra
- `src/sys/app.c`, `app.h` - appsai (juosta, SD, unload); `cmd_app.c` (load, run, unload, apps)
- `apps/hello.c` - appso šablonas; `apps/spin.c` - antras appsas
- `src/sys/modload_dir.c` - `mod_load_dir` (atskirta nuo `modload.c`)
- `src/lua/` - ZX Lua (`zl.h` API, `zl_int.h`, `zl_lex.c`, `zl_comp.c`, `zl_vm.c`, `zl_lib.c`)
- `tests/lua/*.lua`, `*.out` - ZX Lua testai; `tools/zlrun.c` - PC paleidiklis; `tools/mktests.py` - testų blokas (dydžių patikra)
- `src/lua/zl_gc.c`, `zl_tab.c` - GC ir lentelės (18 etapas); `src/sys/keytext.c` - klavišas -> UTF-8
- `src/lua/zl_sys.c` - Lua sistemos moduliai; `src/sys/luagw.h` - tarpininkas; `src/sys/luasys.h`, `luarepl.c` - 19 etapo REPL ir skriptų task'ai
- `scripts/demo.lua`, `autorun.lua` - 19 etapo skriptai (SD); `soak.lua`, `work.lua` - stabilumo testas
- `docs/HARDWARE.md` - I/O plokštė; `docs/RELEASE.md` - išleidimas
- `src/sys/modload.c` - moduliai iš SD; `cmd_mod.c` (load), `cmd_video.c` (video), `cmd_fs.c` (dir, type)
- `tools/mklogo.py` - bandomasis bitmap'as; `tools/pngcheck.py` - FT81x kadro patikra
- `src/mod/i2c.c`, `src/mod/i2c.h` - I2C bit-bang ir LM75 (extio dalis)
- `src/mod/sd.c`, `src/mod/sd.h`, `src/mod/pffconf.h` - SPI, SD, Petit FatFs (extio dalis)
- `sd/` - SD kortelės atvaizdo turinys patikroms; `tools/mkfat.py` - FAT16 atvaizdas
- `src/panic.c`, `src/panic_asm.asm`, `src/panic.h` - klaidų kabliai ir panika
- `src/sys/` - sistemos programa: `drv.c` (tvarkyklių modelis), `sysdrv.c`
  (scr), `sysdrv_kbd.c` (8 etapo kbd), `input.c` (9 etapo kbd ir joy),
  `zxps.c` (ps / kill), `shell.c` (konsolės branduolys), `cmd_*.c` (komandos);
  programoms - biblioteka `sys.lib`
- `src/con.c`, `src/con.h` - ekrano konsolė ir langai
- `src/con_asm.asm` - greitas simbolio piešimas
- `src/con_os.c`, `src/con_os.h` - ekrano mutex'as (FreeRTOS)
- `src/kbd.c`, `src/kbd.h` - klaviatūra
- `src/zx_hw.asm`, `src/zx.h` - prievadai (fastcall)
- `src/port/` - FreeRTOS porto ZX dalis (board defs, konfigūracija, port.c,
  `include/sdcc/portmacro.h` perdanga - bankas kontekste, `zxport.h` - xTaskCreateBanked,
  `freertos_tasks_c_additions.h` - lentelė zx_kernel)
- `src/demo.c` - etapų demonstracijos - programos (`ZXRTOS_STAGE`)
- `tools/mkfont.py` - šriftas, `tools/mkrom.py` - ROM 0, programa, ROM 1,
  `tools/mksyms.py` - ROM 0 simboliai programoms,
  `tools/zxcheck.py` - patikros pagal ekraną, `tools/wavcheck.py` - garso patikra, `tools/stats.sh` - užimtumas

## Licencijos

ZXRTOS kodas - Apache-2.0 (c) 2026 Donatas. FreeRTOS ir Z80 portas
(`deps/tarballs`, `src/port/port.c`) - MIT (Amazon, Phillip Stevens).
Petit FatFs (`deps/tarballs/pff3a.zip`) - FatFs licencija (ChaN, 1 punkto BSD).
