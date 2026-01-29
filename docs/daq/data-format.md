# Data format

There are 2 defined data formats used by the digitizer to transfer data to the DAQ: one is implemented by the `subhit` FW while the second doesn't not. The `subhit` protocol has a lot of imnlications and the 2 data formats are not compatible. In the following paragraphs both will be defined in detail, to allow users to manage data.

The `subhit` data format is the target protocol and will gradually replace the `no-subhit` one.

## No-subhit data format

This is the very first data format defined to transfer as much information as possible disregarding the rate optimization. It was defined to debug the FW and allow easily to decode all the received data. Moreover, this format does not impelement the `subhit` detection, as explained in the `DAQ` chapter of this documentation.

## Subhit data format

The `subhit` data format is the "engineered" protocol which exploits the capability of the Digitizer board to provide information during the dead time of charge measurement done by the ADCs of the High and Low range.