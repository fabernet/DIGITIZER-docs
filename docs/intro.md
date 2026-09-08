# Introduction

This documentation explains the working principle of the Digitizer board, from the HW, FW, and SW point of view.

## Choosing the FW

FPGA FW is stored in __mcs__ files, whose names carry information about the FW content itself. The name (at least of the latest releases) consists of a _GW code_, a suffix which identify the serial protocol, and a suffix which identifies the DPB HW version compatibility.

The __mcs__ file is intended to be uploaded into the non-volatile memory of the board, allowing a permanent programming of the FPGA at power up.

### GW Code

The so-called GW code of the Digitizer consists of a 16-bit hex word, which specifies HW and FW version of the file.

Description : Package for HYPER-K GateWare (GW) versions
Since GW version 7F, this is how the reg is managed:

|   4 bit   |    3 bit |  1 bit   |         8 bit         |
| :---:     | :---:    | :---:    |         :---:         |
|  ID-HW    |  OD-HW   |    FMT   |      GW VERSION       |

_ID-HW_ and _OD-HW_ codes represent the HW version of the resepctive HW boards.

_FMT_ specifies the data format: it is `1` for the _subhit_ FW, while it is `0` for older FW which does not implement the subhit acquisition.

Finally, the _GW VERSION_ states the version of the FW.

### Serial protocol

The suffix which indicates the Serial protocol between DPB and DIG can be __GUI__ or __PKT__, according to GUI-like interface (interactive menus, string commands, etc.etc.), or PKT-like interface (ASCII protocol with packets, string commands, etc.).

### DPB HW version

DPBv3 is the only HW version which needs to be distinguished from the others, since there is an inversion of the aurora link on DIG1 link. The suffix specifies __DPBv3__ when the FW is intended for HW version 3. If no suffix is present or if a specific HW version is in the file name, then this is the version for DPB versions other than v3.