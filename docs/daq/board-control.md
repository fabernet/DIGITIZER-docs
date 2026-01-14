# Board control

There are some commands dedicated to control the DAQ on the Digitizer board. The most important one which allows to take data is described [here](#stream-enable).

## Front end enable

Enabling the front-end makes sense mainly for the debug of the HW. During normal operation, this mode is not required nor adviced.

The aim of this mode is enabling the conversion process of the charge on the ADCs of the Low and High path of the integrator. All the HW signals (trigger, SPIs, BLR) are active and completely functional. Data are not transmitted to the DPB for data acquisition.

Reception of the trigger is also enabled, and the ADC and TDC rate monitors are active and working.

### Slow control command

The slow control command which enable front-end is the following:
```
SET DIGx FESTATUS ch val
```
where:

- `x` is the digitizer number (0/1)
- `ch` is the digitizer channel number (0-17 or ALL)
- `val` is 0 to disable or 1 to enable front-end

## Stream enable

This is the standard mode for data taking. Data transmission to the DPB is enabled and the front-end is also enabled to drive ADC signals and to perform hit time stamping. It is not needed to enable the front-end explicitly, as explained in this [paragraph](#front-end-enable)

### Slow control command

The slow control command which enables/disables front-end and starts/stops data taking is the following:
```
SET DIGx DAQSTATUS ch val
```
where:

- `x` is the digitizer number (0/1)
- `ch` is the digitizer channel number (0-17 or ALL)
- `val` is 0 to disable or 1 to enable streaming data taking

The command can be used to enable/disable the front-end and to start and stop the data transmission to the DPB. Depeding on `ch` option, the command is applied either to a single channel or, using a global parameter, enabling all the channels at the same time. 
