# Appendix

In this paragraph, a detailed explanation of technical arguments is given.

## Slow control protocols

The slow control channel to communicate with the digitizer board is the serial port connecting it to the DPB. Natively, there are 2 possible communication protocols over this serial link: the so-called `GUI` and the `COPacket`.

The `GUI` protocol was developed as a mean to communicate "visually" with the digitizer board. There is a menu implemented and commands can be issued sending ASCII codes through a terminal or an application. This protocol is not suitable to be implemented in a "machine readable" way, since there is no control over the character stream sent or returned by the digitizer. This is meant essentially for "human" interaction through a terminal, though a lot of effort has been done to use it also in Python scripts.

The `COPacket`, on the contrary, though it is a very simple protocol, is thought to be "machine" friendly. It is an ASCII packet based protocol, with leading and trailing characters, commands and parameters well defined and of known length.

### dpb-slowcontrolapp

This is the target slow control procedure: the DPB implements a service, called `dpb-slowcontrolapp`, which manages the interface to the digitizer. Details of this implementation can be found [here](https://gitlab.cern.ch/hk-dpb-team/hk_petalinux_project_dpb2_2022_2/-/blob/master/doc/dpb-slowcontrolapp-howto.md?ref_type=heads).

### Digitizer GUI


### Digitizer COPkt

The protocol is base on packets starting with the `$` leading character and ending with the `#` trailing character. The command follows immediately after the `$` character; it is a string which may or may not require one or more parameters. For example, to have a list of available commands the `$help#` command can be issued.

```
//
//  COPacketCmdHkDig.cpp
//
//  Created by Faber on 13 Jul 2022
//  Copyright Â© 2018 Faber. All rights reserved.
//

#include "COPacketCmdHkDig.h"

const uint16_t   HkDigCmdSize = 99;
_COPacketCmdType HkDigCommands[HkDigCmdSize] = {
	// Parameters commands
	{"gthn", HKDIG_GET_THR_NUM,      " ch# Get ch thr"},
	{"sthn", HKDIG_SET_THR_NUM,      " ch thr# Set ch thr"},
	{"stha", HKDIG_SET_THR_ALL,      " thr# Set all thr"},

	{"gitn", HKDIG_GET_IT_NUM,       " ch# Get ch intT"},
	{"sitn", HKDIG_SET_IT_NUM,       " ch I# Set ch intT"},
	{"sita", HKDIG_SET_IT_ALL,       " I# Set all intT"},

	{"gdtn", HKDIG_GET_DT_NUM,       " ch# Get ch deadT"},
	{"sdtn", HKDIG_SET_DT_NUM,       " ch D# Set ch deadT"},
	{"sdta", HKDIG_SET_DT_ALL,       " D# Set all deadT"},

	// Calibrator commands
	{"scalm",  HKDIG_SET_CAL_TYPE,    " M# Cal Mode"},
	{"scale",  HKDIG_SET_CAL_N,       " ch 0/1# Set cal en"},
	{"scalp",  HKDIG_SET_CAL_POWER,   " 0/1# calb pwr"},
	{"scall",  HKDIG_SET_CAL_PLS_LEN, " L# pls len (10.4ns)"},
	{"scala",  HKDIG_SET_CAL_PLS_AMP, " A# pls amp"},
	{"gcala",  HKDIG_GET_CAL_PLS_AMP, "# pls amp"},
	{"scaltp", HKDIG_SET_CAL_NPDN,    " p2m# p2m (41.6ns)"},
	{"scaltm", HKDIG_SET_CAL_MUTE,    " m2e# m2e (10.4ns)"},
	{"scalte", HKDIG_SET_CAL_PLSEN,   " e2p# e2p (10.4ns)"},
	{"scalt",  HKDIG_SET_CAL_PERIOD,  " T# cal T (50us)"},
	{"scalg",  HKDIG_SET_CAL_GAIN,    " G# Cal Gain"},

	// DAQ commands
	{"glgc", HKDIG_GET_CHN_LG_CHG,   " ch# Get LG charge"},
	{"ghgc", HKDIG_GET_CHN_HG_CHG,   " ch# Get HG charge"},
	{"gchs", HKDIG_GET_CHN_STATUS,   " ch# Get ch status"},
	{"gchc", HKDIG_GET_CHN_CNTRL,    " ch# Get ch control"},
	{"schc", HKDIG_SET_CHN_CNTRL,    " ch ctl# Set ch control"},
	{"efen", HKDIG_START_FE_N,       " ch# En  ch FE"},
	{"dfen", HKDIG_STOP_FE_N,        " ch# Dis ch FE"},
	{"edqn", HKDIG_START_DAQ_N,      " ch# En  ch DAQ"},
	{"ddqn", HKDIG_STOP_DAQ_N,       " ch# Dis ch DAQ"},
	{"grof", HKDIG_RO_FMON_N,        " ch# RO  ch"},

	// Global commands
	{"efea", HKDIG_START_FE_ALL,     "# En  all FE"},
	{"dfea", HKDIG_STOP_FE_ALL,      "# Dis all FE"},
	{"edqa", HKDIG_START_DAQ_ALL,    "# En  all DAQ"},
	{"ddqa", HKDIG_STOP_DAQ_ALL,     "# Dis all DAQ"},
	{"tdcr", HKDIG_TDC_RST,          "# Tdc Rst"},

	// Data transmission commands
	{"sdmx", HKDIG_SET_AUR_DEMUX,    " D# set demux"},

	// Pedestal commands
	{"spedn", HKDIG_SET_PED_ENABLE,   " ch en# Set ch en"},
	{"gpedn", HKDIG_GET_PED_ENABLE,   " ch# Get ch en"},
	{"spedp", HKDIG_SET_PED_PERIOD,   " P# Set T (50us)"},
	{"gpedp", HKDIG_GET_PED_PERIOD,   "# Get T (50us)"},
	{"speds", HKDIG_SET_PED_STAGGER,  " S# Set stagger (10.4ns)"},
	{"gpeds", HKDIG_GET_PED_STAGGER,  "# Get stagger (10.4ns)"},

	// OD commands
	{"sodsel", HKDIG_SET_OD_SEL_REG, " ch 0/1# Set od ch"},
	{"godsel", HKDIG_GET_OD_SEL_REG, "# Get od sel"},

	{"ghwv", HKDIG_GET_HW_VER,       "# HW Ver"},
	{"ggwv", HKDIG_GET_GW_VER,       "# GW Ver"},
	{"ggwd", HKDIG_GET_GW_DATE,      "# GW Date"},
	{"gswv", HKDIG_GET_SW_VER,       "# SW Ver"},
	{"gbds", HKDIG_GET_BOARD_STATUS, "# BRD status"},
	{"gbdc", HKDIG_GET_BOARD_CNTRL,  "# BRD control"},
	{"gupt", HKDIG_GET_UPTIME,       "# Uptime"},
	{"raur", HKDIG_RST_AURORA_LINK,  "# Rst Aurora"},
	{"sclk", HKDIG_SET_CLOCK,        " val# loc(0)-dpb(1)"},
	{"gclk", HKDIG_GET_CLOCK,        "# Get clk"},
	{"gtlck", HKDIG_GET_TLNK_LOCK,   "# Get TLock"},
	{"spsc", HKDIG_SET_ADS_PSC,      "# Set PSC"},

	{"goui", HKDIG_GET_EEPROM_OUI,   "# Get OUI"},
	{"geid", HKDIG_GET_EEPROM_EID,   "# Get EID"},

	// Rate monitor commands
	{"grmp", HKDIG_GET_RMON_PER,     "# Get RMonPer"},
	{"srmp", HKDIG_SET_RMON_PER,     " per# Set RMonPer"},
	{"grma", HKDIG_GET_RMON_ADC_N,   " ch# Get ch ADCr"},
	{"grmt", HKDIG_GET_RMON_TDC_N,   " ch# Get ch TDCr"},
	{"grmf", HKDIG_GET_RMON_FMT_N,   " ch# Get ch FMTr"},
	{"grmm", HKDIG_GET_RMON_MUX_N,   "# Get MUXr"},
	{"grmr", HKDIG_GET_RMON_RST_N,   "# Get RSTr"},
	{"runr", HKDIG_RUN_RMON,         "# Run RMon"},
	{"rmne", HKDIG_RUN_RMON_EN,      "# En rmon"},
	{"rmnd", HKDIG_RUN_RMON_DIS,     "# Dis rmon"},

	// Sensor commands
	{"g3v3a",  HKDIG_GET_BOARD_3V3A,  "# 3V3A"},
	{"g12va",  HKDIG_GET_BOARD_12VA,  "# 12VA"},
	{"gi12v",  HKDIG_GET_BOARD_I12V,  "# I12V"},
	{"g5v0a",  HKDIG_GET_BOARD_5V0A,  "# 5V0A"},
	{"g5v0f",  HKDIG_GET_BOARD_5V0F,  "# 5V0F"},
	{"gc12v",  HKDIG_GET_BOARD_C12V,  "# C12V"},
	{"gi5va",  HKDIG_GET_BOARD_I5VA,  "# I5VA"},
	{"gi3v3a", HKDIG_GET_BOARD_I3V3A, "# I3V3A"},
	{"gi12va", HKDIG_GET_BOARD_I12VA, "# I12VA"},
	{"gtfe",   HKDIG_GET_BOARD_TFE,   "# T_FE"},
	{"gtfpga", HKDIG_GET_BOARD_TFPGA, "# T_FPGA"},
	{"gtpwr",  HKDIG_GET_BOARD_TPWR,  "# T_PWR"},
	{"gtfer",  HKDIG_GET_BOARD_TFER,  "# T_FE Raw"},
	{"gtfpgar",HKDIG_GET_BOARD_TFPGAR,"# T_FPGA Raw"},
	{"gtpwrr", HKDIG_GET_BOARD_TPWRR, "# T_PWR Raw"},

	{"gtch0",   HKDIG_GET_BOARD_TCH0,    "# T_CH0"},
	{"gtch11",  HKDIG_GET_BOARD_TCH11,   "# T_CH11"},
	{"gtch0r",  HKDIG_GET_BOARD_TCH0R,   "# T_CH0 Raw"},
	{"gtch11r", HKDIG_GET_BOARD_TCH11R,  "# T_CH11 Raw"},
	{"g5vod",   HKDIG_GET_BOARD_5VOD,    "# 5VOD"},
	{"gi5vod",  HKDIG_GET_BOARD_I5VOD,   "# I5VOD"},
	{"s5v0a",  	HKDIG_SET_5V0A,   		 " num# Set 5V0A"},
	{"s5v0e",  	HKDIG_SET_5V0A_EEPROM,   " num# WrEE"},

	{"stbr",  	HKDIG_SET_TB_REG,  		 " num# Set TBr"},
	{"gtbr",  	HKDIG_GET_TB_REG,  		 "# Get TBr"},

	{"gbme",   HKDIG_GET_BME_DATA,    "# BME"},
	{"gtcal",  HKDIG_GET_BME_TCAL,    "# TCal"},
	{"ghcal",  HKDIG_GET_BME_HCAL,    "# HCal"},
	{"gpcal",  HKDIG_GET_BME_PCAL,    "# PCal"},

    {"help",   HKDIG_HELP,            "#"},
    {"erro",   HKDIG_ERRO,            "#"}
};

_COPacketCmdList HkDigCmdList = {
    HkDigCmdSize,
    HkDigCommands
};
```

## Coonnecting through the DPB

The Digitizer communicates through a serial port: when it is connected to the DPB, the communication can be routed by the DPB.

After logging in the DPB with _ssh_, move to the script folder and activate socat for both Digitizers:
```
$ bash run-xocat.sh 1 dpb-dig0-port
$ bash run-xocat.sh 2 dpb-dig1-port
```
Since the OS instantiates the serial ports for the 2 Digitizers as `/dev/ttyUL1` and `/dev/ttyUL2`, the index `1` and `2` in the `run-socat.sh` command are fixed. Tipically, we choose `9001` as the dpb-dig-port for Digitizer 0, and `9002` for Digitizer 1.


On the Host, clone the  __DiscreteSoftware__ repo and, in the `docs` folder, run the vcom.sh script:
```
bash vcom.sh 0 dpb-ip-address dpb-dig-port
```
The `vcom.sh` script instantiates a serial port in the directory `install` of the repo. To test the _socat tunnelling_, use a serial communication program like `microcom` or `minicom`:
```
$ microcom -p <repo-path>/install/vmodem0
```

The python scripts of the __DiscreteSoftware__ repo are now able to communicate to the Digitizer using this _tunnel_ serial port.

For more detailed information, read the documentation in the folder `docs` inside the __DiscreteSoftware__ repo.
