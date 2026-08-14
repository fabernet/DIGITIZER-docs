# On board peripherals

## EEPROM

A 2-Kbit I2C serial EEPROM from Microchip, with part number 24AA02E48T, is onboard. This device has a double function: storing few bytes available to the user and implementing OUI and EID unique number identification. 

An OUI (Organizationally Unique Identifier) is a 24-bit code, usually used to identify a device manufacturer. An EID (eUICC Identifier) is a 32-digit global serial number used to identify a device in a unique way. These 2 codes can be used to univocally identify the Digitizer board.

### EEPROM SC Commands

The slow control command which enable front-end is the following:
```
READ DIGx OUI
READ DIGx EID
```
where:

- `x` is the digitizer number (0/1)

## RFID

Since the very first release of the Digitizer, DIGv2, an RFID device has been available on board. The chosen device is the `Murata LXMS21ACMF-218`, which can be read by a dedicated RFID reader.

Due to the lack of availability of Murata RFID components at the time of production, the IC will not be mounted and will be replaced by a data matrix code printed on the PCB (top and bottom layers with different codes).
