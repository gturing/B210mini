# BlackSDR B210mini

**B210-class 2×2 MIMO performance in a B205mini-class footprint.**

BlackSDR B210mini is a compact USB 3.0 software-defined radio that combines the RF capability of the Ettus USRP B210 with the small form factor of the USRP B205mini. The RF front-end is simplified, the board is shrunk, and the 2×2 MIMO / AD9361-class performance of the B210 is kept.

It enumerates as a **USRP B210** under [UHD](https://github.com/EttusResearch/uhd) and works with the existing B210 software stack (GNU Radio, MATLAB, srsRAN, OpenAirInterface, SDR++, SDRangel, and others).

> This is **not** an official Ettus Research / NI product. It is a B210-compatible design intended for research, education, and experimentation.

---

## Why B210mini exists

The original **USRP B210** is the workhorse 2×2 MIMO USB SDR: AD9361, 70 MHz–6 GHz, up to 56 MHz instantaneous bandwidth, full duplex. It is also relatively large (~97 × 155 mm) and carries a more elaborate RF layout.

The **USRP B205mini** solved the size problem (~50 × 84 mm, business-card class) by simplifying the board — but it is a **1×1 SISO** radio (AD9364) and does not keep the B210’s dual-channel coherent MIMO.

**BlackSDR B210mini** is the combination that those two boards never were:

| Design goal | Taken from | Result |
|---|---|---|
| 2 TX + 2 RX, coherent 2×2 MIMO | USRP B210 | Dual-channel full-duplex performance kept |
| Wideband direct-conversion RFIC | USRP B210 (AD9361 class) | 70 MHz–6 GHz, up to 56 MHz IBW |
| Small board, field-friendly USB power | USRP B205mini | Pocket / OEM / UAV-payload size |
| Fewer RF stages, shorter traces | Redesign | Simplified RF front-end, lower parts count |
| Host software | USRP B210 UHD image | Drop-in B210 workflow |

In short: **B210 performance, B205 size, simpler RF.**

```
  USRP B210                 USRP B205mini              BlackSDR B210mini
  ┌─────────────────┐       ┌──────────┐               ┌────────────┐
  │ 2×2 MIMO        │       │ 1×1 SISO │               │ 2×2 MIMO   │
  │ AD9361          │  +    │ Mini PCB │   ──opt──►    │ Mini PCB   │
  │ Full RF chain   │       │ USB 3.0  │               │ Simplified │
  │ ~97 × 155 mm    │       │ ~50×84 mm│               │ RF frontend│
  └─────────────────┘       └──────────┘               └────────────┘
```

---

## Features

- **2×2 MIMO**, full duplex (2 TX / 2 RX), coherent channels
- **70 MHz – 6 GHz** continuous coverage (typical; some boards tune from ~50 MHz)
- **Up to 56 MHz** instantaneous bandwidth (61.44 MS/s quadrature)
- **12-bit** ADC / DAC
- **USB 3.0 Micro B**, host-powered for most use
- Onboard **Artix-7 XC7A100T** FPGA (~100k logic cells) for the B210 datapath
- **AD9361-class** integrated transceiver
- Simplified RF front-end versus the full-size B210
- External **10 MHz** reference and **1 PPS** inputs for multi-radio sync
- Onboard **±0.5 ppm VCTCXO** (typical) — usable without an external GPSDO for many lab tasks
- Works as a standard **UHD `type=b210`** device after the matching FPGA image is installed

---

## Hardware specifications

Values below describe the intended B210mini class. Exact figures can vary slightly by board revision.

### Radio

| Item | Spec |
|---|---|
| Architecture | 2R2T, full duplex, coherent 2×2 MIMO |
| Frequency range | 70 MHz – 6 GHz (typical) |
| Instantaneous bandwidth | Up to 56 MHz (1×1); ~30.72 MHz per channel in 2×2 |
| Max sample rate | 61.44 MS/s |
| ADC / DAC | 12-bit / 12-bit |
| ADC SFDR | ~78 dBc (typical) |
| TX power | >10 dBm typical mid-band (lower near 6 GHz) |
| RX noise figure | < 8 dB typical |
| IIP3 | ~−20 dBm at typical NF |
| Tuning | Independent TX / RX LO; MIMO channels share LO per direction |

### Digital / clock / I/O

| Item | Spec |
|---|---|
| FPGA | Xilinx / AMD Artix-7 **XC7A100T** |
| Host interface | USB 3.0 SuperSpeed, Micro B |
| Reference clock | Onboard VCTCXO, ±0.5 ppm typical |
| Sync inputs | 10 MHz ref + 1 PPS (IPEX / U.FL class connectors on many revs) |
| GPIO / JTAG | Board-revision dependent |

### Physical

| Item | Spec |
|---|---|
| Board size | ~90 × 63 mm class (B205-like, far smaller than B210) |
| Weight | ~50 g (bare board, typical) |
| Power | USB 3.0 bus power; ~3.8 W max typical |
| Original B210 size (for comparison) | 97 × 155 × 15 mm, ~350 g |
| Original B205mini size (for comparison) | 50 × 84 mm |

### Comparison

| | USRP B210 | USRP B205mini | **BlackSDR B210mini** |
|---|---|---|---|
| Channels | 2×2 MIMO | 1×1 SISO | **2×2 MIMO** |
| RFIC | AD9361 | AD9364 | **AD9361 class** |
| FPGA (original / this board) | Spartan-6 LX150 | Spartan-6 LX150 (i) | **Artix-7 XC7A100T** |
| IBW | 56 MHz | 56 MHz | **56 MHz** |
| Frequency | 70 MHz–6 GHz | 70 MHz–6 GHz | **70 MHz–6 GHz** |
| Host | USB 3.0 | USB 3.0 | **USB 3.0 Micro B** |
| Form factor | Full B-series board | Mini | **Mini** |
| RF front-end | Full B210 layout | Mini SISO layout | **Simplified 2×2 layout** |

---

## Repository contents

This repository is a getting-started kit for BlackSDR B210mini (and compatible LibreSDR / clone boards that use **XC7A100T + AD9361** and enumerate as B210).

Typical files:

```
.
├── README.md
├── usrp_b210_fpga.bin      # FPGA image for Artix-7 XC7A100T B210-compatible boards
└── docs/                   # optional schematics, photos, notes
```

A known working public FPGA image for **XC7A100T + AD9361** B210-class clones is maintained here:

- https://github.com/gturing/B210mini

Use that image (or the copy shipped in this repo) if UHD’s stock `usrp_b210_fpga.bin` does not configure the Artix-7 device.

---

## Software setup (Linux)

Tested pattern: Ubuntu / Debian, UHD 4.x–7.x.

### 1. Install UHD

```bash
sudo add-apt-repository ppa:ettusresearch/uhd
sudo apt update
sudo apt install libuhd-dev uhd-host python3
```

### 2. Download stock UHD images

```bash
sudo /usr/lib/uhd/utils/uhd_images_downloader.py
```

Images usually land in one of:

- `/usr/share/uhd/images`
- `/opt/local/share/uhd/images`

Confirm with:

```bash
uhd_config_info --images-dir
```

### 3. Install the B210mini FPGA image

Stock Ettus B210 bitstreams target Spartan-6. BlackSDR B210mini uses **Artix-7 XC7A100T**, so replace the B210 FPGA image:

```bash
# example: image from the LibreSDR B210 XC7A100T tree
wget https://github.com/gturing/B210mini/raw/main/usrp_b210_fpga.bin

IMAGES_DIR="$(uhd_config_info --images-dir)"
sudo cp usrp_b210_fpga.bin "$IMAGES_DIR/usrp_b210_fpga.bin"
```

Keep a copy of the original file if you also use a genuine Ettus B210 on the same machine.

### 4. Probe the device

```bash
uhd_usrp_probe
```

A healthy board looks like this:

```
[INFO] [B200] Detected Device: B210
[INFO] [B200] Loading FPGA image: .../usrp_b210_fpga.bin
[INFO] [B200] Operating over USB 3.
[INFO] [B200] Initialize CODEC control...
[INFO] [B200] Initialize Radio control...
[INFO] [B200] Register loopback test passed
```

If the probe hangs on FPGA load, the bitstream does not match the FPGA (wrong family or density).

### 5. USB permissions (optional)

```bash
sudo cp /lib/udev/rules.d/uhd-usrp.rules /etc/udev/rules.d/ 2>/dev/null || true
sudo udevadm control --reload-rules
sudo udevadm trigger
```

---

## Using the radio

UHD sees the board as a normal B210:

```bash
# identify
uhd_find_devices

# RX example
uhd_rx_cfile --args "type=b210" -f 915e6 -r 2e6 -g 40 /tmp/cap.dat

# GNU Radio
#  Use UHD: USRP Source / Sink, device args: type=b210
```

Useful device args:

```
type=b210
num_recv_frames=128
master_clock_rate=16e6
```

For 2×2 MIMO, request two channels on the USRP source/sink (`num_channels=2`) and keep sample rate / LO consistent across the pair (B210-class radios share RX LO and TX LO).

---

## Software compatibility

| Stack | Notes |
|---|---|
| UHD 3.15 – 4.10 | Primary driver. Board appears as `B210`. |
| GNU Radio | UHD blocks |
| MATLAB / Simulink | Communications Toolbox Support Package for USRP |
| srsRAN / OpenAirInterface | B210 device type |
| SDR++, SDRangel, GQRX | Via SoapyUHD or native UHD |
| Pothos, Inspectrum, custom C++/Python | libuhd |

FPGA rebuilds (only if you change HDL) have been demonstrated on **Vivado ML Standard 2021.1** targeting **Artix-7 XC7A100T**. 
---

## Applications

- LTE / 5G NR / Wi-Fi PHY lab work (srsRAN, OAI)
- Spectrum monitoring and recording
- GNSS / ADS-B / AIS / broadcast / ISM experiments
- LoRa / LPWAN receive and research
- Two-channel direction finding and coherent MIMO demos
- Teaching SDR, DSP, and wireless comms
- Embedded / portable setups where a full-size B210 does not fit

---

## Design notes

**What was kept from B210**

- Dual independent analog channels and 2×2 MIMO
- AD9361-class instantaneous bandwidth and frequency span
- UHD B210 software identity, so existing flowgraphs keep working
- External 10 MHz / 1 PPS synchronization path

**What was taken from B205mini**

- Mini PCB outline and USB-centric power
- Short interconnects and a board that can live next to a laptop or inside a small enclosure
- Field-use density: antennas, USB-C, done

**What was simplified**

- RF front-end parts count and routing versus the original full-size B210
- Connector and power delivery toward a single USB-C host cable
- FPGA moved from Spartan-6 to Artix-7 XC7A100T (modern tool flow, clone-friendly bitstream)

The trade is mechanical and analog, not architectural: you still program it like a B210.

---

## Troubleshooting

| Symptom | Likely cause | What to try |
|---|---|---|
| `uhd_usrp_probe` never finishes loading FPGA | Wrong bitstream (Spartan-6 image on Artix-7) | Install the XC7A100T `usrp_b210_fpga.bin` |
| Device not found | udev / cable / USB 2.0-only hub | Use a USB 3.0 port; check `lsusb` |
| Drops / `U` overflow | USB controller or CPU | Lower sample rate; USB 3.0 host; more recv frames |
| Weak TX / high NF | Antenna, gain, or simplified front-end limits | Check SMA/IPEX jumpers; sweep gain; add LNA/PA if needed |
| MIMO channels not coherent | Different rates or two LOs requested | Same `master_clock_rate` and center frequency on both channels |
| Board resets under 2×2 + high rate | USB power budget | Powered USB 3.0 hub or external 5 V if the revision provides it |

---

## Disclaimer

- BlackSDR B210mini is a **B210-compatible** third-party design. Ettus Research, NI, Analog Devices, and AMD/Xilinx are not affiliated with this repository.
- FPGA images and host instructions are provided **AS-IS**, without warranty.
- RF regulations still apply. Transmit only where you are licensed, and keep out-of-band emissions legal.
- If a board does not match **XC7A100T + AD9361**, do not force the B210mini bitstream onto it.

---

## References

- Ettus USRP B200 / B210 / B200mini / B205mini overview: https://kb.ettus.com/B200/B210/B200mini/B205mini/B206mini
- UHD: https://github.com/EttusResearch/uhd

---

## License

Documentation in this repository is provided for use with BlackSDR B210mini hardware.

FPGA bitstreams, UHD, and vendor IP remain under their own licenses. Do not assume Ettus / Xilinx / ADI files in a clone tree are redistributable without checking the original terms.

---


