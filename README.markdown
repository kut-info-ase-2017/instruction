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

### GPIO

We use GPIO (General Purpose I/O) of Raspberry Pi to control peripherals such as LED and sensors.  Raspberry Pi has multiple GPIO pin, each is identified by either BCM number or Pin number.  We use BCM in this course. (Refer to http://osoyoo.com/2017/06/26/introduction-of-raspberry-pi-gpio/).  Use of GPIO number is confusing; BCM number is printed as GPIO number on a connector while pin name "GPIO. XX" is used in elsewhere.

GPIO pin can be controlled with `Pi.GPIO` module from Python programs.

### breadboard

We use a breadboard to construct a circuit.  Hols on a breadboard are connected as shown in the following picture.
<img src="BREADBOARD.png">

## Instruction

* Connect Raspberry Pi to a bread board
* Construct a circuit on the bread board (connection graph: http://osoyoo.com/wp-content/uploads/2017/06/Untitled-Sketch_bb.jpg)
  * connect BCM Pin#17 to the longer pin of LED
  * connect the sorter pin with either pin of a register (200 ohm)
  * connect the other pin of the register to GND pin of Raspberry pi.
* Plug the power of your Raspberry Pi and log in.
* Execute the python program found at the following URL. You need to use `sudo` to access privileged resources, i.e., GPIO.  
 http://osoyoo.com/driver/pi3_start_learning_kit_lesson_4/pythontest.py

# Temperature and humidity sensor module

(read Raspberry Pi Starter Kit Lesson 17 http://osoyoo.com/2017/07/06/dht11/)

DHT11 module is a sensor module on which necessary registers are implemented.  All you have to do is to connect to appropriate pins of  Raspberry pi.

## Instruction

* Unplug the power cable of your Raspberry Pi
* Construct a circuit on the breadboard (connection graph:http://osoyoo.com/wp-content/uploads/2017/07/Untitled-Sketch_bb.png)
* Plug the power cable of your Raspberry Pi and log in.
* Execute the python program found at the folloing URL: http://osoyoo.com/driver/pi3_start_learning_kit_lesson_17/dht11.py
