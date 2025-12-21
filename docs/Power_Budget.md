# Power Budget Estimation: SC940D

> [!WARNING]
> This budget is a theoretical estimation based on component datasheets and typical values. Actual verification on hardware is required.

## 1. System States
We define two primary power states for the analysis:
1.  **Idle / Standby**: MCU running, Communication module in RX window/sleep, no relays active.
2.  **Max Transmission**: Communication module TX, MCU active.
3.  **Full Actuation**: All relays energized (Worst Case).

## 2. Component Consumption Analysis

### A. Logic Core (3.3V / 5V Rail)
| Component | State | Current (Est.) | Power (Est.) |
| :--- | :--- | :--- | :--- |
| **PIC16F946** | Run (4MHz) | ~2 mA | 10 mW |
| **PIC16F946** | Sleep | < 1 µA | ~0 mW |
| **SC943 (Module)** | Idle/RX | ~10-20 mA | ~50-100 mW |
| **SC943 (Module)** | TX (RF High) | ~100-150 mA | ~500 mW |
| **Protection/Leakage** | Standby | < 100 µA | < 1 mW |

### B. Actuators (12V Rail)
*Note: Relay coils are the dominant power consumer.*

| Component | Quantity | Unit Current (12V) | Unit Power | Total Max Power |
| :--- | :--- | :--- | :--- | :--- |
| **Finder 40.61** | ~12 | ~40 mA (0.5W) | 0.5 W | 6.0 W |
| **Finder 34.51** | ~5 | ~15 mA (0.17W) | 0.17 W | 0.85 W |

## 3. Total Power Estimation (Input 12V)

### Scenario 1: Standby (No Relays)
-   **Logic Load**: ~20mA @ 5V (from Buck)
-   **Buck Efficiency**: ~85%
-   **Input Current (12V)**: ~1-10 mA.
-   **Total Power**: **< 0.2 W**.

### Scenario 2: Typical Operation (2 Relays Active + RX)
-   **Relays**: 2x 40.61 = 1 W.
-   **Logic**: 0.1 W.
-   **Total Power**: **~1.1 W**.

### Scenario 3: Worst Case (All Relays ON + TX)
-   **Relays**: 6.85 W.
-   **Logic (TX)**: 0.5 W / 0.85 (Eff) ≈ 0.6 W.
-   **Total Power**: **~7.5 W**.

## 4. Recommendations
1.  **PSU Sizing**: The upstream 12V power supply must be rated for at least **10W (or ~1A)** to handle the worst-case inrush and simultaneous actuation.
2.  **Thermal**: 7.5W is significant heat. Ensure the enclosure allows for passive convection if "All ON" is a valid state.
3.  **Firmware Optimization**: Implement "soft-start" or sequenced activation of relays to prevent current spikes (inrush) from acting simultaneously on the power rail.
