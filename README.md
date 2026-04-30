# Solar Fan Monitor 🌞

ESP32-C3 Mini projekt na monitorovanie solárneho ventilátora cez Bluetooth.

## Čo to robí
- Meria **teplotu a vlhkosť** (DHT22)
- Meria **napätie a kapacitu batérie** (napäťový delič)
- Posiela dáta cez **BLE** na telefón (každých 30 sekúnd)
- Medzi meraniami ide do **deep sleep** → minimálna spotreba

---

## ⚠️ Dôležitá oprava v schéme

**GPIO 21 nie je ADC pin na ESP32-C3!**

ADC piny na ESP32-C3 sú iba: GPIO 0, 1, 2, 3, 4, 5

→ Prepoj výstup napäťového deliča z GPIO 21 na **GPIO 4**

```
Batt+ ── R3(100kΩ) ──┬── GPIO 4 (ADC)
                     │
                   R5(100kΩ)
                     │
                    GND
```

---

## Zapojenie

### DHT22
| DHT22 pin | ESP32-C3 |
|-----------|----------|
| VCC       | 3.3V     |
| GND       | GND      |
| DATA      | GPIO 20  |

### Napäťový delič (batéria)
| Komponent | Zapojenie              |
|-----------|------------------------|
| R3 (100kΩ)| Batt+ → GPIO 4        |
| R5 (100kΩ)| GPIO 4 → GND          |

Merací rozsah: 0 – 6.6V (bezpečné pre 1S Li-ion max 4.2V ✓)

---

## Knižnice (Arduino Library Manager)
- `NimBLE-Arduino`
- `DHT sensor library` (Adafruit)
- `Adafruit Unified Sensor`

---

## Arduino IDE nastavenia
- **Board:** ESP32C3 Dev Module
- **Upload Speed:** 921600
- **USB CDC On Boot:** Enabled  ← dôležité pre Serial Monitor!

---

## BLE dátový formát (JSON)
```json
{"t":24.5,"h":62.0,"v":3.95,"b":78}
```
| Kľúč | Popis              |
|------|--------------------|
| `t`  | Teplota (°C)       |
| `h`  | Vlhkosť (%)        |
| `v`  | Napätie batérie (V)|
| `b`  | Kapacita batérie (%)|

---

## Mobilná appka

Otvor `dashboard.html` v **Chrome na Androide** pre BLE dashboard.

Alebo použi **nRF Connect**:
1. Scan → nájdi `SolarFan Monitor`
2. Connect
3. Služba `6E400001...` → TX `6E400003...` → Subscribe (🔔)

---

## Spotreba energie (odhad)
| Stav          | Prúd      |
|---------------|-----------|
| Deep sleep    | ~5 µA     |
| Meranie+BLE   | ~80 mA    |
| Priemerný (30s cyklus, 5s aktívny) | ~14 mA |

Pre 6× 18650 paralel (~3000 mAh celkom):
→ Výdrž cca **~9 dní** bez solárneho dobíjania
