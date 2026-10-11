# FoMni37

**FoMni, the Omnichord-style chord harp, on the M-VAVE SMK-37 Pro.**

FoMni37 is a port of [FoMni](https://github.com/charlesvestal/fm1-fomni) by **Charles Vestal**, a chord harp
firmware for the M-VAVE FM-1 inspired by the Suzuki Omnichord, to the M-VAVE SMK-37 Pro keyboard. Both devices
use the same JieLi AC791N chip, boot chain and update loader; only the panel wiring differs. FoMni37 adds the
SMK-37 Pro board support (keys, pads, faders, wheels, knobs, display, LEDs, flash map, update path) and maps
FoMni onto the bigger panel: the 16 pads become the strum plate, the 37-key keybed holds the chord buttons.

FoMni's own README, with how the instrument works, its pages and its MIDI, is
[README-UPSTREAM.md](README-UPSTREAM.md).

> **Status: experimental.** Tested on an SMK-37 Pro. Read [Safety](#safety) before you install it.

## Playing it on the SMK-37 Pro

Every FM-1 control keeps its FoMni function under the SMK's printed name, so FoMni's manual applies. The SMK's
extra controls add to that.

### Pads: the strum plate

The 16 pads are the 16 strings, played with velocity: a soft hit sounds quieter and darker, a hard one full and
bright (and the MIDI out note carries the velocity). The top row is strings 1–8 (low), the bottom row strings
9–16 (high), as the white keys F3–G5 run on the FM-1. Run a finger across a row to strum.

Pad lights: a string lights in the harp colour while it rings; at rest the root strings glow dimly (the dots of
the strum plate). Setup page, Lights Off: the pads stay dark.

### Keybed: two layouts (KNOB BANK switches, saved with the project)

**HARP (default): the FM-1 layout.** The middle 27 keys F3–G5 work as on the FM-1: the white keys pluck the
strings (with velocity), the black keys are FoMni's 11 chord buttons (F C G, Dm Am, Em G7 E7, D7 Bb, A7 by
default; changed on the Chords page). The outer keys, held, pick the chord type of the next chord button:

| Outer key held | Chord type |
|---|---|
| C3 | minor (as CHORD) |
| D3 | 7th (as NOTE REPEAT) |
| C3 + D3, or C6 | m7 |
| E3 | maj7 |
| C#3 | dim |
| D#3 | aug |
| A5 | sus4 |
| B5 | add9 |
| G#5, A#5 | not used |

**CHORDS: the Omnichord's chord buttons on the whole keybed.** Every key is a chord button; the key's note is
the root and its octave the row:

| Keys | Chords |
|---|---|
| C3 … B3 | major (C, C#, D … B) |
| C4 … B4 | minor |
| C5 … B5 | 7th |
| C6 | lets the chord go (also a held one) |

CHORD and NOTE REPEAT held still add minor / 7th (both: m7). The strings are on the pads only. The KNOB BANK
light is on in this layout.

### Buttons

| SMK-37 Pro | FoMni (FM-1 name) | Does |
|---|---|---|
| PATCH | HOME | Play page |
| SEQ | SEQ | Rhythm page |
| FX | FX | Sound page |
| SCALE | SEL | Chords page |
| GLOBE | GLO | Setup page |
| PARA | EDIT | next page |
| < / > | (new) | previous / next page |
| SEQ PLAY | PLAY | rhythm on / off |
| SEQ REC | REC | SYNC START |
| ARP | ARP | CHORD HOLD |
| CHORD | ENV | held: minor |
| NOTE REPEAT | LFO | held: 7th (with CHORD: m7) |
| OCTAVE− / OCTAVE+ | OCT− / OCT+ | the strum plate's octave |
| BT | SAVE | save (FoMni also saves by itself when quiet) |
| KNOB BANK | (new) | keybed HARP / CHORDS |
| FADER BANK | (new) | fader bank A / B (light on in bank B) |

### Knobs, faders, wheels

| SMK-37 Pro | Does |
|---|---|
| K1 | master volume (the FM-1's MASTER pot, as a virtual pot) |
| K2 | SELECT: tempo |
| K3 | ALGORITHM: rhythm |
| K4 | PRESETS: transpose |
| K5 … K8 | KNOB 1 … 4: the page's four values |
| Faders 1–4, bank A | Voice 1, Voice 2, Chord, Drums (the levels) |
| Faders 1–4, bank B | Sustain, Reverb, Chord rev, Space |
| Pitch wheel | pitch bend ±2 semitones (strings, chord and bass; not the drums) |
| Mod wheel | vibrato, 5.5 Hz, up to ±½ semitone (strings, chord and bass) |

A fader takes over once it is moved (also after a bank change); the new value shows in the header. The wheels
calibrate themselves. The pitch bend and vibrato are not sent as MIDI.

### Power-on and special combos

| Combo | Does |
|---|---|
| OCTAVE− + OCTAVE+ held while powering on | HARDWARE CALIBRATION: re-teach which button and knob is which. It asks for the FM-1 names: use the tables above (e.g. "ENV" = CHORD, "HOME" = PATCH, "KNOB 1" = K5). The defaults already match the SMK-37 Pro. |
| OCTAVE− + OCTAVE+ held 5 s | the chip's update mode (UBOOT), for flash backups and recovery |
| two failed starts in a row | SAFE MODE: no sound, USB on for a reinstall; SEQ PLAY tries again |

### Not used (yet)

The pedal jack, the TRS jack (on the SMK it is MIDI OUT; FoMni's TRS MIDI IN is an FM-1 input and is off here),
pad pressure (aftertouch) and Bluetooth (FoMni never turns the radio on). USB MIDI in and out work as in FoMni.

## Build

The JieLi toolchain is Linux x86-64 only: build on Linux or in WSL2 (Ubuntu) on Windows. Setup as in
[BUILDING.md](BUILDING.md):

```sh
sh tools/get_toolchain.sh                       # the toolchain, to ~/.jieli/toolchain
git clone --depth 1 --filter=blob:none --sparse --branch AC79NN_SDK_V1.2.1_2023-12-13 \
    https://gitee.com/Jieli-Tech/fw-AC79_AIoT_SDK.git ~/fw-AC79_AIoT_SDK
git -C ~/fw-AC79_AIoT_SDK sparse-checkout set cpu/wl82/tools
sudo apt install python3-pil                    # or: pip3 install Pillow

BOARD=smk37 ./build.sh       # FoMni37 for the SMK-37 Pro -> build/fomni37.fwsc, identity "SMK-37 Pro_800"
./build.sh                   # FoMni for the FM-1 -> build/omni.fwsc, byte-identical to upstream FoMni
```

Host tests (after an FM-1 build; `AC79_SDK` set for the package and update-path tests):

```sh
AC79_SDK=~/fw-AC79_AIoT_SDK tests/run_tests.sh
```

They include the SMK-37 Pro flash map and store owner, the SMK package layout, the update staging and the
update loader's windows (which must never touch the protected ranges), and the instrument as the SMK build
compiles it. On Linux with glibc the simulator build of upstream FoMni needs `CC="cc -D_GNU_SOURCE"` (its
`-std=c99` hides `clock_gettime`; upstream builds on macOS).

Build options for the SMK: `OM_LCD_BAUD` (the display's SPI clock divider, default 1 = 30 MHz; 4 = the stock
firmware's 12 MHz, see [Open points](#open-points)).

## Install

1. Read [Safety](#safety) and back up the flash.
2. Connect the SMK-37 Pro over USB, close every other program that uses its MIDI port, and run FoMni's installer
   (Python 3 with `mido` and `python-rtmidi`):

   ```sh
   python3 tools/fm1_install.py build/fomni37.fwsc
   ```

   It finds the keyboard's MIDI port (the stock firmware's, or FoMni37's "FoMni SMK-37"), checks the model,
   asks the running firmware to start the update loader and writes the package; the keyboard restarts and the
   installed identity is checked. FoMni's web installer is not set up for the SMK-37 Pro.
3. Updating FoMni37 later works the same way.

**On the first start** FoMni37 takes over the store area of the flash it shares with other SMK-37 Pro ports
(0x9D000–0xAEFFF): whatever another firmware, or the stock firmware, kept there is cleared, and the area is
tagged as FoMni's. FoMni's own project and panel settings live there.

**Roll back to the stock firmware** with M-VAVE's own updater and the official SMK-37 Pro firmware package.
FoMni37 answers the update protocol as the stock firmware does and writes the stock update record name
(`ota-SMK-37 Pro_016`), so the official tools should see the keyboard as usual (not yet tried with FoMni37; if
the updater does not find it, use UBOOT and your backup). FoMni's command-line installer cannot read the official SMK package (a different container layout), so
use M-VAVE's tool for that. The stock firmware then finds the store area cleared or holding FoMni's data; for
a completely clean state write that range back from your flash backup.

**If it does not start:** after two failed starts FoMni37 opens SAFE MODE with USB on, where the installer can
write a package again (or the stock firmware). After two more it drops into the chip's update mode (UBOOT),
where the app area can be rewritten with a JieLi UBOOT tool.

## Safety

- **Experimental firmware, no warranty** (see the GPL-3.0). You install it at your own
  risk.
- **Back up the whole 1 MiB flash first**, in the chip's UBOOT mode (e.g. with
  [jl-uboot-tool](https://github.com/kagaimiq/jl-uboot-tool)), twice, and compare the two dumps. The stock
  firmware has no way into UBOOT by itself; with FoMni37 installed, hold OCTAVE− and OCTAVE+ for 5 seconds.
  Keep the dump private: it holds data unique to your unit.
- **Never write 0xC0000–0xC1FFF** (Bluetooth pairing and the factory key / pad / wheel calibration) **or
  0xFF000–0xFFFFF** (key_mac), and leave the stock resources 0xF4000–0xFDFFF alone: they are unique to your unit
  or needed by the stock firmware. FoMni37 itself writes only 0x9D000–0xAFFFF (its store and the owner tag) and
  0xBB000–0xBFFFF (update staging); its update loader writes only the app area 0x4000–0x9CFFF. Each of these is
  an explicit allow-list, checked at compile time and again in the flash driver, and covered by the host tests.
  A manual UBOOT write is not protected: write only the ranges you need.
- FoMni37 reads the factory velocity curves from 0xC1000 and never writes them.
- Power the keyboard from a reliable USB port during an update and do not unplug it while the screen says
  UPDATE.

## Open points

- **Display clock.** FoMni drives the FM-1's display at 60 MHz and polls its keys between screen updates; the
  SMK's stock firmware runs its display at 12 MHz. FoMni37 uses 30 MHz. If the screen shows garbage, build with
  `OM_LCD_BAUD=4` (12 MHz): it works, but redraws while strings ring get slower. Strums are latched by the
  input scan either way, so none are lost, only delayed.
- The HARDWARE CALIBRATION screen asks for the FM-1 button names.
- Not used yet: pedal, TRS MIDI OUT, pad pressure.

## Branches

| Branch | What |
|---|---|
| `main` | FoMni (upstream `main` at 405ef41) + the SMK-37 Pro port, everything behind `BOARD_SMK37` |

## License and credits

FoMni37 is free software under the **GNU General Public License v3.0 only** ([LICENSE](LICENSE)), like FoMni and
Felucca. Where each part comes from, and the third-party licences, are in [LICENSING.md](LICENSING.md).

- **FoMni**: Charles Vestal, <https://github.com/charlesvestal/fm1-fomni> (the instrument, its UI, its
  platform changes; this repository is a fork of it).
- **Felucca**: Leo Kuroshita (@kurogedelic), Hügelton Instruments, <https://github.com/hugelton/Felucca> (the
  platform: hardware layer, USB, update loader, storage, installer), by way of
  [X0X](https://github.com/charlesvestal/fm1-x0x) by Charles Vestal.
- **Chordian**: Jan125, <https://github.com/Jan125/pb.chordian> (CC0: the rhythms, waves, drum sounds and envelope
  shapes FoMni is made from).
- **SMK-37 Pro board layer** (input, display and pad LEDs, flash map, update path): genkigenki, from the
  `smk37-board` series of [Snoop37](https://github.com/genkigenki/Snoop37) and the hardware notes in
  [fm1-to-smk37](https://github.com/genkigenki/fm1-to-smk37).

Omnichord is a trademark of Suzuki Musical Instrument Corporation, used only to say what inspired FoMni. M-VAVE,
FM-1 and SMK-37 Pro are names of their respective owners. FoMni37 is not affiliated with or endorsed by Suzuki,
M-VAVE, JieLi or Hügelton Instruments. No stock firmware is part of this repository.
