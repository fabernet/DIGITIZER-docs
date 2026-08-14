# Digitizer slow control

The Digitizer exchanges slow control commands through the serial port routed on the MiniSAS cable connected to the DPB. Since there are 2 Digitizers per DPB, there will be 2 serial ports, `/dev/ttyUL1` and `/dev/ttyUL2`, to connect, respectively, to _Digitizer 0_ and _Digitizer 1_.

The Digitizer slow control functions can only be accessed by means of a service, implemented on the DPB, called `dpb-slowcontrolapp`; this service bridges the custom protocol between the Digitizer and the DPB to expose APIs through the GbE interface.

As any other service in the Petalinux distribution for the DPB, the application can be monitored, started, stopped, and restarted using the usual commands:

- $ systemctl status dpb-slowcontrolapp
- $ systemctl start dpb-slowcontrolapp
- $ systemctl stop dpb-slowcontrolapp
- $ systemctl restart dpb-slowcontrolapp

Details of this implementation can be found in the [Petalinux repo documentation page](https://gitlab.cern.ch/hk-dpb-team/hk_petalinux_project_dpb2_2022_2/-/blob/master/doc/dpb-slowcontrolapp-howto.md?ref_type=heads).
