```c
#include    "xc.h"              // Microchip XC8 compiler include file
#include    "stdint.h"          // Include integer definitions
#include    "stdbool.h"         // Include Boolean (true/false) definitions

#include    "UBMP4.h"           // Include UBMP4 constants and functions

// TODO Set linker ROM ranges to 'default,-0-7FF' under "Memory model" pull-down.
// TODO Set linker code offset to '800' under "Additional options" pull-down.

// Program variable definitions
 int delayCycles = 255; // Delay between each ocsilation of the beeper (controls pitch)
 char resetDelay = 255; // The starting pitch of each blast
 int maxDelay = 700; // How low the pitch is at the end of each blast
 char fireRate = 20; // How fast the blasts repeat- firerate
 char resetFireRate = 20;
 bool SWPressed = false; // Tool to check if certain switches are pressed

 // Function(s)
 void gunShot()
 {
    for(char singleShot = 0; singleShot < 45; singleShot++) // Fires 1 full blast if button is tapped
    {
        delayCycles += 10; // Increases delay between oscillation (changes pitch)- gives the blasts a dynamic sound
        if(delayCycles >= maxDelay) // Delay resets; starts the next blast 
        {
            delayCycles = resetDelay;
        }
        for(char soundlength = 0; soundlength <= fireRate; soundlength++) // Loops the blast for a duration
        {
            BEEPER = !BEEPER;
            for (int pew = 0; pew <= delayCycles; pew++) // Loops nothing; creates delay between oscillations
            {}
        }
    }       
                    
                
 }

int main(void)
{
    OSC_config();               // Configure internal oscillator for 48 MHz
    UBMP4_config();             // Configure on-board UBMP4 I/O devices
	
    while(1)
    {
        if(SW2 == 0)
        {
            LATC = 0b11110000;
            gunShot();
            LATC = 0b00000000;
            __delay_ms(25);
        }
        else
        {
            delayCycles = resetDelay; // resets delay; every new click starts from the starting pitch
        }
        if(SW3 == 0 && SWPressed == false) // Increases "fireRate" by 1- lowers firing speed
        {
            fireRate++;
            SWPressed = true;
        }
        else if(SW4 == 0 && SWPressed == false) // Decreases "fireRate" by 1- increases firing speed
        {
            fireRate--;
            SWPressed = true;
        }
        if (SW3 == 1 && SW4 == 1 && SWPressed) // Allows SW3/SW4 to work again after releasing their buttons
        {
            SWPressed = false;
        }
        if(fireRate < 1 || fireRate > 255) // Stops fireRate from exceeding 8-bit limit; resets fireRate to default
        {
            fireRate = resetFireRate;
        }
        if(SW5 == 0) // manual fireRate reset to default
        {
            fireRate = 20;
        }
        // Activate bootloader if SW1 is pressed..
        if(SW1 == 0)
        {
            RESET();
        }
    }
}

 ```
