*MEAM 5100: Mechatronics (grad level)*

Would you still look at my portfolio if I was a worm?

This class was a deep dive into things I previously took for granted (how does a motor keep a consistent speed, how do devices connect over wifi, how does a microcontroller work at the base level, etc). Basically my first look into embedded systems. It was really cool to dive under the hood and learn about the low-level hardware/circuitry and bit-level logic that tends to get abstracted away with platforms I've worked with in the past. 

Snippet of code for this particular project is at the end. 

![[5100WaldoFunction.mp4]]

Some other interesting projects from this class that I will probably add later include an IR remote and receiver, and my final project of a two-wheeled PID controlled robot controlled via wifi and based purely off this lower-level embedded code.

Code for Waldo Worm:
```c
/* 
Project: MEAM 5100 Lab 3
Author: Ashna Khemani
Copyright (C) 2025 Ashna Khemani - All Rights Reserved
License: You may use, distribute and modify this code under the terms of the GNU GPLv3.0 license.
*/


#include "MEAM_general.h"  // includes the resources included in the MEAM_general.h file
#include "m_usb.h"


void initADC(int chan){
    /*
    chan: ADCchan port you wish to initialize
    */

    // set reference voltage to Vcc
    clear(ADMUX, REFS1); set(ADMUX, REFS0);

    // set ADC clock prescaling: 128
    set(ADCSRA, ADPS2); set(ADCSRA, ADPS1); set(ADCSRA, ADPS0);

    

    // disable digital input on desired port, and select as analog inp channel
    switch (chan){
        // ADC0, F0
        case 0: set(DIDR0,ADC0D); clear(ADCSRB,MUX5); clear(ADMUX,MUX2); clear(ADMUX,MUX1); clear(ADMUX,MUX0); 
            break;
        case 1: set(DIDR0,ADC1D); clear(ADCSRB,MUX5); clear(ADMUX,MUX2); clear(ADMUX,MUX1); set(ADMUX,MUX0); 
            break;
        case 4: set(DIDR0,ADC4D); clear(ADCSRB,MUX5); set(ADMUX,MUX2); clear(ADMUX,MUX1); clear(ADMUX,MUX0); 
            break;
        case 5: set(DIDR0,ADC5D); clear(ADCSRB,MUX5); set(ADMUX,MUX2); clear(ADMUX,MUX1); set(ADMUX,MUX0); 
            break;
        case 6: set(DIDR0,ADC6D); clear(ADCSRB,MUX5); set(ADMUX,MUX2); set(ADMUX,MUX1); clear(ADMUX,MUX0); 
            break;
        case 7: set(DIDR0,ADC7D); clear(ADCSRB,MUX5); set(ADMUX,MUX2); set(ADMUX,MUX1); set(ADMUX,MUX0); 
            break;
        case 8: set(DIDR2,ADC8D); set(ADCSRB,MUX5); clear(ADMUX,MUX2); clear(ADMUX,MUX1); clear(ADMUX,MUX0); 
            break;
        case 9: set(DIDR2,ADC9D); set(ADCSRB,MUX5); clear(ADMUX,MUX2); clear(ADMUX,MUX1); set(ADMUX,MUX0); 
            break;
        case 10: set(DIDR2,ADC10D); set(ADCSRB,MUX5); clear(ADMUX,MUX2); set(ADMUX,MUX1); clear(ADMUX,MUX0); 
            break;
        case 11: set(DIDR2,ADC11D); set(ADCSRB,MUX5); clear(ADMUX,MUX2); set(ADMUX,MUX1); set(ADMUX,MUX0); 
            break;
        case 12: set(DIDR2,ADC12D); set(ADCSRB,MUX5); set(ADMUX,MUX2); clear(ADMUX,MUX1); clear(ADMUX,MUX0); 
            break;
        case 13: set(DIDR2,ADC13D); set(ADCSRB,MUX5); set(ADMUX,MUX2); clear(ADMUX,MUX1); set(ADMUX,MUX0); 
            break;
    }
    set(ADCSRA, ADEN);
}

int readADC (int chan){
    /*
    chan: ADCchan to read from
    */
    initADC(chan);

    // ---- Dummy conversion ----
    set(ADCSRA, ADSC);
    while (!bit_is_set(ADCSRA, ADIF));
    set(ADCSRA, ADIF);
    (void)ADC;  // read and discard result

    set(ADCSRA, ADSC);  // enable adc; start conversion
    while(!bit_is_set(ADCSRA, ADIF));    // wait while conversion not done
    set(ADCSRA, ADIF);  // reset the flag
    int val = ADC;  // store the result
    return val;     // return value

}

int lin_interp (int x1, int x2, int y1, int y2, int x){
    return y1 + ((y2-y1) / (x2-x1)) * (x-x1);
}

int main(void)
{
    _clockdivide(0); //set the clock speed to 16Mhz
    m_usb_init();           // init printing

    // set motor output pins: DDR
    set(DDRB, 6); 
    set(DDRB, 7);
    set(DDRB, 5);



    // ------ Set Timer1 mode
    // Using timer1, set to mode14 for fastPWM which uses the ICR (the TOP value for counter)
    set(TCCR1B, WGM13); set(TCCR1B, WGM12); set(TCCR1A, WGM11); clear(TCCR1A, WGM10);

    // ------ Prescale Timer1 to 1/8 (want high-res)
    clear(TCCR1B, CS12); set(TCCR1B, CS11); clear(TCCR1B, CS10);

    // ------ Set ICR1 -- max or TOP value Timer1 can reach for mode14 pwm
    // f_des = f_timer / ICR --> 50 = 2MHz / ICR --> ICR = 40000
    ICR1 = 40000;

    // ------ Set COM (compare output mode)
    // what to do when timer matches compare reg/OCR: turn off ports
    // pins: PB6/OC1B/10 and PB7/OC1C/11
    set(TCCR1A, COM1B1); clear(TCCR1A, COM1B0);
    set(TCCR1A, COM1C1); clear(TCCR1A, COM1C0);
    set(TCCR1A, COM1A1); clear(TCCR1A, COM1A0);



    while (1){
        // Read and print out the pot values
        int leftPot = readADC(7);   // A0
        int rightPot = readADC(0);  // A5
        int headPot = readADC(1);   // A4
        
        // // Set the OCR values based on pot readings
        int pin11val = (int)lin_interp(675, 15, 1000, 5000, leftPot);   // "reversed"
        int pin10val = (int)lin_interp(120, 800, 1000, 5000, rightPot);
        int pin9val = (int)lin_interp(170, 900, 1000, 5000, headPot);
        // OCR1C = pin11val;    // 2000=5% of 40000 cycle=full CW. 4000=10% of 40000 cycle=full CCW
        // OCR1B = pin10val;
        // OCR1A = pin9val;

        m_usb_tx_uint(leftPot); m_usb_tx_string("\n"); //m_usb_tx_int(pin10val); m_usb_tx_string("\t");
        // m_usb_tx_int(rightPot); m_usb_tx_string("  "); m_usb_tx_int(pin11val); m_usb_tx_string("\n");



    }
    return 0;   /* never reached */
}




```