# ZXRTOS 1.0.0

FreeRTOS ZX Spectrum 128K kompiuteryje **vietoj originalaus ROM**. Įjungus
iškart startuoja FreeRTOS: task'ai naudoja 128K atminties bankus, iš SD
kortelės kraunamos tvarkyklės (AY muzika, I2C temperatūra, FT81x EVE
ekranas, VS1053 MP3), o vietoj BASIC veikia **ZX Lua** REPL.

Ant tikros geležies dar neišbandyta - tik emuliatoriuje.

## Failai

| Failas | Kas |
|---|---|
| `zxrtos-128.rom` | ROM, 32 KB: ROM 0 - branduolys, ROM 1 - šriftas, krovėjas, SD tvarkyklė |
| `zxrtos-sd.img` | SD kortelės atvaizdas (FAT, 32 MB) |

## Paleidimas zxeve

Meniu **Mašina → ZXRTOS** arba mygtukas **ZXRTOS** įrankių juostoje.
zxeve įkelia ROM ir SD atvaizdą, modelis - 128K. Krovėjas iš SD įkelia
Lua sistemą (`LUA.SYS`), tvarkykles iš `MOD/`, įvykdo `AUTORUN.LUA` ir
įjungia REPL. Atgal į įprastą ZX Spectrum - dar kartą paspaudus mygtuką.

## SD kortelė

| Kelias | Kas |
|---|---|
| `LUA.SYS` | ZX Lua sistema |
| `MOD/AY.ZXM` | AY muzikos grotuvas |
| `MOD/I2C.ZXM` | I2C ir LM75 temperatūros jutiklis |
| `MOD/FT81X.ZXM` | FT81x EVE ekrano tvarkyklė |
| `AUTORUN.LUA` | vykdomas starte: temperatūra į FT81x ekraną, AY muzika |
| `DEMO.LUA`, `WORK.LUA` | pavyzdiniai skriptai (`task.run("DEMO.LUA")`) |
| `EXTRA/` | `VS1053.ZXM` (MP3), `SOAK.LUA` (ilgas stabilumo testas) |

Į `MOD/` padėtos tvarkyklės įkeliamos starte. Tvarkyklių vienu metu - tiek,
kiek liko laisvų bankų.

## ZX Lua

REPL: įvesta eilutė pirma bandoma kaip išraiška (reikšmė atspausdinama),
tada kaip sakiniai; nebaigta konstrukcija (`function f()`) tęsiama
eilute su `>>`.

Kalba: kintamieji, `if`, `while`, `for`, funkcijos, lentelės
(`{1, 2, x = 3}`, `t.k`, `t[k]`, `#t`, `o:m()`), `pairs` / `ipairs`.
Bibliotekos: `table` (insert, remove, concat), `string` (len, sub, rep,
byte, char, upper, lower), `math` (abs, max, min, random), `print`,
`error`, `collectgarbage`. Sveikieji skaičiai - 16 bitų.

Sistemos moduliai:

| Modulis | Funkcijos |
|---|---|
| `screen` | `at(x, y, s)`, `cls()` |
| `key` | `get()` - klavišo kodas arba nil (nelaukia) |
| `joy` | `get()` - Kempston bitai arba nil |
| `task` | `sleep(t)`, `ticks()`, `run(failas)` - skriptas iš SD atskirame task'e |
| `temp` | `get()` - LM75 temperatūra, dešimtosios °C (nil - nėra) |
| `ay` | `play(n [, kartoti])`, `stop()` |
| `gfx` | `cls(0xRGB)`, `text(x, y, 0xRGB, s)`, `show()` - FT81x ekranas |
| `bank` | `free()` - laisvų bankų skaičius |

Pavyzdys - tekstas FT81x ekrane:

```lua
gfx.cls(0x013)
gfx.text(20, 20, 0xFF0, "Labas!")
gfx.show()
```

Klaviatūra: `{` `}` `[` `]` `~` `|` `\` - SYMBOL SHIFT + F G Y U A S D.

## Licencijos

ZXRTOS - Apache-2.0, (c) 2026 Donatas. FreeRTOS ir Z80 portas - MIT
(Amazon, Phillip Stevens). Petit FatFs - FatFs licencija (ChaN, 1 punkto
BSD).
