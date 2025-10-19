# Production Line Conveyor-belt Introduction

This project consists of a PLC system utilizing a finite state machine (FSM) to control a conveyor-belt and several pneumatic outputs.

This project was completed at the Automated Controls Installation and Maintenance program in BCIT during the PLCs course, ACIM-5010.

This course served as an introduction to PLC systems. However, as a Mechatronics Technologist most of the topics covered in this course were known to me. As a result of this, I had decided to create the following PLC system utilizing a state machine where one was not required to complete the project.

## System Components

This PLC system consists of the following components:
* 1769-L30 Allen Bradley PLC
* PowerFlex 70 EC_I
* Conveyor Belt System Provided by the ACIM program.

![ACIM_Setup](https://github.com/user-attachments/assets/7a3474bb-a8ff-4f30-a2b6-0c59f0caaa18)

# Project Description

## Infeed

At the top of the line, a pneumatic feeder output is toggled at a specified frequency and feeds boxes to the moving belt.

https://github.com/user-attachments/assets/a8249a3a-254c-40e7-ab4d-80ad37c51a1e

The feeder is controlled by a Subroutine within the programmed finite state machine.
[feederPulse_SBR.pdf](https://github.com/user-attachments/files/22988367/feederPulse_SBR.pdf)

The Subroutine involves simple toggling logic and can be fully shown here.
<img width="1052" height="517" alt="image" src="https://github.com/user-attachments/assets/3caedaf9-042b-4212-9a71-a7fc1d9347e7" />

## Line Service

The state machine controls the moving platform depending on a sensory input and feeds boxes in a pre-determined fashion from left to right.

https://github.com/user-attachments/assets/62355579-662a-4570-8a21-ec45b6ff8299

The cradle/platform is controlled by yet another subroutine within the state machine. 
[lineLoad_SBR.pdf](https://github.com/user-attachments/files/22988379/lineLoad_SBR.pdf)

This subroutine can be briefly described as multiple cascading, latching timers that determine the time it takes for each actuator to energize. Below is a piece of the program, the full program can be obtained above.
<img width="1053" height="460" alt="image" src="https://github.com/user-attachments/assets/dfc8d7c5-e17b-4df4-a23f-302ea559bb71" />





