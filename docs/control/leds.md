# Digitizer LEDs

There are 4 LEDs on the digitizer to help understand the state of the board.

## LD-12V

LD-12V is a red led close to the MiniSaS connector indicating that a 12V is applied.

## LED1 - Aurora link

LED1 is a green LED used to give information related to the connection between the DPB and the Digitizer. There are 2 possible cases:

- if the Digitizer is connected to the DPBv3, then an Aurora Simplex link is required and LED1 will be blinking in case of protocol lock
- if the Digitizer is connected to any version of the DPB other than 3, an Aurora Full-Duplex link is implemented and LED1 will be fixed on when the protocol is locked.

## LED2 - Timing link

When the timing link between the DPB and the DIG is active and the protocol is locked, LED2 will blink at a rate driven by the timing link clock and information. This led is driven automatically and no action must be taken by the user to control it.

## LED3-LED4

This 2 LEDs shows a binary counter, which gives the information about the microcontroller inside the FPGA is up and running.
