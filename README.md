# SatNOGS Rotator Firmware

Firmware [SatNOGS Rotator Controller](https://gitlab.com/librespacefoundation/satnogs/satnogs-rotator-firmware).

Repository includes all source files for the SatNOGS rotator controller Firmware.

Electronics can be found on [satnogs-rotator-controller](https://gitlab.com/librespacefoundation/satnogs/satnogs-rotator-controller)

## Instructions

In order to use this code, you need to install
 * AccelStepper library
 * PID_v1 library
 * Wire library

You need to choose the version of the Firmware you will be utilizing based on your controller and rotator setup. Namely we have two different versions (one for DC motors and one for Stepper motors).

##### Steps

* Download arduino IDE
* Add these lines in /arduino-1.8.5/hardware/arduino/avr/boards.txt

```
##############################################################
satnogs.name=SatNOGS
satnogs.upload.tool=avrdude
satnogs.upload.protocol=buspirate
satnogs.upload.maximum_size=32256
satnogs.upload.maximum_data_size=2048
satnogs.upload.speed=115200

satnogs.bootloader.tool=avrdude
satnogs.bootloader.low_fuses=0xFF
satnogs.bootloader.high_fuses=0xDE
satnogs.bootloader.extended_fuses=0xFD
satnogs.bootloader.unlock_bits=0x3F
satnogs.bootloader.lock_bits=0x0F
satnogs.bootloader.file=optiboot/optiboot_atmega328.hex

satnogs.build.mcu=atmega328p
satnogs.build.f_cpu=16000000L
satnogs.build.board=AVR_SATNOGS
satnogs.build.core=arduino
satnogs.build.variant=eightanaloginputs
##############################################################
```

* Install [Arduino-Makefile](https://github.com/sudar/Arduino-Makefile)

```
ARDUINO_DIR – Directory where Arduino is installed
ARDMK_DIR – Directory where you have copied the makefile
AVR_TOOLS_DIR – Directory where avr tools are installed
USER_LIB_PATH – Directory where arduino libraries are installed
AVRDUDE – Directory where avrdude are installed
include – Directory where Arduino.mk are installed
```

* Build the code

```
make
```

* Upload using ISP

    * Connect [arduino](https://www.arduino.cc/en/Tutorial/ArduinoISP) or buspirate for ISP programming

        1. Pin 13 (SCK) to Pin 13 of arduino pro mini, ISP connector
        2. Pin 12 (MISO) to Pin 12 of arduino pro mini, ISP connector
        3. Pin 11 (MOSI) to Pin 11 of arduino pro mini, ISP connector
        4. Pin 10 (RESET) to Pin RST of arduino pro mini, ISP connector
        5. 5+ (Vcc) to Pin VCC of arduino pro mini, ISP connector
        6. Gnd (Gnd) to Pin GND of arduino pro mini, ISP connector

* BusPirate

```
satnogs.upload.protocol=buspirate (in board.txt)
ISP_PROG = buspirate (in Makefile)
```
```
make ispload
```

* Arduino

```
satnogs.upload.protocol=arduino (in board.txt)
ISP_PROG = arduino (in Makefile)
```

```
make ispload
```

* Upload using FDTI, but is necessary to uninstall arduino pro-mini from board

Connect [FTDI](https://learn.sparkfun.com/tutorials/using-the-arduino-pro-mini-33v)

```
satnogs.upload.protocol=arduino (in board.txt)
```
```
make upload
```
* Burn optiboot

Only with ISP programming

    * Arduino as ISP
    * BusPirate as ISP

```
make burn_bootloader
```

## Easycomm implemantation

* AZ, Azimuth, number - 1 decimal place [deg]
* EL, Elevation, number - 1 decimal place [deg]
* SA, Stop azimuth moving
* SE, Stop elevation moving
* RESET, Move to home position
* IP, Read an input, number
    * Temperature = 0
    * SW1 = 1
    * SW2 = 2
    * Encoder1 = 3
    * Encoder2 = 4
* VE, Request Version
* GS, Get status register, number
    * idle = 1
    * moving = 2
    * pointing = 4
    * error = 8
* GE, Get error register, number
    * no_error = 1
    * sensor_error = 2
    * homing_error = 4
    * motor_error = 8

TODO:
* VL, Velocity Left ,number [mdeg/s]
* VR, Velocity Right, number [mdeg/s]
* VU, Velocity Up, number [mdeg/s]
* VD, Velocity Down, number [mdeg/s]
* CR, Read config, register [0-x]
* CW, Write config, register [0-x]

## Controller Configurations

* Stepper Motor
    * Endstops
    * Encoders, optional
    * UART or R485
* DC Motor
    * Endstops
    * Encoders
    * UART or RS485

## Pins Configuration

```
M1IN1 6, Step or PWM1
M1IN2 5, Direction or PWM2
M1SF  7, Status flag
M1FB  A1, Load measurment

M2IN1 11, Step or PWM1
M2IN2 3, Direction or PWM2
M2SF  10, Status flag
M2FB  A0, Load measurment

MOTOR_EN 8, Enable/Disable motors

SW1 9, Endstop for axis 1
SW2 4, Endstop for axis 2

RS485_DIR 2, RS485 Half Duplex direction pin

SDA_PIN 3, Data I2C pin
SCL_PIN 4, Clock I2C pin

PIN12 12, Digital output pin
PIN13 13, Digital output pin
A2    A2, Analog input pin
A3    A3, Analog input pin
```

## Testing with hamlib - rotctl

Use commands of rotctl.

```
rotctl -m 204 -s 19200 -r /dev/ttyUSB1 -C timeout=200 -vvvvv
```

## Contribute

The main repository lives on [Gitlab](https://gitlab.com/librespacefoundation/satnogs/satnogs-rotator-firmware) and all Merge Request should happen there.

## License

&copy; 2014-2017 [Libre Space Foundation](http://libre.space).

Licensed under the [GPLv3](LICENSE)
