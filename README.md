# titantools-macos

macOS **CLI** flash helper for SpacemiT **K3** / Banana Pi **BPI-SM10 (CoM260)**.

> **This is not a port of Titantools.** SpacemiT’s Titantools GUI is closed source and ships Windows / Linux AppImage only. There is nothing public to fork.
>
> This repo is the practical Mac equivalent: Homebrew **`fastboot`** + SpacemiT’s open flash flow from [`K3-Ubuntu-Images`](https://github.com/spacemit-com/K3-Ubuntu-Images).

## Install

```bash
brew install android-platform-tools zstd python
python3 -m venv .venv && .venv/bin/pip install pyyaml

git clone https://github.com/opensolvers/titantools-macos.git
cd titantools-macos
chmod +x bin/flash-k3
./bin/flash-k3 check
```

## Board setup (BPI-SM10)

1. Power from the **DC jack** (12 V / 5 A or 19 V / 2.37 A) — USB-C is **not** a power input.
2. Hold **FC_REC**, apply power (or press **RST**), release **FC_REC**.
3. Connect **USB-C** to the Mac (UFP / download mode).
4. Optional: USB-TTL on the 12-pin header at **115200** for console (not used for flashing).

```bash
./bin/flash-k3 devices
```

## Flash

### A) Titantools package (preferred on Mac)

Download the Ubuntu **`.tar.gz`** (Titantools format) from
[spacemit-com/K3-Ubuntu-Images releases](https://github.com/spacemit-com/K3-Ubuntu-Images/releases), then:

```bash
./bin/flash-k3 flash --titan ~/Downloads/ubuntu-26.04-preinstalled-desktop-riscv64.tar.gz
```

Or point at an already-extracted directory that contains `fastboot.yaml`, `u-boot.itb`, and `factory/FSBL.bin`.

### B) Raw GPT image (`.img` / `.img.zst`)

You also need BootROM staging payloads (`u-boot.itb`, `FSBL.bin`) from a Titantools package or a gadget/u-boot install tree:

```bash
export FIRMWARE_DIR=~/Downloads/ubuntu-titan-extracted   # has u-boot.itb + factory/FSBL.bin
./bin/flash-k3 flash --image ~/Downloads/k3-ubuntu/ubuntu-26.04-preinstalled-desktop-riscv64.img.zst
```

## What works / what does not

| Tool | macOS |
|---|---|
| Titantools GUI | No (proprietary Win/Linux) |
| Titantools `flashserver` binary | No (Linux ELF) |
| Stock `fastboot` (platform-tools) | Yes — this repo |
| SD-card `dd` of the desktop `.img` | Separate path; not BootROM USB flash |

## Layout

```
bin/flash-k3                 # macOS entrypoint
src/image_flash_macos.py     # flash driver (path fixes vs upstream)
vendor/spacemit-k3-ubuntu-images/   # vendored fastboot.yaml + partition tables + upstream script
```

## Upstream attribution

Flash protocol files and the original `image_flash.py` come from
[spacemit-com/K3-Ubuntu-Images](https://github.com/spacemit-com/K3-Ubuntu-Images).
That repository currently publishes **no SPDX license**; we vendor for interoperability and keep copies under `vendor/` with clear provenance. Our wrapper scripts are Apache-2.0.

## Disclaimer

Flashing can brick storage contents. Use at your own risk. Prefer a known-good Titantools `.tar.gz` from SpacemiT when available.
