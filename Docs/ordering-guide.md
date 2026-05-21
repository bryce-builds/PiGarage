# PiGarage Ordering & Assembly Guide

## 1. Prepare Production Files

Compress all files in the `Production/` folder into a `.zip` or `.rar`, **excluding** the position file (`PiGarage-all-pos.xlsx`). The zip should contain only Gerber and drill files.

## 2. Order PCBs with Assembly

1. Go to [JLCPCB](https://cart.jlcpcb.com/quote) and upload your compressed Gerber file.
2. The default settings should be fine. The main ones to confirm:
   - **Layers:** 2
   - **Thickness:** 1.6mm
   - **Surface Finish:** HASL (lead-free)
   - **Copper Weight:** 1oz
3. Scroll down and select **PCB Assembly**.
4. Set **PCBA Qty** to **2** (this is the minimum). Feel free to share the spare with someone else wanting to use a Pi as their head unit!

## 3. Upload BOM and CPL

- **BOM:** Upload `BOM/BOM.xlsx`
- **CPL (Component Placement List):** Upload `Production/PiGarage-all-pos.xlsx`

## 4. Check Component Orientation

In the **Component Placements** preview, you will need to ensure the components are oriented correctly. Line up each component's dot/pin-1 marker with the corresponding dot on the board. Rotate any that don't match.

## 5. Hand-Solder Remaining Parts

Not all components are available through JLCPCB assembly. Purchase the remaining parts from DigiKey (or your preferred distributor) and hand-solder them:

- **J1** — Molex Mini-Fit Jr 6-pin car harness header — DigiKey [1724470206](https://www.digikey.com/en/products/detail/molex/1724470206/5116910)
- **J2** — Molex Mini-Fit Jr 4-pin ADC analog input header — DigiKey [1724470204](https://www.digikey.com/en/products/detail/molex/1724470204/5116909)
- **J3** — PJ-320B 3.5mm audio jack
- **J4** — 40-pin Raspberry Pi GPIO header (2×20, 2.54mm)
- **J5** — JST PH 2.0mm 2-pin fan output header (B2B-PH-K-S) — optional, populate if using a cooling fan

For J5, also source the mating wire-side connector: JST **PHR-2** housing and **SPH-002T-P0.5L** contacts (both available on DigiKey). Crimp the contacts onto the fan's bare wires, insert into the housing, and plug into J5.

> **Note:** J5 is available on JLCPCB as C131337, but it is an **Extended** part — JLCPCB charges ~$3 per unique extended part as a feeder setup fee. Since J5 is a simple through-hole connector, it is cheaper to hand-solder it yourself and skip the extended part fee.

Refer to `BOM/design-notes.txt` for the full parts list with part numbers and design rationale.

## 6. Install Software

```bash
sudo mkdir -p /opt/pigarage
sudo cp Software/power_latch.py /opt/pigarage/power_latch.py
sudo cp Software/pigarage.service /etc/systemd/system/pigarage.service
sudo systemctl daemon-reload
sudo systemctl enable pigarage
sudo systemctl start pigarage
```

The service monitors ACC (BCM27) and holds the power latch (BCM25) high. When ACC drops for 3 seconds, the Pi shuts down safely and releases the latch.
