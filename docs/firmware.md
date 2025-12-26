# Firmware Documentation

The firmware for the **SC940D** board is developed in C for the **Microchip PIC16F946** microcontroller.

## Development Environment
- **Compiler**: Hi-Tech C for PIC10/12/16.
- **IDE**: MPLAB X (Project file `essensys_ba.X`).
- **MCU**: PIC16F946.
- **Clock**: High Speed (HS) Oscillator.
- **Watchdog**: Disabled at startup (`WDTDIS`).

## Project Structure
The source code is located in `SC940D/Prog/code_ba (fichiers sources 1.7)/source/`.

| File | Description |
| :--- | :--- |
| `main.c` | Entry point, initialization, and EEPROM management. |
| `hard.c/h` | Hardware Abstraction Layer (HAL). Pin definitions and low-level I/O. |
| `gestionentrees.c` | Input signal processing and debouncing. |
| `gestionsorties.c` | Output control logic (Relays). |
| `variateur.c` | Dimmer ("Variateur") control algorithms. |
| `traitement.c` | Main application logic and state machine. |
| `slavenode.c` | Communication protocol implementation (I2C/Bus). |

## Memory Map (EEPROM)
The EEPROM is used to store persistent configuration and state.

| Address | Description |
| :--- | :--- |
| `0x000D` | EEPROM Mapping Version. |
| `0x000E-0F` | Initialization Check (`0xAA55`). |
| `0x0010-11` | Relay States (LSB/MSB). |
| `0x0012-29` | Dimmer Configuration (Current level, Saved level, Mode). |
| `0x002A-31` | Shutter Action Times (Volets). |
| `0x0032-42` | Lamp Extinction Times. |

## Key Features
1.  **State Persistence**: Relay states and dimmer levels are saved to EEPROM to restore state after power loss.
2.  **Input Management**: Direct handling of digital inputs with debouncing.
3.  **Load Control**:
    - **Relays**: Simple On/Off control.
    - **Dimmers**: Phase control or PWM (depending on hardware variant) for lighting.
    - **Shutters**: Timed control for opening/closing blinds.
4.  **Communication**: Uses a custom "Slave Node" protocol, likely over I2C or a proprietary bus to communicate with the central controller (`SC943` module).

## Building
Open the project `essensys_ba.X` in MPLAB X and build using the Hi-Tech C toolchain. The output hex file is generated in the `dist` directory.
