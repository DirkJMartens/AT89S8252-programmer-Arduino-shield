# AT89S8252-programmer-Arduino-shield
An ISP programmer based on an Arduino Uno shield with a Python script programmer

AVRDUDE can be made to run using a modified conf file to accommodate some of the 8051-derived uCs, but the USBasp is a bigger challenge. 
Mainly because virtually all uCs use an active LOW RESET signals where the 8051-family uses active HIGH. 

A Python script was developed by M. Tirado which can be found here https://github.com/mtirado1/at89s8252-arduino. 
Using a few discrete components, I made an Arduino Uno shield with a 40-pin DIL-socket to receive the target AT89S8252 to be programmed. 

For the shield, I used a protoshield and used wire-wrapping to make the connections (see pics): 
- Connect MISO/MOSI/SCK/RESET and GND/VCC between Uno and target DIL socket 
- Connect a crystal between pins 18 and 19 (maximum 24 MHz) 
- In my design, the crystal oscillator did not work very well and replaced it with a TCXO on pin 19 and leaving pin 18 not connected 
- Pin 31 (EA) connected to Vcc to ensure code is run from internal program memory 
- Pin 9 (RESET) with a pull-down to GND and a 10uF cap to Vcc 
- I installed some headerpins and resistors so I can mount some LEDs for blinkenlights and for some testing the "blink" program. 

Arduino UNO-based programmer for the AT89S8252
----------------------------------------------
- Upload the "programmer.ino" sketch into the Arduino Uno. 
- Connect LEDs with anode via a 220 ohm series-resistor to Vcc 
- Connected LEDs with cathode to pins 1/2/3 of the AT89 and they should now blink 
- Mount the target AT89S8252 into the socket on the Arduino programmer shield and the shield onto the Uno. 
- Edit the config.py file: 
	- Ensure the COM-port matches the port for the UNO
	- Edit the path to point the "blink.hex" file provided in the "blink" folder 
	- Save and close 
- Connect the programmer and in a Command Prompt, run the command "py writeprogram.py".
- This Python script will output the following information: 
	- List the full path of the .HEX file that will be uploaded (as per config.py) 
	- List the COM port it will use (as per config.py)
	- Your programmer responds and you see "Programmer ready." 
	- It will then list the content of the .hex file 
	- Upload the .hex file into the target 
	- It will then ask the question to verify the upload 
	- Press "y" followed by Enter to run the verification cycle. 
	- You can edit script line 46 and replace it with "a = 'y'" to always verify without asking for confirmation 
	- This is especially useful when calling the script from within the Keil uVision IDE 
- This should have uploaded the "blink.hex" file into the AT89S8252 
- If you have some blinking LED, you have confirmed all hardware and software is properly configured and connected. 

For your own source code: 
-------------------------
- Enter the source code in the uVision IDE from Keil (can be downloaded from their website for free) 
- A few options must be changed from the default settings: 
	- "Flash - Download" will be disabled by default. To enable, edit the following: 
	- Under "Project - Options for Target - Output", ensure "Create HEX file" is ticked 
	- Under "Project - Options for Target - Utilities", tick "Use External Tool for Flash Programming" 
	- Under "Command", enter "py writeprogram.py" and specify the full path 
	- Tick the "Run Independent" 
- Edit the config.py file: 
	- Ensure the COM-port matches the port for the UNO
	- Edit the path to the hex-file generated by the uVision IDE. 
	- Save and close 
- Press F7 in the IDE to ensure the source code compiles without errors into .HEX file 
- Press F8 to compile the code and upload it using the Arduino 

A few additional pointers: 
--------------------------
- AVRDUDE does not have the config file for the AT89S8252 but there are some modified versions available on the internet 
- The most basic circuit the AT89S8252 needs to operate is: 
	- Supply power through GND on pin 20 and Vcc on pin 40 
	- A crystal between pins 18 and 19 _OR_ a TCXO on pin 19 with pin 18 not connected (max 24 MHz) 
	- Pin 31 (EA) connected to Vcc to run code from internal program memory 
	- Pin 9 (RESET) with a pull-down to GND and a 10uF cap to Vcc 
- In uVision, each C-code source file must include the "reg8252.h" file for the chip-specific defines and declares 
- These include pin definitions, port definitions, memory map of the registers, etc 
- The oscillator frequency is 12 times higher than the machine cycle frequency (e.g. 2 MHz for 24 MHz) 
- AT89S8252 instructions take 1 to 4 machine cycles to complete, making it a max. 1 MIPS chip 
- For comparison, an ATMega running at 20 MHz oscillator frequency completes 20 MIPS 
