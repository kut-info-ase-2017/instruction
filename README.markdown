# References

* Raspberry Pi Starter Kit: learning course of sensors in the startar kit http://osoyoo.com/2017/07/13/raspberry-pi-3-starter-learning-kit-introduction/#3

* Raspbian OS (variant of Ubuntu Linux)
https://www.raspberrypi.org/downloads/raspbian/

# Item List

* In PL-2 Raspberry Pi Container
  * Raspberry Pi
  * USB power cable
  * USB SD card adapter
  * Raspberry Pi paper box (do not damage)
  * IO board (do not damage)
  * SD card

* In Raspberry Pi Starter Kit
  * Raspberry Pi 3 Starter Learning Kit Packing List
  * items listed on the Packing List

# Getting started

(read Raspberry Pi Starter Kit Lesson 1
  http://osoyoo.com/2017/06/20/raspberry-pi-3-basic-tutorial/)

* Connect Raspberry pi
  * keyboard (USB)
  * display (HDMI)
  * power cable (USB micro)
* Log in
  * user: pi, password: raspberry
* Set-up SSH (Section B. Remotely in the on-line material)
  * Type `sudo systemctl enable ssh` in a terminal.
  * Find the IP address assigned to your Raspberry Pi by `ifconfig`.
  * Log in though SSH from your laptop: `ssh pi@<IP address>`
* Power off
  * Type `sync` to ensure all data is written to the SD card.
  * Unplug the power cable.

# Light LED

(read Raspberry Pi Starter Kit Lesson 4 http://osoyoo.com/2017/06/22/intall-wiringpi/)

*REMARK: Be careful not to destroy devices.  DO NOT connect 5V/3V/GND pins each other or directly to a signal pin.   LED has virtually no resistance.  Insert a register.*

## Preliminaries

We use GPIO (General Purpose I/O) of Raspberry Pi to control peripherals such as LED and sensors.  Raspberry Pi has multiple GPIO pin, each is identified by either BCM number or Pin number.  We use BCM in this course. (Refer to http://osoyoo.com/2017/06/26/introduction-of-raspberry-pi-gpio/).  Use of GPIO number is confusing; BCM number is printed as GPIO number on a connector while pin name "GPIO. XX" is used in elsewhere.

GPIO pin can be controlled with `Pi.GPIO` module from Python programs.

## Instruction

* Connect Raspberry Pi to a bread board
* Construct a circuit on the bread board
  * connect BCM Pin#17 to the longer pin of LED
  * connect the sorter pin with either pin of a register (200 ohm)
  * connect the other pin of the register to GND pin of Raspberry pi.
  <img src="http://osoyoo.com/wp-content/uploads/2017/06/Untitled-Sketch_bb.jpg">
* Plug the power of your Raspberry Pi and log in.
* Execute the following python program.  This program can be found at http://osoyoo.com/driver/pi3_start_learning_kit_lesson_4/pythontest.py

```
#!/usr/bin/python

###########################################################################
#Filename      :pythontest.py
#Description   :blink LED
#Author        :alan
#Website       :www.osoyoo.com
#Update        :2017/06/20
############################################################################

import RPi.GPIO as GPIO
import time

# set GPIO 0 as LED pin
LEDPIN = 17

#print message at the begining ---custom function
def print_message():
    print ('|********************************|')
    print ('|            blink LED           |')
    print ('|      ------------------------- |')
    print ('|        LED connect to GPIO0    |')
    print ('|                                |')
    print ('|        LED will blink at 500ms |')
    print ('|      ------------------------- |')
    print ('|                                |')
    print ('|                          OSOYOO|')
    print ('|********************************|\n')
    print ('Program is running...')
    print ('Please press Ctrl+C to end the program...')

#setup function for some setup---custom function
def setup():
    GPIO.setwarnings(False)
    #set the gpio modes to BCM numbering
    GPIO.setmode(GPIO.BCM)
    #set LEDPIN's mode to output,and initial level to LOW(0V)
    GPIO.setup(LEDPIN,GPIO.OUT,initial=GPIO.LOW)

#main function
def main():
    #print info
    print_message()
    while True:
       GPIO.output(LEDPIN,GPIO.HIGH)
       print('...LED ON\n')
       time.sleep(0.5)

       GPIO.output(LEDPIN,GPIO.LOW)
       print('LED OFF...\n')
       time.sleep(0.5)
       pass
    pass

#define a destroy function for clean up everything after the script finished
def destroy():
    #turn off LED
    GPIO.output(LEDPIN,GPIO.LOW)
    #release resource
    GPIO.cleanup()
#
# if run this script directly ,do:
if __name__ == '__main__':
    setup()
    try:
            main()
    #when 'Ctrl+C' is pressed,child program destroy() will be executed.
    except KeyboardInterrupt:
        destroy()
```

Read the on-line material for the details of this program.
http://osoyoo.com/2017/06/23/python-light-led/


# Temperature and humidity sensor module

(read Raspberry Pi Starter Kit Lesson 17 http://osoyoo.com/2017/07/06/dht11/)

DHT11 module is a sensor module on which necessary registers are implemented.  All you have to do is to connect to appropriate pins of  Raspberry pi.

## Instruction

* Unplug the power cable of your Raspberry Pi
* Construct a circuit on the breadboard <img src="http://osoyoo.com/wp-content/uploads/2017/07/Untitled-Sketch_bb.png">
* Plug the power cable of your Raspberry Pi and log in.
* Execute the following program.

```
##########################################################################
# Filename    : dht11.py
# Description : test for SunFoudner DHT11 humiture & temperature module
# Author      : Alan
# Website     : www.osoyoo.com
# Update      : 2017/07/06
##########################################################################
import RPi.GPIO as GPIO
import time

#DHT11 connect to BCM_GPIO14
DHTPIN = 14

GPIO.setmode(GPIO.BCM)

MAX_UNCHANGE_COUNT = 100

STATE_INIT_PULL_DOWN = 1
STATE_INIT_PULL_UP = 2
STATE_DATA_FIRST_PULL_DOWN = 3
STATE_DATA_PULL_UP = 4
STATE_DATA_PULL_DOWN = 5

def read_dht11_dat():
    GPIO.setup(DHTPIN, GPIO.OUT)
    GPIO.output(DHTPIN, GPIO.HIGH)
    time.sleep(0.05)
    GPIO.output(DHTPIN, GPIO.LOW)
    time.sleep(0.02)
    GPIO.setup(DHTPIN, GPIO.IN, GPIO.PUD_UP)

    unchanged_count = 0
    last = -1
    data = []
    while True:
        current = GPIO.input(DHTPIN)
        data.append(current)
        if last != current:
            unchanged_count = 0
            last = current
        else:
            unchanged_count += 1
            if unchanged_count > MAX_UNCHANGE_COUNT:
                break

    state = STATE_INIT_PULL_DOWN

    lengths = []
    current_length = 0

    for current in data:
        current_length += 1

        if state == STATE_INIT_PULL_DOWN:
            if current == GPIO.LOW:
                state = STATE_INIT_PULL_UP
            else:
                continue
        if state == STATE_INIT_PULL_UP:
            if current == GPIO.HIGH:
                state = STATE_DATA_FIRST_PULL_DOWN
            else:
                continue
        if state == STATE_DATA_FIRST_PULL_DOWN:
            if current == GPIO.LOW:
                state = STATE_DATA_PULL_UP
            else:
                continue
        if state == STATE_DATA_PULL_UP:
            if current == GPIO.HIGH:
                current_length = 0
                state = STATE_DATA_PULL_DOWN
            else:
                continue
        if state == STATE_DATA_PULL_DOWN:
            if current == GPIO.LOW:
                lengths.append(current_length)
                state = STATE_DATA_PULL_UP
            else:
                continue
    if len(lengths) != 40:
        print "Data not good, skip"
        return False

    shortest_pull_up = min(lengths)
    longest_pull_up = max(lengths)
    halfway = (longest_pull_up + shortest_pull_up) / 2
    bits = []
    the_bytes = []
    byte = 0

    for length in lengths:
        bit = 0
        if length > halfway:
            bit = 1
        bits.append(bit)
    print "bits: %s, length: %d" % (bits, len(bits))
    for i in range(0, len(bits)):
        byte = byte << 1
        if (bits[i]):
            byte = byte | 1
        else:
            byte = byte | 0
        if ((i + 1) % 8 == 0):
            the_bytes.append(byte)
            byte = 0
    print the_bytes
    checksum = (the_bytes[0] + the_bytes[1] + the_bytes[2] + the_bytes[3]) & 0xFF
    if the_bytes[4] != checksum:
        print "Data not good, skip"
        return False

    return the_bytes[0], the_bytes[2]

def main():
    print "Raspberry Pi wiringPi DHT11 Temperature test program\n"
    while True:
        result = read_dht11_dat()
        if result:
            humidity, temperature = result
            print "humidity: %s %%,  Temperature: %s C" % (humidity, temperature)
        time.sleep(1)

def destroy():
    GPIO.cleanup()

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        destroy()
```
