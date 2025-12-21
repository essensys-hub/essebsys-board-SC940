# Design for Manufacturing (DFM) & Optimization Recommendations

## 1. PCB Stackup & Fabrication
-   **Current Stackup**: 4-Layer (Sig/GND/PWR/Sig).
-   **Recommendation**: Ensure controlled impedance profiles are specified if the communication module (`SC943`) uses high-speed lines or external antennas routed on the main board.
-   **Drill Sizes**: Verify that the "Release 16.0" tolerance updates are compatible with the chosen PCB house (standard vs. advanced capabilities).

## 2. Testability (DFT)
-   **Test Points**: The design includes `MIR1`, `MIR2`, `MIR3` (Fiducials/Pick-n-Place marks) but lacks explicitly labeled test points for critical signals (e.g., `TP_3V3`, `TP_GND`, `TP_MCLR`).
-   **Recommendation**: Add dedicated test pads (1mm diameter) for:
    -   Voltage Rails (12V, 5V, 3.3V).
    -   Communication Bus (TX/RX lines to the module).
    -   ICSP / Programming pins for the PIC16F946 (unless handled by the HE10 connector).

## 3. Assembly & Thermal Management
-   **Relay Density**: The board packs ~17 relays.
    -   **Risk**: High localized heating if multiple adjacent relays (`K1`-`K3`, `K4`-`K9`) are active simultaneously.
    -   **Mitigation**: Ensure the enclosure has ventilation slots above the relay bank. Verify PCB trace width for the "Common" contact of relays (~16A capability requires significant copper width or polygon pours).
-   **Component Clearance**: Vertical terminal blocks (`Phoenix Contact FFKDS`) are tall. Ensure they do not interfere with the enclosure lid or daughterboard placement (`SC943`).

## 4. Component Lifecycle
-   **PIC16F946**: This is an older mature product. Verify long-term availability or considering migrating to a newer PIC16F1xxx or PIC18F series for better code density and lower power.
-   **LMR12010**: Active and good availability.
-   **Relays**: Finder 40/34 series are industry standards with excellent availability.
