# Slow control

This is the target slow control interface which will be used in the experiment to communicate with the Digitizer. The slow control channel to communicate with the digitizer board is the serial port connecting it to the DPB. The DPB implements a service, called `dpb-slowcontrolapp`, which manages such interface hiding the commands flowing between the two boards.

Details of this implementation can be found [here](https://gitlab.cern.ch/hk-dpb-team/hk_petalinux_project_dpb2_2022_2/-/blob/master/doc/dpb-slowcontrolapp-howto.md?ref_type=heads).

In general, commands can be used to read/write registers or start procedure on the Digitizer.
