# HW setup

Starting from DIGv5, the Analog 5 volts power rail is set by a DAC, the __MCP4725A0T-E/CH__. This DAC has a _EEPROM_ which contains the value loaded at power up. This default value will be programmed during the production to the desired value.

### Slow control command

The slow control command which sets the _5VA_ is the following:
```
SET DIGx SET5VA val
```
where:

- `x` is the digitizer number (0/1)
- `val` is the 5VA DAC value ranging from 0 to 4095.
