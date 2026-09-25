# Linux dpb-slowcontrolapp service

This is the target slow control communication path with the Digitizer; it is implemented by the DPB with a service, called `dpb-slowcontrolapp`, which manages the interface to the digitizer. Details of this implementation can be found [here](https://gitlab.cern.ch/hk-dpb-team/hk_petalinux_project_dpb2_2022_2/-/blob/master/doc/dpb-slowcontrolapp-howto.md?ref_type=heads).

`dpb-slowcontrolapp` commands consist if a `READ/SET` command, followed by Digitizer selection `DIG0/DIG1` and then by the _Service command_ shown hereafter. Each service command maps to one or more `HKDIG_*` commands (see "Digitizer slow control protocols" section in the Appendix).

## SET commands map

The following table maps the write commands of the two protocols:

| HKDIG Enum | Mnemonic | Service Command | Parameter | Value |
|---|---|---|---|---|
|CHANNEL SECTION|||||
| `HKDIG_SET_CHN_CNTRL` | `schc` | `STATUS` | 0–17 | number |
| `HKDIG_SET_THR_NUM` / `HKDIG_SET_THR_ALL` | `sthn` / `stha` | `DISCTRES` | 0–17, ALL | number |
| `HKDIG_SET_IT_NUM` / `HKDIG_SET_IT_ALL` | `sitn` / `sita` | `INTTIME` | 0–17, ALL | number |
| `HKDIG_SET_DT_NUM` / `HKDIG_SET_DT_ALL` | `sdtn` / `sdta` | `DEADTIME` | 0–17, ALL | number |
| `HKDIG_START_FE_N` / `HKDIG_STOP_FE_N` (+ `_ALL`) | `efen` / `dfen` (+ `efea` / `dfea`) | `FESTATUS` | 0–17, ALL | ON/OFF |
| `HKDIG_START_DAQ_N` / `HKDIG_STOP_DAQ_N` (+ `_ALL`) | `edqn` / `ddqn` (+ `edqa` / `ddqa`) | `DAQSTATUS` | 0–17, ALL | ON/OFF |
|CALIBRATOR SECTION|||||
| `HKDIG_SET_CAL_N` | `scale` | `CALIBEN` | 0–17 | ON/OFF |
| `HKDIG_SET_CAL_TYPE` | `scalm` | `CALIBTYPE` | – | 0–3 |
| `HKDIG_SET_CAL_PERIOD` | `scalt` | `CALIBPER` | – | number |
| `HKDIG_SET_CAL_GAIN` | `scalg` | `CALIBGAIN` | – | LOW/HIGH |
| `HKDIG_SET_CAL_POWER` | `scalp` | `CALIBPWR` | – | 0–1 |
| `HKDIG_SET_CAL_PLS_LEN` | `scall` | `CALIBLEN` | – | number |
| `HKDIG_SET_CAL_PLS_AMP` | `scala` | `CALIBAMP` | – | number |
| `HKDIG_SET_CAL_NPDN` | `scaltp` | `CALIBPDN` | – | number |
| `HKDIG_SET_CAL_MUTE` | `scaltm` | `CALIBMUTE` | – | number |
| `HKDIG_SET_CAL_PLSEN` | `scalte` | `CALIBSEN` | – | number |
|PEDESTAL SECTION|||||
| `HKDIG_SET_PED_ENABLE` | `spedn` | `PEDTYPE` | 0–17 | 0–1 |
| `HKDIG_SET_PED_PERIOD` | `spedp` | `PEDPERIOD` | – | number |
| `HKDIG_SET_PED_STAGGER` | `speds` | `PEDSTAG` | – | number |
|RATE MONITOR SECTION|||||
| `HKDIG_SET_RMON_PER` | `srmp` | `RMONT` | – | number |
| `HKDIG_RUN_RMON` | `runr` | `RMONRUN` | – | – |
| `HKDIG_RUN_RMON_EN` / `HKDIG_RUN_RMON_DIS` | `rmne` / `rmnd` | `RMON` | – | ON/OFF |
|OD SECTION|||||
| `HKDIG_SET_OD_SEL_REG` | `sodsel` | `ODSEL` | 0–5 | 0–1 |
| `HKDIG_SET_ADS_PSC` | `spsc` | `PSC` | 12–17 | number |
|PERIPHERAL SECTION|||||
| `HKDIG_SET_5V0A` | `s5v0a` | `5V0A` | – | number |
| `HKDIG_SET_5V0A_EEPROM` | `s5v0e` | `5V0E` | – | number |
|PRODUCTION TESTBENCH SECTION|||||
| `HKDIG_SET_TB_REG` | `stbr` | `TBREG` | – | number |
|GENERIC SECTION|||||
| `HKDIG_SET_AUR_DEMUX` | `sdmx` | `DEMUX` | – | ON/OFF |
| `HKDIG_TDC_RST` | `tdcr` | `TDCRST` | – | – |
| `HKDIG_RST_AURORA_LINK` | `raur` | `AURORARST` | – | – |
| `HKDIG_SET_CLOCK` | `sclk` | `CLOCK` | – | LOCAL/DPB |

> **Note:** `STATUS` (write) is a somewhat misleading name — it actually writes
> the **channel control register** (`HKDIG_SET_CHN_CNTRL`), not a status
> register. See `STATUS` (read) below, which does read the actual status
> register.

## READ commands map

The following table maps the read commands of the two protocols:

| HKDIG Enum | Mnemonic | Service Command | Parameter |
|---|---|---|---|
| `HKDIG_GET_HW_VER` | `ghwv` | `HWVER` | – |
| `HKDIG_GET_GW_VER` | `ggwv` | `GWVER` | – |
| `HKDIG_GET_SW_VER` | `gswv` | `SWVER` | – |
|CHANNEL SECTION||||
| `HKDIG_GET_CHN_STATUS` | `gchs` | `STATUS` | 0–17, AUR0, AUR1 |
| `HKDIG_GET_CHN_CNTRL` | `gchc` | `CHCONTROL` | 0–17 |
| `HKDIG_GET_THR_NUM` | `gthn` | `DISCTRES` | 0–17 |
| `HKDIG_GET_IT_NUM` | `gitn` | `INTTIME` | 0–17 |
| `HKDIG_GET_DT_NUM` | `gdtn` | `DEADTIME` | 0–17 |
|CALIBRATOR SECTION||||
| `HKDIG_GET_CAL_PLS_AMP` | `gcala` | `CALIBAMP` | – |
| `HKDIG_GET_CHN_HG_CHG` | `ghgc` | `HG` | 0–17 |
| `HKDIG_GET_CHN_LG_CHG` | `glgc` | `LG` | 0–17 |
|PEDESTAL SECTION||||
| `HKDIG_GET_PED_PERIOD` | `gpedp` | `PEDPERIOD` | – |
| `HKDIG_GET_PED_ENABLE` | `gpedn` | `PEDTYPE` | 0–17 |
|RATE MONITOR SECTION||||
| `HKDIG_GET_RMON_ADC_N` | `grma` | `RMONADC` | 0–17 |
| `HKDIG_GET_RMON_TDC_N` | `grmt` | `RMONTDC` | 0–17 |
| `HKDIG_GET_RMON_FMT_N` | `grmf` | `RMONFMT` | 0–17 |
| `HKDIG_GET_RMON_MUX_N` | `grmm` | `RMONMUX` | – |
| `HKDIG_GET_RMON_RST_N` | `grmr` | `RMONRST` | – |
| `HKDIG_GET_RMON_PER` | `grmp` | `RMONT` | – |
|OD SECTION||||
| `HKDIG_GET_OD_SEL_REG` | `godsel` | `ODSEL` | 0–5 |
|BOARD SECTION||||
| `HKDIG_GET_BOARD_STATUS` | `gbds` | `BDSTATUS` | – |
| `HKDIG_GET_BOARD_CNTRL` | `gbdc` | `BDCONTROL` | – |
|VOLTAGE & CURRENT SECTION||||
| `HKDIG_GET_BOARD_3V3A` | `g3v3a` | `3V3A` | – |
| `HKDIG_GET_BOARD_12VA` | `g12va` | `12VA` | – |
| `HKDIG_GET_BOARD_I12V` | `gi12v` | `I12V` | – |
| `HKDIG_GET_BOARD_5V0A` | `g5v0a` | `5V0A` | – |
| `HKDIG_GET_BOARD_5V0F` | `g5v0f` | `5V0F` | – |
| `HKDIG_GET_BOARD_C12V` | `gc12v` | `C12V` | – |
| `HKDIG_GET_BOARD_I5VA` | `gi5va` | `I5VA` | – |
| `HKDIG_GET_BOARD_I3V3A` | `gi3v3a` | `I3V3A` | – |
| `HKDIG_GET_BOARD_I12VA` | `gi12va` | `I12VA` | – |
| `HKDIG_GET_5V0A_EEPROM` | `g5v0e` | `5V0E` | – |
|TEMPERATURE SECTION||||
| `HKDIG_GET_BOARD_TFE` | `gtfe` | `TFE` | – |
| `HKDIG_GET_BOARD_TFPGA` | `gtfpga` | `TFPGA` | – |
| `HKDIG_GET_BOARD_TPWR` | `gtpwr` | `TPWR` | – |
| `HKDIG_GET_BOARD_TFER` | `gtfer` | `TFER` | – |
| `HKDIG_GET_BOARD_TFPGAR` | `gtfpgar` | `TFPGAR` | – |
| `HKDIG_GET_BOARD_TPWRR` | `gtpwrr` | `TPWRR` | – |
| `HKDIG_GET_BOARD_TCH0` | `gtch0` | `TCH0` | – |
| `HKDIG_GET_BOARD_TCH11` | `gtch11` | `TCH11` | – |
| `HKDIG_GET_BOARD_TCH0R` | `gtch0r` | `TCH0R` | – |
| `HKDIG_GET_BOARD_TCH11R` | `gtch11r` | `TCH11R` | – |
| `HKDIG_GET_BOARD_5VOD` | `g5vod` | `5VOD` | – |
| `HKDIG_GET_BOARD_I5VOD` | `gi5vod` | `I5VOD` | – |
| `HKDIG_GET_BME_DATA` | `gbme` | `TEMP`, `RELHUM`, `PRESS` | – |
|GENERIC SECTION|||||
| `HKDIG_GET_EEPROM_OUI` | `goui` | `OUI` | – |
| `HKDIG_GET_EEPROM_EID` | `geid` | `EID` | – |
| `HKDIG_RO_FMON_N` | `grof` | `FMON` | 0–17 |
| `HKDIG_GET_UPTIME` | `gupt` | `UPTIME` | – |
| `HKDIG_GET_CLOCK` | `gclk` | `DAQCLOCK` | – |
| `HKDIG_GET_TLNK_LOCK` | `gtlck` | `TLNLOCK` | – |
|PRODUCTION TESTBENCH SECTION|||||
| `HKDIG_GET_TB_REG` | `gtbr` | `TBREG` | – |
| `HKDIG_GET_TB_ST` | `gtbs` | `TBST` | – |

> **Note:** `TEMP`, `RELHUM`, and `PRESS` are all served from a single BME280
> read (`HKDIG_GET_BME_DATA` / `gbme`), which returns temperature, relative
> humidity, and pressure together. The service reads the calibration parameters
> using commands `gtcal`, `ghcal`, and `gpcal`, decoding the returned values
> in the proper way.
