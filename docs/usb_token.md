# USB token form-factor

![PITCHFORK!!5!](img/guitarhero.jpg)

## Schematics

![schema](img/gh-scheme.png)

## PCB

The various components on the board are laid out in the following way.

### Buttons
![buttons](img/pcb-buttons.png)
### Battery Charger
![charger](img/pcb-charger.png)
### CPU
![cpu](img/pcb-cpu.png)
### External Entropy Source
![external entropy source](img/pcb-entropy.png)
### microSD slot
![micro sd slot](img/pcb-microsd.png)
### NRF24l01 radio transmitter
![nrf24l01](img/pcb-nrf.png)
### 128x64 monochrom OLED display
![oled display](img/pcb-oled.png)
### Power switches for CPU and radio transmitter
![power switches](img/pcb-switches.png)
### USB port
![usb port](img/pcb-usb.png)

## Bugs in the latest revision

pull down pin 3 of the radio powerswitch to GND using a resistor to
the GND pin of the 4.7uF cap. This eliminates the chance that the CPU
supplies power to the NRF via the SPI pins.

![nrf gnd](img/bug-nrf-gnd.png)

pin 10 of the microSD slot needs to be pulled-down to GND:

![sdcd switch fix](img/bug-sdcd.png)

Scratch off some of the GND zone, so it does not touch the nrf_ce pin:

![zone glitch](img/bug-zone1.png)

Isolate pin 2 of the 33nF cap from GND, at the same time also make sure pin 1 is connected to GND:

![another zone glitch](img/bug-zone2.png)

