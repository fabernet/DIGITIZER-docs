# Introduction

This documentation explains the working principle of the Digitizer board, from the HW, FW, and SW point of view.

## Choosing the FW

FPGA FW is stored in __mcs__ files, whose names carry information about the FW content itself. The name (at least of the latest releases) consists of a _GW code_, a suffix which identify the serial protocol, and a suffix which identifies the DPB HW version compatibility. Currently, the FW files are stored in a dedicated shared folder on [_CERNbox_](https://cernbox.cern.ch/files/spaces/eos/user/f/fameli/DIGITIZER-FW?items-per-page=100&files-spaces-generic-view-mode=resource-table&tiles-size=2).

The __mcs__ file is intended to be uploaded into the non-volatile memory of the board, allowing a permanent programming of the FPGA at power up.

### GW Code

The so-called _GW code_ of the Digitizer consists of a 16-bit hex word, which specifies both the HW and GW versions of the bitstream or EEPROM _mcs_ file.

### GW Code meaning from version 0x6498 (Sep 2026)

The meaning of the GW code is quite straightforward: the MSB encode the HW version of ID and OD Digitizer, while the LSB encodes the GW version. Since GW version 0x98, this is the meaning of the code:

|  4 bit   |  4 bit  |         8 bit         |
|  :---:   |  :---:  |         :---:         |
|  ID-HW   |  OD-HW  |      GW VERSION       |

_ID-HW_ and _OD-HW_ codes represent the HW version of, resepctively, ID Digitizer board and OD Digitizer board.

The _GW VERSION_ encodes the version of the FW, identifying also sub-ranges according to the following table:

| Code Min |  Code Max |         FW type      |
|  :---:   |  :---:    |         :---:        |
|   0xF0   |   0xFF    | Production Testbench |
|   0xB0   |   0xEF    |       Subhit         |
|   0x00   |   0xAF    |     Non-subhit       |

### GW Code meaning until version 0x4696 (Sep 2026)

This coding was used until version _0x4696_: the main difference is the _FMT_ bit, which was used to distinguish between subhit and non-subhit FW versions. Now, this task is attributed to the range of the GW version code. Since GW version 7F, this is how the reg is managed:

|   4 bit   |    3 bit |  1 bit   |         8 bit         |
| :---:     | :---:    | :---:    |         :---:         |
|  ID-HW    |  OD-HW   |    FMT   |      GW VERSION       |

_ID-HW_ and _OD-HW_ codes represent the HW version of the resepctive HW boards.

_FMT_ specifies the data format: it is `1` for the _subhit_ FW, while it is `0` for older FW which does not implement the subhit acquisition.

### Serial protocol

The suffix which indicates the Serial protocol between DPB and DIG can be __GUI__ or __PKT__, according to GUI-like interface (interactive menus, string commands, etc.etc.), or PKT-like interface (ASCII protocol with packets, string commands, etc.). Both protocols can be used with a direct serial connection. The _dpb-slowcontrolapp_ service, running on the DPB, relies on the __PKT__ protocol. 

### DPB HW version

DPBv3 is the only HW version which needs to be distinguished from the others, since there is an inversion of the aurora link on DIG1 link. The suffix specifies __DPBv3__ when the FW is intended for HW version 3. If no suffix is present or if a specific DPB HW version is in the file name, then this is the version for DPB versions other than v3.
