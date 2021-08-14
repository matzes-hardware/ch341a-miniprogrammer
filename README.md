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
