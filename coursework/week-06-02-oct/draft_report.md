# Small Animal Environment Control System ___DRAFT___

## The Problem
Small animals such as birds can make wonderful pets. One of the hardest things to account for when housing small pets is ensuring that their environmental conditions are ideal to maintain their health. large fluctuations in temperature and exposure to volatile chemicals especially in urban environments can contribute to poor health and injury.

## The Product

An Automated Environment Control System (AECS) controlled by a small low power micro controller with temperature, and air quality sensors that controls a fan and/or heating/cooling system. 

An AECS can use a **temperature sensor** to take readings of the environment both inside and outside of a pet enclosure and activate the **fan**/heating and cooling system in order to maintain a desirable temperature range inside the enclosure. 
By using an **air quality sensor**, the fan could be disabled to prevent temporary spikes in harmful chemicals from being vented into the enclosure in addition to being used as an indicator for required cleaning due to build-up of volatile compounds inside the enclosure.

As an additional feature the AECS can communicate with a more end user device over **serial** or ethernet to give updates on the state and readings taken by the system.

## Hardware

### Component List

| Component | Quantity | Product ID | Description |
|-|-:|-|-|
|Control System| 1 | STM32F411E-DISCO | MCU |
|Fan| 1| NOCTUA NF-P14s-1500PWM | PC Case Fan with PWM speed control and tachometer  |
| Volatiles Sensor | 1| SEN-CCS811 | Air Quality sonsor  |
| Temperature Sensor | 2 | DHT11 | Temperature and Humidity Module |
| IO Communication | 1 |  | Serial RS232 |
| Storage | 1 | 
| Power Supply | 1 | 


### Hardware Diagram 
[Hardware Diagram](hardware-diagram.png)


### Possible Hardware Additions 
* Ethernet
* Heater
* Cooler
* Additional fans
* Camera
* Microphone

## Software

The software is in C++ using the C++17 standard,
It uses a Real Time Operating System FreeRTOS for scheduling with a task asynchronous focused coding style.

The main runtime initialises the flash storage then watcher tasks that monitor peripherals (temp sensor, air quality sensor, fan etc.) followed by the user communication modules (serial/ethernet).

The system then links the communication between modules so that the individual modules can send/receive messages from one and other.

When communications come in from the user it may consist of either a request for an environment variable (e.g. temp) or a command to take some action ( e.g. manually override and turn on the fan). these communications are dispatched to the relevant module with a callback to provide necessary feedback. 

For Example: If the user requests the current temperature via a temperature request packet sent over serial. That packet is read by the serial communication driver then the serial driver sends a request to the temperature sensor driver with a callback that takes the temperature value transmits a temperature value packet.

Another example is if the user sends a command packet requesting the fan be turned off. That packet is also read by the serial communication driver which sends it to the fan driver along with a callback that will send a confirmation packet back over the serial to the user.

## Assembly

### Building the firmware from sorce
The firmware is written in c++ and uses the CubeIDE toolchain with GCC to compile and flash to the STM compute module.

The system is assembled as laid out in the following circuit diagram

### Testing

The logic unit tests are written in C++ for x86 compilation on a developer computer.

The system is integration tested by performing the following actions and checking that the system responds accordingly
| Action | Follow-up
|--------|-------|
| Send a temperature request| Wait up to 1 second for a temperature response Check the value to ensure it is reasonable|
| Send a fan speed request | Wait up to 1 second for a fan speed response. Check the response is reasonable|

### Power
The system is powered with a 12V 65W power supply (which was on hand). The system at max draw uses barely more than the minimum power required to run the fan ~2.5W


## Production Requirements 
The system is designed to be constructed with minimal electrical component customisation. The use of jumper leads and standard common use interfaces enables construction by a person with minimal understanding of embedded system development.

Since this is intended as a development/prototype rig it bulky and not ideal for commercial use. However, it should be relatively easy to adapt to a marketable system with the use of custom circuit boards and the use of less end user targeted components.