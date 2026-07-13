# QMC5883P Compass Arduino Library

Based on [QMC5883LCompass](https://github.com/mprograms/QMC5883LCompass)

---
## Overview
QMC5883P Compass is a Arduino library for using QMC5583P series chip boards as a compass.


## QMC5883P vs QMC5883L — Key Differences

Both are 3‑axis AMR magnetic sensors from QST (Shanghai QST), sharing the same LGA‑16 footprint but **not register‑compatible**. Choose wisely depending on your application.

### 📊 Specification Comparison

| Feature                | QMC5883L                              | QMC5883P                                 |
|------------------------|---------------------------------------|------------------------------------------|
| **Full scale range**   | ±2 G / ±8 G                          | ±2 G / ±8 G / ±12 G / **±30 G**        |
| **Max ODR**            | 200 Hz                               | **1.5 kHz**                             |
| **Sensitivity @ ±8 G** | 3000 LSB/G (typ)                     | 3750 LSB/G (typ)                        |
| **Noise floor**        | ~0.6 µT RMS                          | ~0.3 µT RMS @100 Hz (better AFE)        |
| **Temperature sensor** | ✅ Built‑in                           | ❌ **None** (reads always zero)          |
| **Self‑test**          | ❌                                    | ✅ Internal coil, bit in CTRL_REG1       |
| **OTP trim loading**   | Not needed                            | ✅ Required after POR (registers 0x2C‑0x2F) |
| **I²C address**        | 0x0D (7‑bit)                         | 0x0D (7‑bit, same)                       |
| **Register map**       | Original L layout (0x00‑0x0A)         | **Completely different** – see datasheet |
| **Suspend current**    | ~20‑30 µA                            | 22 µA                                    |
| **Max active current** | ODR‑dependent (~0.5 mA typ)          | Up to 2.2 mA @ 1.5 kHz ODR              |
| **Common module**      | GY‑273 (widespread)                  | Less common, newer batches               |

### ⚠️ Migration Gotchas

1. **Register maps are totally different.**  
   A driver written for QMC5883L will **not work** with QMC5883P without a full rewrite or conditional branching.

2. **No temperature output on QMC5883P.**  
   If your heading algorithm relies on temp compensation, you must add an external temperature sensor when using the P variant.

3. **OTP trim loading is mandatory on QMC5883P.**  
   After power‑on reset, write vendor‑specific keys (e.g., `0xA5, 0x3F, 0x1C, 0x88` – confirm with your exact datasheet) to registers 0x2C‑0x2F. Skipping this step causes X/Y/Z offset drift > ±50 µT.

4. **Higher field tolerance on QMC5883P (±30 G).**  
   Useful near strong magnets or motor fields, but absolute accuracy inside ±8 G is slightly worse than the L variant.

5. **QMC5883P supports 1.5 kHz ODR.**  
   Ideal for high‑speed applications like drone attitude estimation or fast‑rotating platforms.

6. **Most cheap “QMC5883” modules are actually the L variant.**  
   Unless explicitly stated by the seller, assume you have a QMC5883L.

## 📎 References

- QMC5883L datasheet – LCSC / QST  
- QMC5883P datasheet Rev C – QST  
- GitHub: [`robert-hh/QMC5883`](https://github.com/robert-hh/QMC5883) – separate `qmc5883l.py` / `qmc5883p.py` drivers showing the register map split
