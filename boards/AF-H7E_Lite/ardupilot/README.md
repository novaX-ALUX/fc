# AF-H7E Lite ArduPilot Board Config

Board definition for the AF-H7E Lite flight controller — the AF-H7E compute and
IMU modules on a smaller carrier without the IO co-processor.

Key hardware mapping:

- MCU: `STM32H753IIK6` (480MHz, 2MB Flash, UFBGA-201), 16MHz crystal
- Modules: `V6X-FM` and `V6X-IMU`, same as AF-H7E
- Carrier: no IO co-processor, `LAN8742` Ethernet, `LTC4417` power path, 2x `MCP2542` CAN
- IMU: `ICM-42688-P` + `BMI088` + `ICM-20649`
- Barometer: 2x `ICP-20100`
- Compass: `RM3100`
- CAN: `FDCAN1` + `FDCAN2`
- Ethernet: `LAN8742` (100BASE-T)
- Board ID: `6207` (novaX-ALUX reserved range 6200–6209)

Differences from AF-H7E:

- No IO co-processor: all 12 outputs come from the FMU timers.
  - `M1`–`M4` TIM5, `M5` `M6` `M9` `M10` TIM4, `M11` `M12` TIM1: PWM, OneShot and DShot
  - `M7`–`M8` TIM12: PWM and OneShot only (no DMA, no DShot)
  - Outputs in one timer group share rate and protocol.
- `SB` column of the PWM header = SBUS servo out on `SERIAL8` (`USART6` TX, PC6).
  Inversion is done inside the MCU (`SERIAL8_OPTIONS 2`).
- `RC IN` = SBUS / PPM / DSM on PI5, auto-detected. No safety switch, no DSM bind power pin.
- Sensors are listed directly instead of FMUV6 board detection.
- Battery monitor default: INA2xx on I2C bus 1.

Layout:

- `hwdef.dat`: main flight-controller hardware definition
- `hwdef-bl.dat`: bootloader hardware definition
- `defaults.parm`: board-specific default parameters (frame, SBUS out, dual CAN)

Build:

```bash
scripts/build_ap.sh AF-H7E_Lite copter
scripts/build_ap.sh AF-H7E_Lite plane
```

Verify before the first power-on:

- The carrier PCB is not built yet. Every connector pin and the `M9`–`M12` MCU
  pins must be checked against the carrier schematic.
- IMU and compass rotations are copied from AF-H7E and are provisional.
