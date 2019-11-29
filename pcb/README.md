# PCBA Design Notes:

## Requirements:
	- Small microcontroller (AVR ATMega, STM32F0)
	- Single IDC connector for common bus data and power
		- I2C(sda,scl)[2], V_motor[3], V_logic[2], GND[5]
	- Hall effect sensor for indexing
	- DIP switches to set address
	- Stepper motor driver IC
	- (optional pop) - RST and CALIB switches
	- sundry LEDs
	- PMIC (Vmot -> V_logic (3.3V))
	- PTC or other input protection (we're common bus yo! gotta be careful)
	- ESD and other protection

## Parts Selected:
	MCU 	- ($1.93/1pc) STM32F030C8T6TR 
	DIPSW 	- ($0.98/1pc) 8pos, SMD, TE CONN. 1-1825058-9
	DRIVE 	- ($3.11/1pc) A4988
	PMIC 	- ($0.83/1pc) DC-DC Buck AOZ1282CI (12 -> 3.3V) (investigate cheaper chinese parts at some point)
			- (solution cost $3?)
	HALL 	- ($0.40/1pc) Toshiba TCS40DPR
	LEDs	- ($0.26/1pc) LTST-C190GKT (0603 Green)
			- ($0.26/1pc) LTST-C190KRKT (0603 Red)
	CONN 	- ($1.56/1pc) Molex 0901303112 . 12POS Rectangular header, shrouded+keyed, R/A
				- Alt: 	5-102617-4 TE Connectivity AMPMODU II line, $2.53/1pc
	TACSW 	- ($0.12/1pc) Panasonic EVQ-Q2B03W

	So far we're looking at ~$15 in components? Maybe $5 for the PCB and $5 for assy/board? (Maybe wishful thinking)

## MCU Functionality:
	In no particular order:
		- Listen to I2C, filter incoming messages and display character corresponding to address
		- Calibration functionality - tacsw advances cards until hallsw, zeroes counter, slowly rotates until operator releases button 
		at the index card. index card position stored in NVM.
		- DIPSW translates to address
		- Control registers accessible by I2C in direct-addressed mode. LEDs, calibration, motor settings, character maps
		- Standby current control (or shut off driver IC entirely if that works mechanically and doesn't cause glitching to adjacent 
		characters)
			- This can be accomplished using a couple I/O and voltage divider ladder to get the voltages we want. Will have to tune the resistor values but easy to use 1 I/O to set RUN vs STDBY current.

## MCU I/O Pin Counts:
	- [2] I2C
	- [8] DIP switch configuration
	- [8] A4988 Motor controller (nSleep, step, ms1:3, dir, nEnable, nReset)
	- [2] A4988 V_ref control
	- [2] LEDs
	- [1] User switch
	- [1] Hall sensor

	Looks like ~24 I/O min? Cheapest that'll fit? STM32F030K6T6, with 26 I/O. ($1.62/1pc, CT)

	Next up in the price lineup is STM32F030C8T6TR, with 39 I/O, at a whopping $1.93/1pc, cut tape.
