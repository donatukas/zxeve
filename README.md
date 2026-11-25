# zxeve

ZX Spectrum 128 emuliatorius Windows sistemai su FT81x EVE ekranu:
[libzxemu](../../../lib/libzxemu) (Z80, ULA, SD, I2C, VS1053) ir Bridgetek
BT8XXEMU (FT813 su koprocesoriumi, ROM šriftais ir lietimu). Skirtas ZXRTOS
FT81x darbui: ZX ekranas ir FT81x langas greta, pelė FT81x lange - lietimas.

Kryžmiškai kompiliuojamas iš Linux su MinGW-w64; sandara - kaip grotuvo
(zlog žurnalas, libconfuse nustatymai, visos DLL aplanke `system\`).

## Kompiliavimas

```sh
make -j4      # bin/zxeve.exe + system/ DLL, roms/, fonts/, nustatymai
make run      # per wine
make test     # 0 etapo bandymas, DLL patikra, GUI testai (wine + Xvfb)
make debug    # bin/zxeve-debug.exe - konsolinis variantas
make dist     # dist/zxeve-<v>.tar.gz ir dist/zxeve-<v>-win64.zip (Windows paketas)
make help
```

Priklausomybės - `../../../lib` (`make deps` parodo, ko trūksta ir kaip
pasigaminti): libzxemu (>= 0.21.0), libspectrum, zlib, bt8xxemu
(github.com/Bridgetek/EVE-Emulator), raylib, raygui, libconfuse, zlog, crashpad.

## Paleidimas Windows

`bin/` katalogą nukopijuokite į Windows. bt8xxemu.dll reikia Microsoft
Visual C++ 2015-2022 x64 runtime (`vc_redist.x64.exe`). Be jo FT81x
BT8XXEMU backend'as nepasileis - mygtuku „FT81x: builtin“ galima perjungti į
libzxemu imitatorių (be koprocesoriaus).

```
zxeve.exe [failas] [--conf F] [--no-audio] [--builtin]
```

Failai: `.tap .tzx` (juosta, autoload), `.z80 .sna .szx`, `.dsk .trd .scl`,
`.img` (SD kortelė - mašina perkuriama). Galima tempti į langą.

ZXRTOS: į `zxeve.conf` įrašykite `roms` (katalogas su ZXRTOS
`128-0.rom` = zxrtos-0.rom ir `128-1.rom` = zxrtos-1.rom) ir `sd_image`.

## Sąsaja

Meniu juosta (Failas, Mašina, FT81x, Rodinys, Pagalba), įrankių juosta su
Lucide piktogramomis ir patarimais, ZX ir FT81x kortelės, būsenos juosta.
Klavišai: F3 - atidaryti, F5 - reset, F6 - pauzė, F7 - garsas, F9 - tik ZX per visą
ekraną, F10 - tik FT81x per visą ekraną, F11 - visa programa per ekraną
(tas pats klavišas arba Esc - atgal; taip pat kortelės antraštės mygtukas);
visa kita klaviatūra - Spectrum'ui. Pelė FT81x lange - lietimas
(BT8XXEMU).

**ZXRTOS:** Mašina → ZXRTOS (arba mygtukas „ZXRTOS“) įkelia
`zxrtos\zxrtos-128.rom` ir `zxrtos\zxrtos-sd.img` - SD su FT81x
tvarkykle ir AUTORUN.LUA, todėl FT81x lange iškart vaizdas. Kitas ROM -
Mašina → Įkelti ROM...

Piktogramos - `res/lucide/*.svg` → `make icons` → `src/icons.c`.

## Nustatymai

`zxeve.conf` šalia `zxeve.exe` - aprašas `conf/zxeve.conf`. Žurnalas -
`zxeve.log` (konfigūracija `zlog.conf`), starto pėdsakas - `zxeve-start.log`.

## Lūžiai (Crashpad)

Lūžus programa į `zxeve-start.log` įrašo modulį ir poslinkį
(`LŪŽIS: ... zxeve.exe + 0x26E2`), parodo pranešimą, o Crashpad
(`system\crashpad_handler.exe`) įrašo `crashdb\reports\<uuid>.dmp`.
Linux'e:

```sh
make crash DUMP=~/is-windows.dmp   # išimtis, modulis, stekas su eilutėmis
make crash ADDR=0x26E2             # zxeve.exe poslinkis -> funkcija:eilutė
```

Dump'as nagrinėjamas tik su tuo pačiu sukompiliavimu (`build/zxeve-sym.exe`).
Bandymui Windows'e: `set ZXEVE_CRASH_TEST=crash` ir `zxeve.exe`.

## Testų režimas

```
zxeve.exe --test-frames N [--screenshot F.png] [--ftdump F.png]
          [--zxdump F.png] [--touch KADRAS X Y] [--type KADRAS TEKSTAS]
```

Po N kadrų įrašomi failai ir programa baigiasi (0 - gerai, 2 - klaida), be
modalinių langų.

## Apribojimai

- BT8XXEMU dirba realiu laiku savo gijose - FT81x kadrai nedeterministiniai
  (patikros - PNG su tolerancija, libzxemu `ftwait`).
- libzxemu failus atveria per `fopen` (ANSI) - keliai su ne ASCII
  raidėmis gali neveikti.
- FT81x INT - IN bitas 5 (libzxemu 0.20.0 pasiūlymas).

## Licencijos

zxeve - GPL-3.0-or-later (kaip libzxemu). BT8XXEMU - Bridgetek nuosavybė,
nemokama licencija EVE programų kūrimui (DLL galima platinti su programa;
suderinamumas su GPL - pasitikslinti). Roboto šriftas - Apache 2.0.
Spectrum ROM'ai - žr. `roms/README.copyright`.
