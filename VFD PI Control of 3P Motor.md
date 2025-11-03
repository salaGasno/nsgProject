## Table of Contents - VFD PI Control of 3-Phase Motor
- [System Components](#system-components)
- [Project Description](#project-description)
- [State Machine Program Description](#state-machine-program-description)
- [Safety Considerations](#safety-considerations)
- [Addendum](#addendum)



# Project Introduction

This project consists of programming both a VFD and a programmable relay in order to control a three-phase motor in a closed loop with the water level of a tank.

This project was completed at the Automated Controls Installation and Maintenance program in BCIT during the VFDs and Servos course, ACIM-5040.

This course focused on the application of AC Drives for motor control in industrial applications. Two VFDs were worked with during this course, one being the Altivar 31 VFD. However, the PowerFlex 70 EC_I was used for this project. This VFD's parameters were configured using Connected Components Workbench (CCW).

The Zelio SR3 B261FU, a programmable ACDC relay, was programmed in ZelioSoft2 software from Schneider Electric. Although not required, the system was programmed in a finite state machine fashion. As both the SW and relay were more limited than a traditional PLC system, they presented a couple of interesting challenges to overcome while developing the state machine. Their solutions are worth noting.

* Date completed: September 19th, 2025.
* Project duration: < 1 Month.

## System Components

This Control System Consists of:
* Zelio SR3 B261FU - Programmable Relay
* PowerFlex 70 EC_I - Variable Frequency Drive
* I/O Board Provided by ACIM Program
* Water-tank setup provided by ACIM Program
* DS Ultrasonic liquid level sensor
* Three-Phase Motor of undocumented make

![ACIM5040_Project_OneFrame](https://github.com/user-attachments/assets/71f662bb-c30e-4968-87a0-921fb1805e29)

# Project Description

With a Hand Off Auto switch on the auto position and the press of the start button, the VFD will apply voltage to the three-phase motor.

https://github.com/user-attachments/assets/87421e79-2719-43cb-8363-aa2508d682c1

The control system will read a VFD parameter entered through either HIM on the PowerFlex or through Connected Components Workbench. Once the proper interlocks are set, the VFD will apply voltage to the three-phase motor in a closed loop with the water level in the container. In the example below, the desired level is set to 60% of the calibrated sensor range.

https://github.com/user-attachments/assets/69067365-d887-4d36-aa55-0d5e4b9fc9ac







  
