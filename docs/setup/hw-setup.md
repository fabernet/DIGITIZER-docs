# HW setup

Starting from DIGv5, the Analog 5 volts power rail is set by a DAC, the __MCP4725A0T-E/CH__. This DAC has a _EEPROM_ which contains the value loaded at power up. This default value will be programmed during the production to the desired value.

Since DIGv5, the 5V power rail provided to the OD has a separate source.

## Slow control command to set 5V0A

The slow control command which sets the _5VA_ is the following:
```
SET DIGx 5V0A val
```
where:

- `x` is the digitizer number (0/1)
- `val` is the 5VA DAC value ranging from 0 to 4095.

## Slow control command to set 5V0A-DAC EEPROM value

The slow control command which sets the EEPROM value is the following:
```
SET DIGx 5V0E val
```
where:

- `x` is the digitizer number (0/1)
- `val` is the DAC value ranging from 0 to 4095.

## Slow control command to read 5V0A-DAC EEPROM value

The slow control command which sets the EEPROM value is the following:
```
READ DIGx 5V0E
```
where:

- `x` is the digitizer number (0/1)
