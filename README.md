# DCMotor
This project included Code Composer Studio, a MSP430 Microcontroller, a QTR-3A Reflectance Sensor Array from Pololu, a 9V Power Source, a DC Motor, and 4 Buttons. The goal of this project was to create a relation between the values that were taken from the sensor and apply them to the speed and direction of the DC motor. 

The figure below shows the connections that were made. 

![image](https://github.com/k-parikh/DCMotor/assets/105128826/9b41ea1f-1df4-4793-846a-663af5541a9b)

  * P6.0, P6.1, P7.0, P7.1: Direction Buttons
  * P1.2: Bridge A Input 1
  * P1.3: Bridge A Input 2
  * P4.7: nSleep (GPIO)

Using the sensor, I recorded the NADC values for when the sensor was not blocked, when the sensoor was on a white surface, and when the sensor was on a black surface. These numbers will be important in the a few steps. 

The next step was controlling the direction and decay speed of the motor when different buttons were pressed.

  * When a "Left" button is pressed once, it will make the motor spin in a reverse direction.
  
  ```
  if((P6IN & BIT1) == 0){ //Left (Reverse)
	 TA1CCR1 = 1000; //TA1CCR1 value
	 TA1CCTL1 = OUTMOD_7; //Output Mode 7
	 TA1CCR2 = 2000; //TA1CCR2 value
	 TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 state = 1;
	 }
  ```
  
  * When a "Right" button is pressed once, it will make the motor spin in a forward direction.

  ```
  if((P6IN & BIT0) == 0){ //Right (Forward)
	 TA1CCR1 = 2000; //TA1CCR1 value
	 TA1CCTL1 = OUTMOD_7; //Output Mode 7
	 TA1CCR2 = 1000; //TA1CCR2 value
	 TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 state = 2;
	}
  ```
  * When an "Up" button is pressed once, it will make the motor stop spinning. And, the stop mode should be the implementation of Slow Decay.
  
  ```
  if((P7IN & BIT1) == 0){ //Up (Not Spinning and Slow Decay)
	 TA1CCR1 = 1000; //TA1CCR1 value
	 TA1CCTL1 = OUTMOD_7; //Output Mode 7
	 TA1CCR2 = 1000; //TA1CCR2 value
	 TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 state = 3;
	}
  ```
  
  * When a "Down" button is pressed once, it will make the motor stop spinning. And, the stop mode should be the implementation of Fast Decay.
  
  ```
  if((P7IN & BIT0) == 0){ //Down (Not Spinning and Fast Decay)
	 TA1CCR1 = 0; //TA1CCR1 value
	 TA1CCTL1 = OUTMOD_7; //Output Mode 7
	 TA1CCR2 = 0; //TA1CCR2 value
	 TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 state = 4;
	}
  ```

The next step was to use the values that we got from the reflectance sensor to adjust the speed of the motor. This is where we will be using the state variable to change the speed of the motor without changing the direction that the motor is spinning. 

  * If the reflectance sensor is not blocked or it is close to the black portion of the given test paper within 0.5 inches or less, the speed of the spin should be "fast".

    ```
    if(x > 3700){
	   if(state == 1){
	    TA1CCR1 = 500; //TA1CCR1 value
      TA1CCTL1 = OUTMOD_7; //Output Mode 7
      TA1CCR2 = 2000; //TA1CCR2 value
      TA1CCTL2 = OUTMOD_7; //Output Mode 7
	   }
	   if(state == 2){
	     TA1CCR1 = 2000; //TA1CCR1 value
	     TA1CCTL1 = OUTMOD_7; //Output Mode 7
	     TA1CCR2 = 500; //TA1CCR2 value
	     TA1CCTL2 = OUTMOD_7; //Output Mode 7
	   }
	   if(state == 3){
	     TA1CCR1 = 1000; //TA1CCR1 value
	     TA1CCTL1 = OUTMOD_7; //Output Mode 7
	     TA1CCR2 = 1000; //TA1CCR2 value
	     TA1CCTL2 = OUTMOD_7; //Output Mode 7
	   }
	   if(state == 4){
	     TA1CCR1 = 0; //TA1CCR1 value
	     TA1CCTL1 = OUTMOD_7; //Output Mode 7
	     TA1CCR2 = 0; //TA1CCR2 value
	     TA1CCTL2 = OUTMOD_7; //Output Mode 7
	   }
  }
    ```
    
  * If the reflectance sensor is close to the white of the given test paper within 0.5 inches or less, the speed of the spin should be "slow".

  ```
  if(x < 3700){
	 if(state == 1){
	   TA1CCR1 = 1500; //TA1CCR1 value
	   TA1CCTL1 = OUTMOD_7; //Output Mode 7
	   TA1CCR2 = 2000; //TA1CCR2 value
	   TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 }
	 if(state == 2){
	   TA1CCR1 = 2000; //TA1CCR1 value
	   TA1CCTL1 = OUTMOD_7; //Output Mode 7
	   TA1CCR2 = 1500; //TA1CCR2 value
	   TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 }
	 if(state == 3){
	   TA1CCR1 = 1000; //TA1CCR1 value
	   TA1CCTL1 = OUTMOD_7; //Output Mode 7
	   TA1CCR2 = 1000; //TA1CCR2 value
	   TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 }
	 if(state == 4){
	   TA1CCR1 = 0; //TA1CCR1 value
	   TA1CCTL1 = OUTMOD_7; //Output Mode 7
	   TA1CCR2 = 0; //TA1CCR2 value
	   TA1CCTL2 = OUTMOD_7; //Output Mode 7
	 }
	}

  ```
  
