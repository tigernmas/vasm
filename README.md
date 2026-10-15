# vasm — Portable and Retargetable Assembler

**vasm** is a portable and retargetable assembler that can generate linkable objects in various formats or absolute code. Multiple CPU, syntax, and output modules can be selected at build time.

It supports many common directives/pseudo-opcodes (depending on the syntax module) as well as CPU-specific extensions. The assembler performs optimizations (for example, choosing the shortest possible branch instruction or addressing mode) and relaxations (for example, converting a branch to an absolute jump when necessary). Most syntax modules also support macros, include directives, repetitions, conditional assembly, and local symbols.

vasm supports target CPUs with more than 8 bits per byte (word-addressing) but requires that the host system has 8-bit bytes. You obtain a special binary for any combination of CPU and syntax module. All relevant output modules are included in that binary; select the desired output format at assembly time with the `-F` option. The default is a test/debug output format.

Official homepage: [http://sun.hasenbraten.de/vasm/](http://sun.hasenbraten.de/vasm/)  
Documentation (HTML): [http://sun.hasenbraten.de/vasm/release/vasm.html](http://sun.hasenbraten.de/vasm/release/vasm.html)  
Documentation (PDF): [http://sun.hasenbraten.de/vasm/release/vasm.pdf](http://sun.hasenbraten.de/vasm/release/vasm.pdf)  
Volker’s page: [http://www.compilers.de/vasm.html](http://www.compilers.de/vasm.html)

Current stable release: **2.0f** (10 July 2026).

## Supported CPU Modules

| Module   | Architectures / Notes |
|----------|-----------------------|
| `6502`   | 6502 family (6502, 65C02, 65CE02, DTV, 45GS02, HU6280, 65802, 65816) |
| `6800`   | 6800, 6801, 6803, 68HC11 |
| `6809`   | 6809, 6309, Turbo9, Konami-2, 68HC12 |
| `arm`    | ARMv1–ARMv4 (including Thumb mode) |
| `c16x`   | C16x / ST10 |
| `hans`   | HANS project (32-bit FPGA CPU) |
| `jagrisc`| Jaguar RISC (GPU and DSP) |
| `m68k`   | M680x0 family (including M6888x, M68851, CPU32, Apollo 68080), ColdFire (V2/V3/V4/V4e) |
| `pdp11`  | PDP-11 (including EIS, FIS) |
| `ppc`    | PowerPC family (POWER, 40x, 440, 460, 6xx, 7xx, 7xxx, 860, Book-E, e300, e500) |
| `qnice`  | QNICE (16-bit FPGA CPU) |
| `spc700` | Sony SPC700 |
| `sweet16`| Steve Wozniak’s Sweet16 pseudo-CPU |
| `tr3200` | TR3200 (virtual CPU from 0x10c) |
| `unsp`   | SunPlus μnSP (ISA V1.0 and V1.1) |
| `vidcore`| Raspberry Pi VideoCore IV |
| `x86`    | IA-32 8/16/32-bit (AT&T / GNU-as style syntax) |
| `z80`    | Z80 family (Z80, 8080, 8085, GBZ80, 64180, RCM2/3/4k) |
| `test`   | Test / dummy backend |

## Supported Syntax Modules

| Module     | Style |
|------------|-------|
| `std`      | Standard (GNU-as style) |
| `mot`      | Motorola / Freescale 68k (Devpac-compatible) |
| `madmac`   | Atari MadMac (6502, 68k, Jaguar) |
| `oldstyle` | Old 8-bit style (6502, 680x, Z80, etc.) |
| `test`     | Test syntax |

## Supported Output Formats (selected)

Common formats include (availability depends on the CPU module):

- ELF (ARM, JRISC, M68k, PowerPC, x86)
- a.out (JRISC, M68k, x86)
- Amiga hunk (M68k)
- Atari TOS executable / DRI object (M68k)
- Sharp X68000 Xfile / Zfile (M68k)
- COFF (ARM, M68k, PowerPC, x86)
- o65 relocatable (6502, 65816)
- Raw binary
- Motorola S-Record
- Intel Hex
- Commodore PRG, Atari COM, Apple DOS binary, Oric TAP (6502)
- Tandy Color Computer / Dragon DOS (6809)
- VOBJ (proprietary portable object format)
- Test / debugging output (default)
- C `#define` output
- And others (AOF, EHF, GST, MOS paper tape, Wozmon, PGX/PGZ, etc.)

Use `-F<format>` to select the output module. See the manual for the full list and options.

## Building from Source

Choose a Makefile that matches your host, or use CMake.

### Available Makefiles

- `Makefile` — standard Unix / GCC
- `Makefile.68k` — AmigaOS 68020 with vbcc
- `Makefile.Cygwin` — Windows via Cygwin / MinGW
- `Makefile.Haiku` — Haiku (does not link libm)
- `Makefile.MiNT` — Atari MiNT 68020 with vbcc
- `Makefile.MOS` — MorphOS with vbcc
- `Makefile.OS4` — AmigaOS 4 with vbcc
- `Makefile.PUp` — PowerUp with vbcc
- `Makefile.TOS` — Atari TOS 68000 with vbcc
- `Makefile.Win32` — Windows with MSVC
- `Makefile.Win32FromLinux` — cross-compile Windows binary on Linux
- `Makefile.WOS` — WarpOS with vbcc

### Building with make

```bash
make CPU=m68k SYNTAX=mot
```

The resulting binary is named `vasm<CPU>_<SYNTAX>[_<HOST>]`.

### Building with CMake

```bash
mkdir build && cd build
cmake -DVASM_CPU=m68k -DVASM_SYNTAX=mot ..
make
```

### Example binaries

| Command                              | Resulting binary          |
|--------------------------------------|---------------------------|
| `make CPU=6502 SYNTAX=oldstyle`      | `vasm6502_oldstyle`       |
| `make CPU=m68k SYNTAX=mot`           | `vasmm68k_mot`            |
| `make CPU=z80 SYNTAX=oldstyle`       | `vasmz80_oldstyle`        |
| `make CPU=ppc SYNTAX=std`            | `vasmppc_std`             |

Binaries are self-contained; no additional runtime files are required. To use with [vbcc](http://sun.hasenbraten.de/vbcc/), copy the binary into `vbcc/bin`.

## Usage

```bash
vasm<cpu>_<syntax> [options] [sourcefile]
```

If no source file is given, vasm reads from stdin until EOF.

Important common options (see the manual for the complete list and module-specific options):

- `-o <file>` — output file
- `-F<format>` — output format (e.g. `-Felf`, `-Fbin`, `-Fhunk`)
- `-L <listfile>` — generate listing
- `-Dname[=value]` — define a symbol
- `-pic` — generate position-independent code
- `-quiet` / `-w` — warning control
- `-showopt` — show optimizations performed

## Authors and Contact

- **Volker Barthelmann** — vb@compilers.de  
- **Frank Wille** — frank@phoenix.owl.de  

For issues specific to a CPU, syntax, or output module, contact the module author first when possible.

## License

vasm is copyright 2002–2026 by Volker Barthelmann.

This archive may be redistributed without modifications and used for non-commercial purposes.  
An exception for commercial usage is granted when the target CPU is M68k and the target OS is AmigaOS; resulting binaries may be distributed commercially without further licensing.  
In all other cases you need written consent.

Certain modules may fall under additional copyrights.

### AI / Contribution Policy

> vasm is human-made software, and at the moment it is our goal to preserve this characteristic. Please keep this in mind if you consider contributing (especially regarding use of generative AI). If your contribution might contain code not written by yourself, please consult us beforehand to check whether we consider it admissible.

This statement applies to submissions on the official homepage. Mirror repositories (such as the GitHub mirror) generally do not accept external pull requests.

## Resources

- Official website: http://sun.hasenbraten.de/vasm/
- Full manual (HTML / PDF): see links at the top of this document
- Related projects: [vbcc](http://sun.hasenbraten.de/vbcc/) (C compiler), [vlink](http://sun.hasenbraten.de/vlink/) (linker)

---

*This README summarizes publicly available information from the official vasm homepage and manual. For the most authoritative and up-to-date details, always refer to http://sun.hasenbraten.de/vasm/.*
