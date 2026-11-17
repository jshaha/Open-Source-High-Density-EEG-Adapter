# Open-Source High-Density EEG Adapter

An open-source hardware project to connect a standard high-density EEG cap (Brain Products R-Net) to Intan RHD headstages, streaming into the [Open Ephys](https://open-ephys.org/) acquisition system for human scalp EEG.

Built by [Neurotech@Berkeley](https://neurotech.studentorg.berkeley.edu/) in collaboration with NeurotechX (Morgan Hough).

## What this is

Commercial high-density EEG systems are closed and expensive. Open Ephys is an open acquisition platform, but its headstages are built for implanted probes in animals, not scalp EEG. This project builds the missing piece: the hardware that bridges a standard EEG cap to the Intan/Open Ephys world, so researchers can record high-density human EEG on an open, lower-cost stack.

The work is reproduction-first, based on the published Open Ephys + EEG breakout board (Black, Voigts et al., 2017, *J. Neural Eng.*), whose design files are at [open-ephys/eeg-adapter](https://github.com/open-ephys/eeg-adapter). We reproduce and extend that design, and release everything openly.

## The signal chain

```
EEG cap (R-Net)
   -> [adapter board]        <- bridges cap connector to headstage
   -> [isolation board]      <- electrically isolates the subject
   -> Intan RHD headstage    <- amplifies + digitizes
   -> Open Ephys acquisition board
   -> USB -> computer -> Open Ephys GUI
```

The two boards in this repo (the adapter and the isolation board) are what we design and fabricate. Everything else is commercial, off-the-shelf hardware.

## Boards

### 1. Cap-to-headstage adapter
Routes the EEG cap's connector to the Intan headstage inputs, carrying the signal channels plus reference and ground. Reproduces and adapts the open-ephys/eeg-adapter design.

*Status: schematic in progress.*

### 2. Electrical isolation board
Sits between the Open Ephys acquisition board and the headstage to keep the subject safely isolated from mains-powered equipment. Isolates the SPI signal path (clock, chip select, MOSI toward the headstage; MISO back) and supplies isolated power to the headstage side.

- LVDS <-> CMOS conversion via ADN4664 (receiver) and ADN4663 (driver)
- Digital isolation across the barrier via ADuM isolators
- Isolated power via a Murata NMV0505-series DC-DC converter

*Status: fabricated (bare board; ICs not yet soldered). The original ADuM3440 isolators were marked obsolete by Analog Devices and swapped for the recommended drop-in replacement, ADuM340E1, in the KiCad layout. End-to-end timing and startup behavior still need bench verification.*

Manufacturing files for the isolation board are in `headstage_2_layer (1).zip` (KiCad-exported Gerbers + drill files, 2-layer).

## Hardware

- **EEG cap:** Brain Products R-Net (commercial). The exact connector depends on which amplifier variant the net was built for (BrainAmp / actiCHamp Plus / LiveAmp); confirm the variant before finalizing the adapter. We are also looking into supporting an EGI (Electrical Geodesics) high-density net as an alternative cap option.
- **Headstage:** Intan RHD, bought through White Mountain Systems (Intan no longer sells direct). Two 64-channel headstages (C3315) are the recommended route over the single 128-channel board (C3316): they use friendlier Omnetics connectors, carry REF/GND on the connector, and match the reference design. For EEG, the R0 jumper is removed so REF and GND are independent.
- **Acquisition:** Open Ephys acquisition board + GUI.

## Open items

- Obtain the R-Net connector pinout from Brain Vision LLC, or measure it directly (it is not publicly documented).
- Confirm the R-Net variant and connector.
- Bench-verify the isolation board: supply rails under headstage load, SPI integrity through the isolation barrier, leakage.
- Finalize and fabricate the adapter board.
- Bring-up and validation: continuity, channel mapping in the Open Ephys GUI, adapter-added noise and crosstalk, and real EEG capture (e.g. alpha rhythm, eyes closed).

## Safety

This hardware connects to a person. The acquisition PC must be run on battery or an isolated supply, and the isolation board exists to protect the subject. This is research hardware, not a certified medical device.

## License

This project is intended to be released as open-source hardware. The upstream open-ephys/eeg-adapter repository does not state a license; appropriate licensing (e.g. CERN OHL) is being confirmed before release.

## Team

Neurotech@Berkeley, Fall 2026. Project managers: Jacob Shahatit, Sean Isomatsu. Client: NeurotechX (Morgan Hough).
