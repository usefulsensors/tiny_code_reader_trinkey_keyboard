# Tiny Code Reader CircuitPython Example
How to access Useful Sensor's Tiny Code Reader from CircuitPython.

## Introduction

The Tiny Code Reader is a small hardware module that's intended to make it easy
to scan QR codes. It has an image sensor and a microcontroller with pretrained
software and outputs information from any identified codes over I2C.

There's a [detailed developer guide](https://usfl.ink/tcr_dev)
available, but this project has sample code that shows you specifically how to 
get the module up and running using CircuitPython on a Trinkey, and output any
recognized code as text to an attached computer as if the module was a keyboard.

## Setting up Circuit Python

You should read the [official guide to setting up CircuitPython on a Pico](https://learn.adafruit.com/getting-started-with-raspberry-pi-pico-circuitpython)
for the latest information, but here is a summary:

 - Download CircuitPython for the for your board from circuitpython.org. For
 example the Pico version is available at https://circuitpython.org/board/raspberry_pi_pico/.
 This project has been tested using the `8.2.0` version.
 - Hold down the `bootsel` button on the Pico and plug it into a USB port.
 - Drag the CircuitPython uf2 file onto the `RPI-RP2` drive that appears.

Once you've followed those steps, you should see a new `CIRCUITPY` drive appear.
You can now drag `code.py` files onto that drive and the Pico should run them.

## Install Libraries

The application works by emulating a keyboard and sending keypresses to the main
machine to lock the screen, or minimize the main window. We need the [adafruit_hid library](https://docs.circuitpython.org/projects/hid/en/latest/)
to do the emulation, so the first step is to download a big bundle of all the
CircuitPython libraries from [circuitpython.org/libraries](https://circuitpython.org/libraries). You'll need to find the right bundle for your CircuitPython version.

Once you have that downloaded, unpack the bundle on your local machine. In the
file viewer, go to the `lib` folder within the unpacked bundle and copy the
`adafruit_hid` directory into the `lib` folder on the `CIRCUITPYTHON` drive.
This should install the library we need to emulate a keyboard.

## Wiring Information

Wiring up the device requires 4 jumpers, to connect VDD, GND, SDA and SCL. The 
example here uses I2C port 0, which is assigned to GPIO4 (SDA, pin 6) and GPIO5
(SCL, pin 7) in software. Power is supplied from 3V3(OUT) (pin 36), with ground
attached to GND (pin 38).

The Trinkey comes with a QWIIC connector, so you should just be able to connect
a QWIIC to QWIIC cable between the reader and the board's port. 

## Reading QR Codes

Copy the `code.py` file in this folder over to the `CIRCUITPY` drive. If you
connect over `minicom` or a similar program to view the logs, you should see
information about the codes seen by the sensor printed out, and messages about
any errors encountered.

You should also be able to see the contents of any QR codes typed into your
attached computer, as if the Trinkey were a keyboard. For example, if you
make sure you have your cursor in a text window and then open [an example QR code](https://en.wikipedia.org/wiki/QR_code#/media/File:QR_code_for_mobile_English_Wikipedia.svg)
on your phone and hold it about fifteen centimeters or six inches from the 
module, facing the camera, you should see `http://en.m.wikipedia.org` typed in,
followed by a newline. As you present other codes, those should also appear on
your main machine as if they have been typed in.

Not all Unicode codes will be supported by the Adafruit keyboard library we're
using, so if you do have more than just ASCII you may want to look into the
[alternative keyboard layouts](https://learn.adafruit.com/make-it-a-keyboard/circuitpython#keyboard-layouts-3103154)
that are available.

## Troubleshooting

### Power

The first thing to check is that the sensor is receiving power through the
`VDD` and `GND` wires. The simplest way to test this is to look at the LED on
the front of the module. Whenever it has power, it should be flashing blue
multiple times a second. If this isn't happening then there's likely to be a
wiring issue with the power connections.

### Communication

If you see connection errors when running the code detection example, you may
have an issue with your wiring. Connection errors often look like this:

```bash
Traceback (most recent call last):                                                    
  File "code.py", line 50, in <module>                                                
OSError: [Errno 19] No such device 
```

To help track down what's going wrong, you can uncomment the following lines in
the script:

```python
while True:
    print(i2c.scan())
    time.sleep(TINY_CODE_READER_DELAY)
```

This will display which I2C devices are available in the logs. You want to make
sure that address number `12` is present, because that's the one used by the
person sensor. Here's an example from a board that's working:

```bash
[12]  
```

You can see that `12` is shown. If it isn't present then it means the sensor
isn't responding to I2C messages as it should be. The most likely cause is that 
there's a wiring problem, so if you hit this you should double-check that the 
SDA and SCL wires are going to the right pins.

## Going Further

This script is designed to be a simple standalone example of reading data from
the Tiny Code Reader, but you should check out the sample code in the [Developer Guide](https://usfl.ink/tcr_dev)
to see more advanced use cases like provisioning wifi or keyboard emulation.
