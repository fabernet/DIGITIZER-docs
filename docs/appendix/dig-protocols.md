# Digitizer Slow control protocols

The slow control channel to communicate with the digitizer board is the serial connected to the DPB. Natively, there are 2 possible communication protocols over this serial link: the so-called `GUI` and the `COPacket`.

The `GUI` protocol was developed as a mean to communicate "interactively" with the digitizer board. There is a menu implemented and commands can be issued sending ASCII codes through a terminal or an application. This protocol is not suitable to be implemented in a "machine readable" way, since there is no control over the character stream sent or returned by the digitizer. This is meant essentially for "human" interaction through a terminal, though a lot of effort has been done to use it also in Python scripts. The main drawback is the lack of a packet format which helps designing an efficient code to exchange data.

The `COPacket`, on the contrary, though it is a very simple protocol, is thought to be "machine" friendly. It is an ASCII packet based protocol, with leading and trailing characters delimiting a variable sized packet, well defined commands and parameters. This will be the target protocol.

Even though both are mantained (in some cases, for debug purposes, some commands could have been implemented only in one protocol), it is recommended to use the COPacket protocol.

## Digitizer GUI Serial protocol

The GUI protocol is intended for _human interaction friendly_, i.e. is designed to be used from a serial terminal in an interactive way. This makes it very confortable for debug and tests, but almost impossible to exchange commands efficiently with an application or a script.

For example, pressing character `m` will show the menu-like prompt, which is slightly self-explaining.

## Digitizer COPacket Serial protocol

The protocol is based on packets of ASCII characters; each packet starts with the `$` leading character and is terminated by the `#` trailing character. The command follows immediately after the `$` character: it is a string which may or may not require one or more parameters. For example, a list of available commands is returned typing the `$help#` command.

Available commands are shown hereafter, taken from the _COPacketCmdHkDig.cpp_ file which implements the whole command set.

This document describes the commands exposed by the `HkDigCommands` command table (`HkDigCmdSize = 102` entries, backed by the `HKDIG_CMD_LIST` enum). Each command consists of:

- **Command** – the short ASCII mnemonic sent over the communication link
- **Enum** – the corresponding `HKDIG_CMD_LIST` / `_COPacketCmdType` identifier
- **Arguments** – parameters the command expects (parsed from the trailing `#`-terminated usage string)
- **Description** – explanation of what the command does, from the enum's inline comments

### 1. Front-End (FE) / Parameter Commands

Get/set per-channel or all-channel thresholds, integration time, and dead time.

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `gthn` | `HKDIG_GET_THR_NUM` | `ch` | Get channel threshold |
| `sthn` | `HKDIG_SET_THR_NUM` | `ch, thr` | Set channel threshold |
| `stha` | `HKDIG_SET_THR_ALL` | `thr` | Set the same threshold for all channels |
| `gitn` | `HKDIG_GET_IT_NUM` | `ch` | Get integration time |
| `sitn` | `HKDIG_SET_IT_NUM` | `ch, I` | Set integration time |
| `sita` | `HKDIG_SET_IT_ALL` | `I` | Set integration time for all channels |
| `gdtn` | `HKDIG_GET_DT_NUM` | `ch` | Get dead time |
| `sdtn` | `HKDIG_SET_DT_NUM` | `ch, D` | Set dead time |
| `sdta` | `HKDIG_SET_DT_ALL` | `D` | Set dead time for all channels |

### 2. Calibration Commands

Configure the calibration board (CAL-Board) and its pulse generator.

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `scalm` | `HKDIG_SET_CAL_TYPE` | `M` | Set calibration emission type: `0` = disable, `1` = toggle once, `2` = 4 kHz |
| `scale` | `HKDIG_SET_CAL_N` | `ch, 0/1` | Set channel calibration input |
| `scalp` | `HKDIG_SET_CAL_POWER` | `0/1` | CAL-Board: power the cal board on or off |
| `scall` | `HKDIG_SET_CAL_PLS_LEN` | `L` | CAL-Board: set calibration pulse length (units of 10.4 ns) |
| `scala` | `HKDIG_SET_CAL_PLS_AMP` | `A` | CAL-Board: set calibration DAC pulse amplitude |
| `gcala` | `HKDIG_GET_CAL_PLS_AMP` | — | CAL-Board: get calibration DAC pulse amplitude |
| `scaltp` | `HKDIG_SET_CAL_NPDN` | `p2m` | CAL-Board: set power-down signal timer (units of 41.6 ns) |
| `scaltm` | `HKDIG_SET_CAL_MUTE` | `m2e` | CAL-Board: set mute signal timer (units of 10.4 ns) |
| `scalte` | `HKDIG_SET_CAL_PLSEN` | `e2p` | CAL-Board: set calibration-enable signal timer (units of 10.4 ns) |
| `scalt` | `HKDIG_SET_CAL_PERIOD` | `T` | CAL-Board: set calibration pulse period (units of 50 µs) |
| `scalg` | `HKDIG_SET_CAL_GAIN` | `G` | CAL-Board: set low gain for the cal board |

### 3. Channel Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `glgc` | `HKDIG_GET_CHN_LG_CHG` | `ch` | Get channel low-gain (LG) charge |
| `ghgc` | `HKDIG_GET_CHN_HG_CHG` | `ch` | Get channel high-gain (HG) charge |
| `gmnc` | `HKDIG_GET_CHN_MEAN_CHG` | `ch, i` | Get mean charge for a channel over `i` samples |
| `gchs` | `HKDIG_GET_CHN_STATUS` | `ch` | Get channel status register |
| `gchc` | `HKDIG_GET_CHN_CNTRL` | `ch` | Get channel control register |
| `schc` | `HKDIG_SET_CHN_CNTRL` | `ch, ctl` | Set channel control register |
| `efen` | `HKDIG_START_FE_N` | `ch` | Enable front-end (FE) of channel `n` |
| `dfen` | `HKDIG_STOP_FE_N` | `ch` | Disable front-end (FE) of channel `n` |
| `edqn` | `HKDIG_START_DAQ_N` | `ch` | Enable DAQ (data streaming) of channel `n` |
| `ddqn` | `HKDIG_STOP_DAQ_N` | `ch` | Stop DAQ of channel `n`, resetting FE, DAQ, pedestal, etc. |
| `grof` | `HKDIG_RO_FMON_N` | `ch` | Measure ring-oscillator frequency of channel `n` |

### 4. Global DAQ Commands

Commands that act on all channels at once.

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `efea` | `HKDIG_START_FE_ALL` | — | Enable FE for all channels |
| `dfea` | `HKDIG_STOP_FE_ALL` | — | Disable FE for all channels |
| `edqa` | `HKDIG_START_DAQ_ALL` | — | Enable DAQ (data streaming) for all channels |
| `ddqa` | `HKDIG_STOP_DAQ_ALL` | — | Stop DAQ for all channels, resetting FE, DAQ, pedestal, etc. |
| `tdcr` | `HKDIG_TDC_RST` | — | Reset the TDC counter |

### 5. Data Transmission Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `sdmx` | `HKDIG_SET_AUR_DEMUX` | `D` | Set the 32-to-64 bit Aurora demux |

### 6. Pedestal Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `spedn` | `HKDIG_SET_PED_ENABLE` | `ch, en` | Set pedestal enable |
| `gpedn` | `HKDIG_GET_PED_ENABLE` | `ch` | Get pedestal enable |
| `spedp` | `HKDIG_SET_PED_PERIOD` | `P` | Set pedestal period (units of 50 µs) |
| `gpedp` | `HKDIG_GET_PED_PERIOD` | — | Get pedestal period (units of 50 µs) |
| `speds` | `HKDIG_SET_PED_STAGGER` | `S` | Set pedestal stagger (units of 10.4 ns) |
| `gpeds` | `HKDIG_GET_PED_STAGGER` | — | Get pedestal stagger (units of 10.4 ns) |

### 7. OD Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `sodsel` | `HKDIG_SET_OD_SEL_REG` | `ch, 0/1` | Set the OD SEL signal to switch HV to the PMT |
| `godsel` | `HKDIG_GET_OD_SEL_REG` | — | Get the OD SEL register |

### 8. Board Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `ghwv` | `HKDIG_GET_HW_VER` | — | Get hardware version |
| `ggwv` | `HKDIG_GET_GW_VER` | — | Get gateware (GW) version |
| `ggwd` | `HKDIG_GET_GW_DATE` | — | Get gateware Hog build date |
| `gswv` | `HKDIG_GET_SW_VER` | — | Get µBlaze software build date |
| `gbds` | `HKDIG_GET_BOARD_STATUS` | — | Get board status |
| `gbdc` | `HKDIG_GET_BOARD_CNTRL` | — | Get board control |
| `gupt` | `HKDIG_GET_UPTIME` | — | Get uptime, in seconds |
| `raur` | `HKDIG_RST_AURORA_LINK` | — | Reset both Aurora links |
| `sclk` | `HKDIG_SET_CLOCK` | `val` | Set DAQ clock source: `0` = local clock, `1` = DPB clock |
| `gclk` | `HKDIG_GET_CLOCK` | — | Get current DAQ clock source |
| `gtlck` | `HKDIG_GET_TLNK_LOCK` | — | Get TLink lock status |
| `spsc` | `HKDIG_SET_ADS_PSC` | `0-3` | Set OD-ADC (ADS7049) clock prescaler: 0–15 clock divider |

### 9. EEPROM Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `goui` | `HKDIG_GET_EEPROM_OUI` | — | Get EEPROM OUI code |
| `geid` | `HKDIG_GET_EEPROM_EID` | — | Get EEPROM EID code |

### 10. Rate Monitor Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `grmp` | `HKDIG_GET_RMON_PER` | — | Get rate-monitor period |
| `srmp` | `HKDIG_SET_RMON_PER` | `per` | Set rate-monitor period |
| `grma` | `HKDIG_GET_RMON_ADC_N` | `ch` | Get ADC rate-monitor value for channel `N` |
| `grmt` | `HKDIG_GET_RMON_TDC_N` | `ch` | Get TDC rate-monitor value for channel `N` |
| `grmf` | `HKDIG_GET_RMON_FMT_N` | `ch` | Get FMT rate-monitor value for channel `N` |
| `grmm` | `HKDIG_GET_RMON_MUX_N` | — | Get board MUX rate-monitor value |
| `grmr` | `HKDIG_GET_RMON_RST_N` | — | Get TDC RESET rate-monitor value |
| `runr` | `HKDIG_RUN_RMON` | — | Start a rate-monitor measurement, lasting `RMON_T` seconds |
| `rmne` | `HKDIG_RUN_RMON_EN` | `ch` | Enable rate-monitor measurement manually, until disabled |
| `rmnd` | `HKDIG_RUN_RMON_DIS` | `ch` | Disable rate-monitor measurement manually |

### 11. Monitoring (Voltage / Current / Temperature) Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `g3v3a` | `HKDIG_GET_BOARD_3V3A` | — | Get 3V3A rail voltage |
| `g12va` | `HKDIG_GET_BOARD_12VA` | — | Get 12VA rail voltage |
| `gi12v` | `HKDIG_GET_BOARD_I12V` | — | Get 12 V input current |
| `g5v0a` | `HKDIG_GET_BOARD_5V0A` | — | Get 5V0A rail voltage |
| `g5v0f` | `HKDIG_GET_BOARD_5V0F` | — | Get 5V0F rail voltage |
| `gc12v` | `HKDIG_GET_BOARD_C12V` | — | Get C12V rail voltage |
| `gi5va` | `HKDIG_GET_BOARD_I5VA` | — | Get current of 5VA rail |
| `gi3v3a` | `HKDIG_GET_BOARD_I3V3A` | — | Get current of 3.3VA rail |
| `gi12va` | `HKDIG_GET_BOARD_I12VA` | — | Get current of 12VA rail |
| `gtfe` | `HKDIG_GET_BOARD_TFE` | — | Get temperature of FE channels, in hundredths of a degree |
| `gtfpga` | `HKDIG_GET_BOARD_TFPGA` | — | Get FPGA temperature, in hundredths of a degree |
| `gtpwr` | `HKDIG_GET_BOARD_TPWR` | — | Get power-section temperature, in hundredths of a degree |
| `gtfer` | `HKDIG_GET_BOARD_TFER` | — | Get raw temperature of FE channels |
| `gtfpgar` | `HKDIG_GET_BOARD_TFPGAR` | — | Get raw FPGA temperature |
| `gtpwrr` | `HKDIG_GET_BOARD_TPWRR` | — | Get raw power-section temperature |
| `gtch0` | `HKDIG_GET_BOARD_TCH0` | — | Get temperature near channel 0 |
| `gtch11` | `HKDIG_GET_BOARD_TCH11` | — | Get temperature near channel 11 |
| `gtch0r` | `HKDIG_GET_BOARD_TCH0R` | — | Get raw temperature near channel 0 |
| `gtch11r` | `HKDIG_GET_BOARD_TCH11R` | — | Get raw temperature near channel 11 |
| `g5vod` | `HKDIG_GET_BOARD_5VOD` | — | Get 5V0OD power rail voltage |
| `gi5vod` | `HKDIG_GET_BOARD_I5VOD` | — | Get current of 5V0OD power rail |
| `s5v0a` | `HKDIG_SET_5V0A` | `num` | Set 5VA power-rail programming DAC |
| `s5v0e` | `HKDIG_SET_5V0A_EEPROM` | `num` | Set 5VA DAC value in EEPROM |
| `g5v0e` | `HKDIG_GET_5V0A_EEPROM` | — | Get 5VA DAC value from EEPROM |

### 12. TB (Testbench) Register Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `stbr` | `HKDIG_SET_TB_REG` | `num` | Set testbench control register |
| `gtbr` | `HKDIG_GET_TB_REG` | — | Get testbench control register |
| `gtbs` | `HKDIG_GET_TB_ST` | — | Get testbench status register |

### 13. BME280 Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `gbme` | `HKDIG_GET_BME_DATA` | — | Read temperature, relative humidity, and pressure data from the BME280 sensor |
| `gtcal` | `HKDIG_GET_BME_TCAL` | — | Get temperature calibration data |
| `ghcal` | `HKDIG_GET_BME_HCAL` | — | Get relative-humidity calibration data |
| `gpcal` | `HKDIG_GET_BME_PCAL` | — | Get pressure calibration data |

### 14. Miscellaneous Commands

| Command | Enum | Arguments | Description |
|---|---|---|---|
| `help` | `HKDIG_HELP` | — | Print command help/usage list |
| `erro` | `HKDIG_ERRO` | — | Get/report last error |

