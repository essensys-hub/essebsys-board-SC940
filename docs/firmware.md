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

## Master-Slave Communication Analysis

The **SC940D** acts as a generic Input/Output slave module controlled by a master concentrator (e.g., **944D**).

### Communication Protocol
- **Physical Layer**: likely I2C (based on `slavenode.c` naming and register usage like `SSPBUF`).
- **Mechanism**:
    1.  **Interrupt Driven**: The `SSP_Handler` receives raw bytes into `RXBuffer`.
    2.  **Validation**: A Checksum is calculated (`us_CalculerCRCSurTrame`). If valid, data is copied to `CmdBuf`.
    3.  **Execution**: The main loop calls `vd_Traitement_I2C` which processes `CmdBuf`.

### Mermaid Flowchart (Firmware Logic)

```mermaid
graph TD
    subgraph Interrupt[Interrupt Service Routine]
        ISR(SSP_Handler) --> Recv[Receive Byte]
        Recv --> Buffer{Buffer Full?}
        Buffer -- No --> Store[Store in RXBuffer]
        Buffer -- Yes --> Checksum[Verify Checksum]
        Checksum -- Valid --> Copy[Copy to CmdBuf]
        Checksum -- Invalid --> Error[Set Error Flag]
    end

    subgraph MainLoop[Main Application Loop]
        Start((Start)) --> Init[Initialization]
        Init --> Loop{While(1)}
        Loop --> Task1[vd_Traitement_I2C]
        Task1 --> CmdCheck{CmdBuf[0] != 0?}
        
        CmdCheck -- Yes --> Switch[Switch Command]
        Switch --> C1[C_FORCAGE_SORTIES]
        Switch --> C2[C_CONF_SORTIES]
        Switch --> C3[C_TPS_EXTINCTION]
        
        C1 --> Action1[Update Relays/Dimmers]
        C2 --> Action2[Update Config & EEPROM]
        C3 --> Action3[Update Timers & EEPROM]
        
        Action1 --> Reset[CmdBuf[0] = 0]
        Action2 --> Reset
        Action3 --> Reset
        
        Reset --> Loop
        CmdCheck -- No --> Loop
    end
```

### Protocol Commands
The master sends commands to control the board. Identified commands from source:

| Command ID | Name | Description |
| :--- | :--- | :--- |
| `0x01` | `C_FORCAGE_SORTIES` | Force state of relays, dimmers, or shutters. |
| `0x02` | `C_CONF_SORTIES` | Configure dimmer mode (On/Off or Dimming). |
| `0x03` | `C_TPS_EXTINCTION` | Set auto-off timer for lamps. |
| `0x04` | `C_TPS_ACTION` | Set movement time for shutters. |
| `0x05` | `C_ACTIONS` | trigger global actions (e.g. Save State). |

### Interaction with Master (944D)
The **944D (Master)** sends cyclic or event-driven frames to the **SC940 (Slave)**.
1.  **Master Request**: The 944D sends a frame containing a Command ID and Data (e.g., "Set Relay 1 ON").
2.  **Slave Processing**: The SC940 receives the frame in ISR, validates it, and sets `CmdBuf`.
3.  **Application State**: The SC940 updates its internal state variables (`us_SortiesRelais`) based on the command.
4.  **Hardware Update**: The main loop eventually drives the physical pins based on these state variables (in `gestionsorties.c`/`hard.c`).
