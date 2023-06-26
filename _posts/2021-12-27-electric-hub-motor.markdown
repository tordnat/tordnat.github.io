---
layout: post
title: Scooter Hub Motor Teardown
---
# Scooter Hub Motor Teardown

As electric scooters and hoverboards have dropped in price considerably, so have their parts. Hub motors have massive potential for high-powered DIY projects. They have the lowest torque/price ratio of any cheap BLDCs on the market, and with a good motor driver they can be very useful. 

## Teardown


You can easily find out how many poles your motor is by counting magnets:

![hub motor](/assets/hub_motor.jpg)

* 27 pole slots
* 30 poles (30 magnets)
* 15 pole pairs

= 27N30P motor

### Hall effect encoder pinout
The model listed above has the current layout:
* Red is Vin, black is GND
* D = A  - yellow
* b = b - green
* c = c - blue 

### How to find the KV of a BLDC-motor

We want to measure phase to phase voltage, so connect an oscilloscope where the probe is on one phase/connector, and ground is on another.

* Spin the motor with a constant velocity 
* Measure the amplitude of the sinusoidal signal you get
* Read the period of the signal 
* Calculate f = 1/T

Since the motor has 15 pole pairs, we know that the electrical frequency is 15 times faster than the rotational speed of the motor. We then know that the rotation per second is equal to f/15. Times this with 60, and we have the RPM. 
* KV = RPM/amplitude

### Measurements

* T = 22.5 ms
* A = 16V
* f = 44.4 Hz
* RPS = 44.4Hz/15(pole pairs) -> 2.96 rotation pr sek
* rpm = 177.7
* KV = rpm/A = 11.1

## Torque Test
I'm using an ODrive as a motor driver using the hall-effect sensors as feedback.
* Testing with 0.32 m lever and a scale
* Crude configuration gave stable torque in 10-13 Nm range

## Sources

* Calculate windings on a motor https://www.emetor.com/windings/
* ODrive guide to hub motors https://docs.odriverobotics.com/hoverboard
