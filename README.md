# Description
`cpu_rec` is a tool that recognizes cpu instructions
in an arbitrary binary file.
It can be used as a standalone tool, or as a plugin for binwalk
(https://github.com/devttys0/binwalk).

# Installation instructions
## Standalone tool
1. Copy `cpu_rec.py` and `cpu_rec_corpus` in the same directory.
2. If you don't have the `lzma` module installed for your python (this
   tool works either with python3 or with python2 >= 2.4) then you
   should `unxz` the corpus files in `cpu_rec_corpus`.
3. If you want to enhance the corpus, you can add new data in the
   corpus directory. If you want to create your own corpus, please
   look at the method `build_default_corpus` in the source code.

## For use as a binwalk module
Same as above, but the installation directory must be the binwalk
module directory: `$HOME/.config/binwalk/modules`.

You'll need a recent version of binwalk, that includes the patch
provided by https://github.com/devttys0/binwalk/pull/241 .

# How to use the tool
## As a binwalk module
Add the flag `-%` when using binwalk.

Be patient. Waiting a few minutes for the result is to be expected.
On my laptop the tool takes 25 seconds and 1 Gb of RAM
to create the signatures for 70 architectures, and then the analysis
of a binary takes one minute per Mb.
If you want the tool to be faster, you can remove some architectures,
if you know that your binary is not one of them (typically Cray or
MMIX are not found in a firmware).

## As a standalone tool
Just run the tool, with the binary file(s) to analyze as argument(s)
The tool will try to match an architecture for the whole file, and
then to detect the largest binary chunk that corresponds to a CPU
architecture; usually it is the right answer.

If the result is not satisfying, prepending twice `-v` to the arguments
makes the tool very verbose; this is helpful when adding a new
architecture to the corpus.

If https://github.com/LRGH/elfesteem is installed, then the
tool also extract the text section from ELF, PE, Mach-O or COFF
files, and outputs the architecture corresponding to this section;
the possibility of extracting the text section is also used when
building a corpus from full binary files.

Option `-d` followed by a directory dumps the corpus in that directory;
using this option one can reconstruct the default corpus.

# Examples
Running the tool as a binwalk module typically results in:
```
shell_prompt> binwalk -% corpus/PE/PPC/NTDLL.DLL corpus/MSP430/goodfet32.hex

Target File:   .../corpus/PE/PPC/NTDLL.DLL
MD5 Checksum:  d006a2a87a3596c744c5573aece81d77

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             None (size=0x5800, entropy=0.620536)
22528         0x5800          PPCel (size=0x4c800, entropy=0.737337)
335872        0x52000         None (size=0x1000, entropy=0.720493)
339968        0x53000         IA-64 (size=0x800, entropy=0.491011)
342016        0x53800         None (size=0x22000, entropy=0.727501)

Target File:   .../corpus/MSP430/goodfet32.hex
MD5 Checksum:  4b295284024e2b6a6257b720a7168b92

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             MSP430 (size=0x5200, entropy=0.472185)
20992         0x5200          None (size=0xe00, entropy=0.467086)
```

We can notice that during the analysis of `PPC/NTDLL.DLL`
a small chunk has been identified as `IA-64`.
This is an erroneous detection, due to the fact that
the IA-64 architecture has statistical properties similar
to data sections.

If the entropy value is above 0.9, it is probably encrypted or compressed
data, and therefore the result of cpu_rec should be meaningless.

## More documentation

The tool has been presented at
[SSTIC 2017](https://www.sstic.org/2017/presentation/cpu_rec/),
with a full paper describing why this technique has been used for
the recognition of architectures. A video of the presentation
and the slides are available.

This presentation is in French. Please raise an
[issue](https://github.com/airbus-seclab/cpu_rec/issues/new)
if you want a translation in English.

# Known architectures in the default corpus
[`68HC08`](https://en.wikipedia.org/wiki/Freescale_68HC08)
[`68HC11`](https://en.wikipedia.org/wiki/Freescale_68HC11)
[`8051`](https://en.wikipedia.org/wiki/Intel_MCS-51)
[`Alpha`](https://en.wikipedia.org/wiki/DEC_Alpha)
[`ARcompact`](https://en.wikipedia.org/wiki/ARC_(processor))
[`ARM64` `ARMeb` `ARMel` `ARMhf`](https://en.wikipedia.org/wiki/ARM_architecture)
[`AVR`](https://en.wikipedia.org/wiki/Atmel_AVR)
[`AxisCris`](https://en.wikipedia.org/wiki/ETRAX_CRIS)
[`Blackfin`](https://en.wikipedia.org/wiki/Blackfin)
[`Cell-SPU`](https://en.wikipedia.org/wiki/Cell_(microprocessor))
[`CLIPPER`](https://en.wikipedia.org/wiki/Clipper_architecture)
[`CompactRISC`](https://en.wikipedia.org/wiki/CompactRISC)
[`Cray`](https://en.wikipedia.org/wiki/Cray)
[`Epiphany`](https://en.wikipedia.org/wiki/Adapteva)
[`FR-V`](https://en.wikipedia.org/wiki/FR-V_(microprocessor))
[`FR30`](http://www.fujitsu.com/downloads/MICRO/fma/pdfmcu/hm91101-cm71-10102-2e.pdf)
[`FT32`](https://en.wikipedia.org/wiki/FTDI)
[`H8-300`](https://en.wikipedia.org/wiki/H8_Family)
[`HP-Focus`](https://en.wikipedia.org/wiki/HP_FOCUS)
[`HP-PA`](https://en.wikipedia.org/wiki/PA-RISC)
[`i860`](https://en.wikipedia.org/wiki/Intel_i860)
[`IA-64`](https://en.wikipedia.org/wiki/IA-64)
[`IQ2000`](http://www.ic72.com/pdf_file/v/165699.pdf)
[`M32C`](https://www.renesas.com/en-eu/products/microcontrollers-microprocessors/m16c.html)
[`M32R`](https://www.renesas.com/en-eu/products/microcontrollers-microprocessors/m32r.html)
[`M68k`](https://en.wikipedia.org/wiki/Motorola_68000_series)
[`M88k`](https://en.wikipedia.org/wiki/Motorola_88000)
[`MCore`](https://en.wikipedia.org/wiki/M%C2%B7CORE)
[`Mico32`](https://en.wikipedia.org/wiki/LatticeMico32)
[`MicroBlaze`](https://en.wikipedia.org/wiki/MicroBlaze)
[`MIPS16` `MIPSeb` `MIPSel`](https://en.wikipedia.org/wiki/MIPS_instruction_set)
[`MMIX`](https://en.wikipedia.org/wiki/MMIX)
[`MN10300`](https://en.wikipedia.org/wiki/MN103)
[`Moxie`](http://moxielogic.org/blog/)
[`MSP430`](https://en.wikipedia.org/wiki/TI_MSP430)
[`NDS32`](http://osdk.andestech.com/index.html)
[`NIOS-II`](https://en.wikipedia.org/wiki/Nios_II)
[`OCaml`](https://en.wikipedia.org/wiki/OCaml)
[`PDP-11`](https://en.wikipedia.org/wiki/PDP-11)
[`PIC10` `PIC16` `PIC18` `PIC24`](https://en.wikipedia.org/wiki/PIC_microcontroller)
[`PPCeb` `PPCel`](https://en.wikipedia.org/wiki/PowerPC)
[`RISC-V`](https://en.wikipedia.org/wiki/RISC-V)
[`RL78`](https://www.renesas.com/en-eu/products/microcontrollers-microprocessors/rl78.html)
[`ROMP`](https://en.wikipedia.org/wiki/ROMP)
[`RX`](https://www.renesas.com/en-eu/products/microcontrollers-microprocessors/rx.html)
[`S-390`](https://en.wikipedia.org/wiki/IBM_System/390_ES/9000_Enterprise_Systems_Architecture_ESA_family)
[`SPARC`](https://en.wikipedia.org/wiki/SPARC)
[`STM8`](https://en.wikipedia.org/wiki/STM8)
[`Stormy16`](https://sourceware.org/cgen/gen-doc/xstormy16.html)
[`SuperH`](https://en.wikipedia.org/wiki/SuperH)
[`TILEPro`](https://en.wikipedia.org/wiki/TILEPro64)
[`TLCS-90`](https://en.wikipedia.org/wiki/Toshiba_TLCS#90)
[`TMS320C2x` `TMS320C6x`](https://en.wikipedia.org/wiki/Texas_Instruments_TMS320)
[`V850`](https://en.wikipedia.org/wiki/V850)
[`VAX`](https://en.wikipedia.org/wiki/VAX)
[`Visium`](https://www.slideshare.net/AdaCore/controls-and-dataservices)
[`WE32000`](https://en.wikipedia.org/wiki/Bellmac_32)
[`X86-64`](https://en.wikipedia.org/wiki/X86-64)
[`X86`](https://en.wikipedia.org/wiki/X86)
[`Xtensa`](https://en.wikipedia.org/wiki/Tensilica)
[`Z80`](https://en.wikipedia.org/wiki/Zilog_Z80)
[`#6502#cc65`](https://github.com/cc65/cc65)

# Licence
## The tool
The `cpu_rec.py` file is licenced under a Apache Licence, Version 2.0.

## The default corpus
The files in the default corpus have been built from various sources.
The corpus is a collection of various compressed files, each compressed
file is dedicated to the recognition of one architecture and is made by
the compression of the concatenation of one or many binary chunks, which
come from various origins and have various licences.
Therefore, the default corpus is a composite document, each sub-document
(the chunk) being redistributed under the appropriate licence.

The origin of each chunk is described in `cpu_rec.py`, in the function
`build_default_corpus`. The licences are:
- files `libgmp.so`, `libc.so`, `libm.so` come from Debian binary
distributions and are distributed under GPLv2 (and LGPLv3 for recent
versions of `libgmp`) and the source code is available from
http://archive.debian.org/.
- `busybox` binaries come from https://busybox.net/downloads/binaries/
and are distributed under GPLv2.
- `C-Kermit` binaries come from ftp://kermit.columbia.edu/kermit/bin/
and are distributed under GPLv2
(according to ftp://kermit.columbia.edu/kermit/archives/COPYING but
the status of each binary is not always clear).
- all files identified in `build_default_corpus` as part of the
`CROSS_COMPILED` subdirectory have been built by myself.
The corresponding source code are
`zlib` (from http://zlib.net/, distributed under the zlib licence)
or `libjpeg` (from http://www.ijg.org/, distributed under an unknown licence)
or some other code based on public sources
(e.g. https://anonscm.debian.org/cgit/pkg-games/bsdgames.git/tree/arithmetic/arithmetic.c modified to work with SDCC compilers).
- The `camlp4` binary is built from https://github.com/ocaml/camlp4
and distributed under LGPLv2.
- The binary for TMS320C2x comes from
https://github.com/slavaprokopiy/Mini-TMS320C28346/blob/master/For_user/C28346_Load_Program_to_Flash/Debug/C28346_Load_Program_to_Flash.out
where it is distributed under an unknown licence.
- The binary for RISC-V comes from https://riscv.org/software-tools/
distributed under GPLv2 and can downloaded at
https://github.com/radare/radare2-regressions/blob/master/bins/elf/analysis/guess-number-riscv64
- The binaries for PIC10 and PIC16 come from http://www.pic24.ru/doku.php/en/osa/ref/examples/intro
where they are distributed under an unknown licence.
- The binary for PIC18 comes from https://github.com/radare/radare2-regressions/blob/master/bins/pic18c/FreeRTOS-pic18c.hex
where it seems to be distributed under GPLv3 (or later).
- The binary for PIC24 comes from https://raw.githubusercontent.com/mikebdp2/Bus_Pirate/master/package_latest/BPv4/firmware/bpv4_fw7.0_opt0_18092016.hex
distributed under Creative Commons Zero.
