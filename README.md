# Dual Motor Driver Board for Electric Wheelchairs

## Overview
This repository contains the complete design files and firmware for a dual-motor driver board developed for electric wheelchair applications. The project is divided into two main components:

1. **Hardware Design** – including schematic diagrams, PCB layout, and a bill of materials (BOM).  
2. **Firmware** – written in **STM32CubeIDE**, which serves as the required development environment.  

The hardware design is provided in two equivalent formats: **Altium Designer** and **JLCEDA (嘉立创)**, ensuring compatibility with different design toolchains.

---

## Hardware
The PCB design incorporates all necessary schematics and layout information, along with a detailed BOM. Two integrated development environments are supported:

- **Altium Designer** – recommended for advanced design modifications.  
- **JLCEDA** – suitable for rapid prototyping and free online access.  

---

## Firmware
The firmware is written in **STM32CubeIDE**, which is required for compilation and debugging. The board supports two different programming interfaces:

- **ST-Link** – if using the default STM32CubeIDE flashing utility, ST-Link is mandatory.  
- **USB (via DFU mode)** – requires installation of **STM32CubeProgrammer**. The use of STM32CubeProgrammer is strongly recommended, as it provides additional debugging support during the first connection.  

### Serial Output
- When using **ST-Link**, the UART interface can be employed as a serial monitor to observe program output.  
- When using **USB**, serial monitoring is also possible, but requires modification of the following function:  

```c
int _write(int fd, const char *p, int len);
```

---

## Practical Notes

### Hall Sensor Alignment
Correct alignment of the Hall sensors with respect to motor rotation is crucial. The Hall signals must either increase from 0 to 5 during forward rotation, or from 5 to 0, depending on wiring.

To verify this, uncomment the following line in the main program under `void HAL_TIM_IC_CaptureCallback(TIM_HandleTypeDef* h);`:

```c
// printf("%d\r\n", hall_state(m));
```

By manually rotating the motor and observing UART output, adjust the mapping table until the Hall output increases from 0 to 5 during forward rotation:

```c
static const int8_t hall_to_sector[8] = {
    -1, /*000*/ 0,  /*001*/
     2, /*010*/ 1,  /*011*/
     4, /*100*/ 5,  /*101*/
     3, /*110*/ -1  /*111*/
};
```

---

### Pole Pair Determination
The number of pole pairs can be determined experimentally:  
Rotate the motor one full mechanical revolution and count the number of **0 → 5 transitions** in the Hall output. This count corresponds to the number of pole pairs.
