# Production Line Conveyor-belt Introduction

This programming project consists of a PLC system utilizing a finite state machine (FSM) to control a conveyor-belt and several pneumatic outputs.

This project was completed at the Automated Controls Installation and Maintenance program in BCIT during the PLCs course, ACIM-5010.

This course served as an introduction to PLC systems. However, as a mechatronics technologist, most of the topics covered in this course were known to me. As a result, I had decided to create the following PLC system project utilizing a state machine even though one was not required for the course.

Date completed: September 7th, 2025.
Project duration: < 1 Month.

## System Components

This PLC system consists of the following components:
* 1769-L30 Allen Bradley PLC
* PowerFlex 70 EC_I
* Point IO
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

The process repeats indefinitely unless a red button is pressed. If this button is pressed, the line will service all three lines before coming to a soft stop. If the emergency stop is pressed, the e-stop button will need to be unlatched and a button sequence must be executed to resume normal function.

https://github.com/user-attachments/assets/adb6910d-be34-4707-b5a2-671e6af394ea

# State Machine Program Description

As this was mainly a programming project, a proper amount of care was put into the state machine's integrity.
The following describes the state machine sequence, a few subroutines and a bonus pause feature.

## Finite State Machine

The finite state machine will be discussed and can be found here. [ConveyorBelt_FSM.pdf](https://github.com/user-attachments/files/22994410/ConveyorBelt_FSM.pdf)

The state machine consists of 9 states it can transition to. Below is a rough sketch of the state diagram.
<img width="440" height="328" alt="stateDiagram0" src="https://github.com/user-attachments/assets/40aa792d-2a3f-4b7a-9464-d2ab5947cccf" />

## State 100 - E-STATE

The emergency state is entered from almost any other state as the rung for it is scanned asynchronously from the state machine.
<img width="1053" height="155" alt="image" src="https://github.com/user-attachments/assets/456fa9d9-b6b8-4574-956c-4384bc1c7391" />
This state can be exited through the unlatching of the emergency stop button followed by a sequence of button presses within an aloted time.
<img width="1052" height="295" alt="image" src="https://github.com/user-attachments/assets/2bc004b8-5d79-4de9-8088-32e46c24fc82" />

## State 0 - IDLE

This state is entered through the first scan status bit and is the starting point of the state machine.
During this state, the green light flashes to indicate that the system is ready. The state is exited via the press of the green pushbutton.

## State 1 - INITIALIZE

This state resets counters involved with the line servicing function of the program and a timer involved with safely restarting the system after an e-stop. The green light will cease to toggle and will be continuously energized when this state is entered.
<img width="1057" height="185" alt="image" src="https://github.com/user-attachments/assets/f7b93263-d15f-47e1-92ac-7bf8c3ee8741" />





