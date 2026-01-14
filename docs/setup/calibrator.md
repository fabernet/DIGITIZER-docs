# Calibrator Board

The calibrator board (__CalB__ from now on) is an electronic board aimed to generate signals to the ID and OD front-end circuitry to monitor performance stability. The __CalB__ provides input pulses to the ID receiver front-end circuit in order to calibrate input chain also after installation of the Digitizer. The board is produced by the ICRR group and works as a mezzanine card plugged onto the Digitizer board. A set of input parameters allow the user to set it up as desired.

When the calibration signals are enabled for one specific channel, the whole procedure is started and the __CalB__ generates calibration pulses. Then, it's up to each single channel to let this signal arrive to the input to be digitized.

Since the power consumption is quite high (~1.5W), it was decided to add the possibility to set a powerdown mode to reduce the input current to few hundreds of uA. To exit from powerdown mode, the board requires around 2ms: this parameter sets the maximum frequency for the calibration process. The sequence of __CalB__ signals which yields the correct timing has been fixed and stored into default parameters. Nonethless, it is possible to change those values for test and debug. The charge of output pulses can be changed.

When emission is required, the __CalB__ will output at the same time the calibration pulses to all the channels: the Digitizer SW is then able enable/disable the multiplexer of each single channel in order to receive/inhibit the calibration pulse.

The way to use the __CalB__ is the following:

- power on __CalB__
- program timing sequence (not recommended: default values are ok!)
- program pulse charge value (amplitude, length, gain)
- set proper calibration type (0 to disable, 2 to enable)
- enable calibration on desired channel

## Powering the __CalB__

The ID Digitizer can power ON/OFF the  __CalB__ power driving an on-board analog switch that can connect the board to the 12V.

### Slow control command

The slow control command which powers on and off the __CalB__ is the following:
```
SET DIGx CALIBPWR val
```
where:

- `x` is the digitizer number (0/1)
- `val` is 0/1 to Power OFF/ON

## Calibration Type

The generation of calibration pulses is handled by a specific command, which can disable or enable the generation.

The command parameters are the following:

| PARAMETER NAME | PARAMETER VALUE | EFFECT |
|    :-:         |      :-:        |   -:   |
|   CAL_NONE     |        0        | Calibration pulse is disabled |
|   CAL_TOGGLE   |        1        | Don't use: debug only |
|   CAL_GEN      |        2        | Calibration pulse is enabled |
|   CAL_FIX      |        7        | Don't use: debug only |

The values for pulse charge and timing are those set as explained in [timing settings](#calibration-timing-parameters) and in [charge settings](#calibration-pulse-charge).

### Slow control command

The slow control command to select calibration type is:
```
SET DIGx CALIBTYPE val
```
where:

- `x` is the digitizer number (0/1)
- `val` selects the calibration type (0/2)

## Enabling channel

At the level of input receiver on the analog front-end, a multiplexer chooses where the input signal should come from: the input
connector, ie the PMT, or the calibrator board. This enabling must be done channel by channel: when enabled, the FW will automatically
switch the input to the calibrator source during pulse generation.

For very high amplitude pulses, there is some diaphony between multiplexer input and output, so that sometimes signals can be
registered also by disabled channels.

### Slow control command

The slow control command which disables/enables the calibration pulse to be received on a specific channel is:
```
SET DIGx CALIBEN c val
```
where:

- `x` is the digitizer number (0/1)
- `c` is the channel number (0-17)
- `val` is 0/1 to disable/enable

## Calibration timing parameters

As previously stated, there are HW signals controlling the behavior of the the __CalB__. A timing sequence has been fixed to drives those signals in an optimal way; the following timing diagram shows the signals and the default values of the sequence:

```
                    P2M      M2C   C2P  PLS  P2E=E2P
                |---- // --|-----|-----|---|-----|||
POWERDOWN  _____|---- // --------------------------|___
MUTE       ----------------|______________________|----
CAL ENABLE ______________________|---------------|_____
PULSE      ____________________________|---|___________

Default Values: P2M ~= 2ms (200000/PDN_PRESCALER; 50000)
Default Values: M2C ~= 2us (200)
Default Values: C2P ~= 1us (100)
Default Values: PLS ~= 1-N
```

There are 3 parameters which set the timing of the pulse generation:

- P2M, ie the _POWERDOWN_ to _MUTE_;
- M2C, ie _MUTE_ to _CALEN_;
- C2P, ie _CALEN_ to _PULSE_;

The pulse length is part of the timing sequence but it main features is controlling the charge of the emitted pulse.

As shown in the diagram, after pulse emission, each signal is deactivated, one after the other. These sequence should be considered as a whole, and should not be changed. The period of generation is the distance in time between successive _POWERDOWN_ cycles, and cannot be lower than the ~2ms needed by the __CalB__ to exit from powerdown state. This fixes the maximum calibration frequency to ~500Hz.

### Calibration period

The calibration period is stored in a 16-bit register whose time unit is 50us. The time span is from 50us up to more than 3 seconds. The maximum frequency for the calibration process is limitied by the time needed for the powerdown disable to take effect, which is on the order of ms. If the period is lower than the PDND, unexpected behavior will happen.

The value into the register is the value that will be reached by the binary counter, i.e. it is the required duration - 1. For example, a value of 5 corresponds to a period of 300us, calculated as (5+1)*50us. A value of 0 corresponds to a period of 50us.

#### Slow control command

The slow control command which sets the calibration period is the following:
```
SET DIGx CALIBPER val
```
where:

- `x` is the digitizer number (0/1)
- `val` is a value ranging from 0 to 65535

### Powerdown to Mute

Since the power consumption of the Calibration board is quite relevant, it has been decided to power down the board between generation of calibration pulses. The electronics on the __CalB__ takes some time to power up, on the order of ms, and this is why the default value is 2ms.

#### Slow control command

The slow control command which sets the __CalB__ powerdown duration is the following:
```
SET DIGx CALIBPDN val
```
where:

- `x` is the digitizer number (0/1)
- `val` is a value ranging from 0 to 65535

### Mute to Calibration pulse enable

The _mute_ signal disables the opamp which drives the signals to the Digitizer. It is needed to avoid crosstalk or unwanted signals.

By default, it is set to ~2us by a 16-bit register whose time unit is 10.4ns

#### Slow control command

The slow control command which sets the __CalB__ mute duration is the following:
```
SET DIGx CALIBMUTE val
```
where:

- `x` is the digitizer number (0/1)
- `val` is a value ranging from 0 to 65535

### Calibration enable to pulse emission

The calibration enable drives the multiplexer on the Digitizer to route the calibration pulse from the __CalB__ to the front-end analog receiver.

By default, it is set to ~1us by a 16-bit register whose time unit is 10.4ns

#### Slow control command

The slow control command which sets the calibration enable to pulse emission is:
```
SET DIGx CALIBSEN ch val
```
where:

- `x` is the digitizer number (0/1)
- `ch` is the digitizer channel number (0-17)
- `val` is the DAC value ranging from 0 to 4095

## Calibration pulse charge

The charge of the calibration pulse depends mainly on 3 parameters: pulse amplitude, pulse length, and gain. The length of the pulse should be as close as possible to few units, since this helps to have a PMT-like pulse. The amplitude can be adjusted programming a DAC, while the gain has only 2 levels, high or low.

### Calibration pulse length

The calibration pulse length is set by an 8 bit register, with a time unit corresponding to 10.4ns. Typically, useful values are on the order of few units, which generate quite good PMT-like pulses. The total charge can be set using both pulse length and the calibration pulse amplitude.

#### Slow control command

The slow control command which sets the calibration pulse length is the following:
```
SET DIGx CALIBLEN val
```
where:

- `x` is the digitizer number (0/1)
- `val` is a number ranging from 0 to 255

### Calibration pulse amplitude

The pulse amplitude is set by a DAC mounted on the __CalB__ and controlled by the Digitizer through a dedicated I2C bus. The DAC is a AD569XR 16 bit DAC with values ranging from 0 to 65535.

#### Slow control command

The slow control command which sets the calibration pulse amplitude is the following:
```
SET DIGx CALIBAMP val
```
where:

- `x` is the digitizer number (0/1)
- `val` is the DAC value ranging from 0 to 65535

### Calibration gain

The calibration gain is a 2-range selection which allows multiplication of pulse amplitude by a factor. The high gain corresponds to a parameter of 1, while the low gain selection is 0.

#### Slow control command

The slow control command which sets the calibration pulse amplitude is the following:
```
SET DIGx CALIBGAIN val
```
where:

- `x` is the digitizer number (0/1)
- `val` is the ccalibration gain (0/1)

