# CH341A-Pro

The "CH341APro" or "CH341A MinProgrammer" or "CH341A MiniProgrammer" is a USB dongle
intended for reading and writing SPI as well as I2C flash ICs.
It is based on the CH341 USB to serial/parallel interface IC, which can operate in different modes: UART, parallel port, SPI or I2C.

## Schematic

A board schematic was derived by analyzing the PCB layout:

https://github.com/Upcycle-Electronics/CH341A-Pro/blob/master/ch341Apro_schematicV01.pdf

## Flaws

The PCB has a number of disadvantages:

* It's based on the CH341. The CH341 can onfigure it's mode through the SCL and SDA pins, which can interfere with I2C bus access.
* The adapter may start up in an undesired mode: The CH341 can not be switched to a another mode after startup, no external configuration EEPROM is present and no jumper to connect ACT# to RST# (as suggested in the datasheet) can be placed.
* In-system programming is dangerous with this adapter as no components are present protecting adapter or target (no diodes, no level-shifter, no fuse).
* SPI can only operate at 3V3. Applying other voltages may damage the adapter.
* The I2C pins are pulled up to 5V, although VCC is tied to 3V3. Some flash ICs may be damaged by this overvoltage.
* The adapter can only act as I2C master, not as slave.
* No bypass capacitors are present at the flash ICs sockets and pads.
* The slew-rate can not be adjusted and no footprints are present to insert resistors into the signal traces.

## How to patch

Disclaimer: Tinkering with the hardware may break something. Proceed at your own risk.

### TL;DR

* remove D2
* connect the bottom pad of C4 to the middle pad of the LDO
* cut the trace above C2 and insert a rectifier or Schottky SMD diode
* on the bottom side connect socket pins 13 and 14 to the 3V3 pin or trace, each via a 4k7 pull-up resistor
* on the bottom side cut the horizontal traces on the bottom left and below the pins to disconnect VCC from 5V (or selectively de-solder pin 28 if you are skilled enough; in that case skip the next step)
* re-connect the 5V rail by soldering a wire between the respective USB pin and one of the two 5V pins on the right
* scratch open the trace above pin 28 and connect the two (tie VCC to 3V3)
* on the bottom side solder two SMD capacitors: between socket pins 7 and 8 as well as 15 and 16

### Long version

The adapter shall support 3V3 and 5V flash ICs, both I2C and SPI, in the socket as well as in an external target powered by 3V3 or 5V.

SPI flash:
The socket is supplied with 3V3, which can be elevated to 5V seamlessly by placing a jumper on the two respective adapter pins. 5V operation is acceptable as long as the CH341 is supplied with 5V aswell: Only then it's inputs are 5V-tolerant. Signal output is then still at 3V3 but in most cases that should be sufficient for interfacing with a 5V target. CS is always an actively driven signal and may be active-high. It should therefore not be pulled, the respective resistor should be disconnected.

I2C flash:
The CH341 may pull SCL and SDA to VCC internally after startup, although this behaviour apparently varies from board to board. To be sure, in order to change the I2C bus voltage, VCC should be adjusted and at the same time two additional, external pull-up resistors should be placed. The SCL and SDA pins are also used for IC configuration, which interfers with I2C operation. This issue, however, can be solved by pulling ACT# low during startup i.e. by permanently placing a jumper on adapter pins 1-2. V3 (pin 9) must remain at 3V3 i.e. be permanently connected to the LDO's output. VCC (pin 28) must be disconnected from the 5V rail and connected to the target's supply. In order to keep the "V3" net at 3V3 when the target supply is elevated to 5V (i.e. by the aforementioned jumper or an externally powered target), a diode must be placed after the LDO's output (i.e. after the "V3" net) and before the target supply (“3V3" net).

In order to decrease the impedance of the targets' supply voltag, two bypass capacitors should be placed below the IC socket.

The adjustments are summarized in the TL;DR paragraph above.
