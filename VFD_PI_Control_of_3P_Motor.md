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

When in the Hand position, the VFD will apply voltage to the motor at a fixed frequency indefinitely unless the E-Stop button is pressed, or the HOA is returned to the Off or inactive position.

The system can freely toggle between manual and automatic control of the water level. The system can enter manual mode freely as the HOA is set to the Hand position, while entering automatic control mode requires the HOA to be set to the Auto position and for the green button to be pressed.

# State Machine Program Description

The ladder logic document can be found here: [PIpumpCtrlFSM_NoahSG.pdf](https://github.com/user-attachments/files/23294503/PIpumpCtrlFSM_NoahSG.pdf)


While the application is rather straight-forward, the design process was far more interesting.

The Zelio SR3 programmable relay paired with ZelioSoft2 approximates the programming environment for a PLC in ladder logic.
However, this comes with its quirks and limitations.

![schneider-programmable-relay](https://github.com/user-attachments/assets/291d2a13-5f05-43aa-abde-59457d1fdf03)

In a finite state machine that would be programmed in a platform like Rockwell's Allen Bradley's PLCs, you will normally have access to some CPU status registers such as S:FS, the first scan bit. This would be used as the only entry point for the first state in the sequence.

Below is a simplified state diagram of the entire control system.

![programmable-relay-fsm-diagram](https://github.com/user-attachments/assets/21cb06c4-bfab-4617-b6e9-dc95a5eb644e)

As you can see, S:FS is used as the entry point for the very first state in this particular FSM. However, the programmable relay, along with ZelioSoft2, are incapable of providing a flag indicating that the first scan is being performed. The programmable relay is analog in its entirety.

However, the first scan bit can be simulated, and after a few attempts at replicating it with one shot instructions and strategically placed OTEs, the final solution was much more simple and did not require any extra steps.

Below is the very first rung of the program and the very last.

<img width="770" height="207" alt="image" src="https://github.com/user-attachments/assets/c88ed956-23d2-40f9-8687-c1026d7ea867" />
<img width="993" height="73" alt="image" src="https://github.com/user-attachments/assets/f74d7f44-a737-456b-84dd-60aec9940044" />

The effective S:FS is a simple OTE at the end of the program.

Another limitation is the hard-limit on XIO/XIC instruction you may insert in a single rung branch. The limit is set to five examine instructions followed by the OTE instruction. This, for the most part, was not an issue. However, this somewhat negatively impacts the transition to a fault state.

As a fault state is entered, this entry should act as an interlock with the energization of State 3: Pump Control. As a result, during a fault, the state is entered but is not latched due to the compromise of placing the XIC for the fault state on the latching branch.

In short, the State99_FAULT XIO should be in line 16 rather than 17. Due to this difference, the fault state will not prevent State 3 from being briefly entered during a fault, but it will be prevented from latching altogether. This means the OTE could be potentially energized at a rate determined by the scan speed and program length, but whether this causes any significant safety issues or not was not discovered.

<img width="748" height="143" alt="image" src="https://github.com/user-attachments/assets/84314e20-00fe-4e61-80ca-282bf9ad1e91" />












  
