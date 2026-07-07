# On board peripherals

## EEPROM

A small EEPROM has a double function of storing few bytes for the user and also implementing OUI and EID unique number identification.

The communication protocol is I2C.

#### EEPROM SC

The slow control command which enable front-end is the following:
```
READ DIGx OUI
READ DIGx EID
```
where:

- `x` is the digitizer number (0/1)
- `ch` is the digitizer channel number (0-17 or ALL)
- `val` is 0 to disable or 1 to enable front-end

## RFID

Since the very first release of the Digitizer, DIGv2, an RFID device has been available on board. The chosen device is the `Murata LXMS21ACMF-218`, which can be read by a dedicated RFID reader.

