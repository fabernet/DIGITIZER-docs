# Appendix

This section is dedicated to detailed explanation of technical arguments.

## Connecting to the Digitizer through the DPB serial port

The Digitizer communicates through a serial port: when it is connected to the DPB, the communication can be routed by the DPB.

After logging in the DPB with _ssh_, move to the script folder and activate socat for both Digitizers:
```
$ bash run-socat.sh 1 dpb-dig0-port
$ bash run-socat.sh 2 dpb-dig1-port
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
