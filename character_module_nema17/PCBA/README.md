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


## Parts Selected:
	MCU 	- ($1.63/1pc) STM32F030F4P6TR 

	DIPSW 	- ($0.98/1pc) 8pos, SMD, TE CONN. 1-1825058-9

	DRIVE 	- ($3.11/1pc) A4988

	PMIC 	- ($0.83/1pc) DC-DC Buck AOZ1282CI (12 -> 3.3V) (investigate cheaper chinese parts at some point)

			- (solution cost $3?)

	HALL 	- ($0.40/1pc) Toshiba TCS40DPR

	LEDs	- ($0.26/1pc) LTST-C190GKT (0603 Green)

			- ($0.26/1pc) LTST-C190KRKT (0603 Red)



## MCU Functionality:
	In no particular order:

		- Listen to I2C, filter incoming messages and display character corresponding to address

		- Calibration functionality - tacsw advances cards until hallsw, zeroes counter, slowly rotates until operator releases button 
		at the index card. index card position stored in NVM.
		
		- DIPSW translates to address
		
		- Control registers accessible by I2C in direct-addressed mode. LEDs, calibration, motor settings, character maps
		
		- Standby current control (or shut off driver IC entirely if that works mechanically and doesn't cause glitching to adjacent 
		characters)
