## Table of Contents - Production Line Control System
- [System Components](#system-components)
- [Project Description](#project-description)
- [State Machine Program Description](#state-machine-program-description)
- [Safety Considerations](#safety-considerations)
- [Addendum](#addendum)



# Project Introduction

This programming project consists of a PLC system utilizing a finite state machine (FSM) to control a conveyor-belt and several pneumatic outputs.

This project was completed at the Automated Controls Installation and Maintenance program in BCIT during the PLCs course, ACIM-5010.

This course served as an introduction to PLC systems. However, as a mechatronics technologist, most of the topics covered in this course were known to me. As a result, I had decided to create the following PLC system project utilizing a state machine even though one was not required for the course.

* Date completed: September 7th, 2025.
* Project duration: < 1 Month.

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

## Line Service

The state machine controls the pneumatic ram's moving platform depending on a sensory input and feeds boxes in a pre-determined fashion from left to right.

https://github.com/user-attachments/assets/62355579-662a-4570-8a21-ec45b6ff8299

The process repeats indefinitely unless a red button is pressed. If this button is pressed, the line will finish servicing all three lines before coming to a halt. If the emergency stop is pressed, the e-stop button will need to be unlatched and a button sequence must be executed to resume normal function.

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

This state resets counters involved with the line servicing function of the program and a timer involved with safely restarting the system after an e-stop. The green light will cease to toggle and will be continuously energized when this state is entered and will remain until the yellow button is pressed and the conveyor belt's desired speed is reached.
<img width="1057" height="185" alt="image" src="https://github.com/user-attachments/assets/f7b93263-d15f-47e1-92ac-7bf8c3ee8741" />

## State 2 - INITIALIZE VFD

This state is reached after pressing the yellow pushbutton. This state energizes the green light and continuously toggles the amber light to indicate that the VFD is energizing the conveyor belt motor and its speed is being monitored.
<img width="1262" height="130" alt="image" src="https://github.com/user-attachments/assets/73a1210d-802c-475d-a1f0-550fcedc0b63" />
This state will energize two pins on the VFD, the stop (active-low) and start (active-high) pins. This starts the motor.
Once the motor reaches the desired speed, a transition occurs and the green light is de-energized at the time the amber light is energized.

## State 3 - LINE 1 SERVICE

This state services the first line of production. 
<img width="1262" height="273" alt="image" src="https://github.com/user-attachments/assets/094c5529-d417-4987-9e2e-e51cf2555ca7" />

* This state calls two subroutines that energize the box feeder, and the pneumatic ram. [feederPulse_SBR.pdf](https://github.com/user-attachments/files/22994851/feederPulse_SBR.pdf), [lineLoad_SBR.pdf](https://github.com/user-attachments/files/22994860/lineLoad_SBR.pdf).
<img width="929" height="116" alt="image" src="https://github.com/user-attachments/assets/5d7b72d2-70c0-4aa2-a917-1d68e0d136bc" />
<img width="923" height="97" alt="image" src="https://github.com/user-attachments/assets/a174ae15-3dbb-4f8d-b50f-b019a210f4ec" />

* The feederPulse subroutine involves simple toggling logic and can be fully shown here. This subroutine is called for every subsequent line-servicing state and will not be mentioned going forward.
<img width="1052" height="517" alt="image" src="https://github.com/user-attachments/assets/3caedaf9-042b-4212-9a71-a7fc1d9347e7" />

* The lineLoad subroutine can be briefly described as multiple cascading, latching timers that determine the time it takes for each actuator to energize. Below is a piece of the program, the full program can be obtained above. This subroutine is also called for every subsequent line-servicing state.
<img width="1053" height="460" alt="image" src="https://github.com/user-attachments/assets/dfc8d7c5-e17b-4df4-a23f-302ea559bb71" />

* The state logic counts the amount of times the ram is actuated and considers a box to have been feed to an output line. The preset value was arbitrary in the context of this project. This logic is applied to every subsequent line-servicing state, the only difference being the fact that the pneumatic guide - the output responsible for moving the ram left and right - is set to energize and allow the ram to move where line 1 is located.
<img width="923" height="312" alt="image" src="https://github.com/user-attachments/assets/ece9abe8-a697-4ac3-a526-3d4e534983f5" />

* Something worth noting is the use of the transition delay timer (transDelay) used in each line-servicing state. This avoids the ram from energizing more than once in an extremely quickly fashion upon a state transition due to a box being present in front of the sensor responsible for triggering the ram. In short, this delay avoids a loss of control of the pneumatic ram upon exiting a line-servicing state and entering another.

As the line is fully loaded, the counter associated with the line sets its done bit, the non-retentive timer finishes its count and the next state transition occurs.
<img width="923" height="209" alt="image" src="https://github.com/user-attachments/assets/b6374520-cbda-411a-bab6-2eef34d7f08d" />

## States 4 and 5 - LINE 2 & 3 SERVICE

States 4 and 5 are a repeat of the previously discussed state above. However, at the end of state 5, the system will either stop or continue from state 3 depending on whether the red pushbutton was pressed. More info below.

## Soft stopping

The program will loop from state 3 - 5 if there is no intervention from an operator. If the red pushbutton is pressed, however, a soft stop flag will be set and be held in memory until it is checked once the final output-line is loaded with the predetermined number of boxes.

* The press of the red pushbutton will issue the soft stop flag in any state that is not the initial idle state.
<img width="928" height="163" alt="image" src="https://github.com/user-attachments/assets/2ccdd98b-fbe1-4672-8024-b04924a68c51" />

* Once the final state for line-servicing is reached the flag is checked. If false, the next state will be state 3 and the process will restart.
* If it is true, however, the state will reset back to idling, state 0.
<img width="923" height="420" alt="image" src="https://github.com/user-attachments/assets/2516da61-0f40-4308-8a44-eba46cd0307f" />

* Every time State 0 is entered, the soft stop flag is cleared to ensure normal operation.
<img width="927" height="99" alt="image" src="https://github.com/user-attachments/assets/a368ad00-502b-463e-a9e0-de857256b26f" />

This creates the soft-stop feature required for the project.

## Pause feature

The pause feature was not a requirement and was included at my leisure. The implementation is simple and has little implication on the rest of the system.

* Pressing the yellow pushbutton stores the current state in a buffer. Since the counters are not reset, pausing does not affect the counters responsible for keeping track of the output.
<img width="924" height="261" alt="image" src="https://github.com/user-attachments/assets/5bac4d6c-0fdc-4665-99f6-ce2d6150c60f" />

* Two states are allocated to this function to ensure resuming is possible only when it is safe to do so.
<img width="923" height="193" alt="image" src="https://github.com/user-attachments/assets/54b6b097-7755-4b32-b6e8-6a72f8811529" />

* In the lineLoad subroutine, the state is checked for pause. While the system is paused, the ram should never energize even if a box is present.
<img width="1054" height="146" alt="image" src="https://github.com/user-attachments/assets/84c1b7d7-2896-4b3d-b898-eeff3124c656" />

# Safety Considerations

## Redundant Interlocks
Throughout the state machine there are several interlocking conditions of similar nature.
<img width="923" height="74" alt="image" src="https://github.com/user-attachments/assets/88b27716-c586-432d-9964-05ea0bde128b" />
The purpose of these XIO instructions is to avoid accidental progression of the state machine and avoid "button mashing".
<img width="925" height="86" alt="image" src="https://github.com/user-attachments/assets/9b6e1842-6e5f-4ba0-b900-8f3f770cccb8" />
In the same manner, any subsequent state is unlikely to trigger through the simultaneous pressing of more than one button.
This is only a surface-level safety consideration.

## Virtual Tags

Although not shown in the state machine, every field device input and output is digitally buffered in separate subroutines.
<img width="927" height="149" alt="image" src="https://github.com/user-attachments/assets/0f3f6d1a-5490-4321-8cef-437fe8634f9b" />
<img width="925" height="101" alt="image" src="https://github.com/user-attachments/assets/eea036da-7aaa-4683-bc1e-9ffe39b8ca74" />
The subroutines for the input and output buffer routines are placed at the start and end of the main routine which is the state machine in this case. Within is a simple XIC to OTE rung for each field device input or output.

The purpose for these virtual tags are tied to the nature of field device I/O in PLC applications. As I/O is updated asynchronously from the PLC logic, the programmer loses some control over the sequence of events that would normally follow in a traditional program without the optional use of interrupts. The use of virtual I/O allows the programmer to remain in control of when real outputs are energized which so happens to always be at the end of the program, the end of a scan cycle.


# Addendum

All in all, this project reinforced skills in PLC programming through advanced state machines.

