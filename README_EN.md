This is an automatic translation, may be incorrect in some places. See sources and examples!

# SERVOSMOOTH
Library for smooth control of servo drives
- Addition to the Standard Library Servo
- Setting the maximum velocity of servo drive
- setting acceleration (acceleration and braking) of servo drive
- Nebenching work
- trapezoidal speed profile
- when using ESC and BC motor, we get a "smooth start" motor
-Installation of the target position of the Servo in the corner (0-180) and pulse length (500-2400)
- automatic shutdown (Detach) when reaching the goal
- smooth start when connecting servo
- Support for the exterator PCA9685

## compatibility
Compatible with all arduino platforms (used arduino functions)

### Documentation
There is [extended documentation] to the library (https://alexgyver.ru/servosmooth/)

## Content
- [installation] (# Install)
- [initialization] (#init)
- [use] (#usage)
- [Example] (# Example)
- [versions] (#varsions)
- [bugs and feedback] (#fedback)

<a id="install"> </a>
## Installation
- The library can be found by the title ** Servosmooth ** and installed through the library manager in:
    - Arduino ide
    - Arduino ide v2
    - Platformio
- [download the library] (https://github.com/gyverlibs/servosmooth/archive/refs/heads/main.zip). Zip archive for manual installation:
    - unpack and put in * C: \ Program Files (X86) \ Arduino \ Libraries * (Windows X64)
    - unpack and put in * C: \ Program Files \ Arduino \ Libraries * (Windows X32)
    - unpack and put in *documents/arduino/libraries/ *
    - (Arduino id) Automatic installation from. Zip: * sketch/connect the library/add .Zip library ... * and specify downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%BD%D0%BE%BE%BE%BED0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Update
- I recommend always updating the library: errors and bugs are corrected in the new versions, as well as optimization and new features are added
- through the IDE library manager: find the library how to install and click "update"
- Manually: ** remove the folder with the old version **, and then put a new one in its place.“Replacement” cannot be done: sometimes in new versions, files that remain when replacing are deleted and can lead to errors!


<a id="init"> </a>
## initialization
`` `CPP
Servosmooth Servo;
Servodriversmooth Servo;// for PCA9685
`` `

<a id="usage"> </a>
## Usage
`` `CPP
VOID Write (Uint16_T Angle);// analogue of the method from the Servo library
VOID Writemicroseconds (Uint16_T Angle);// analogue of the method from the Servo library
Void attach (uint8_t pin);// analogue of the method from the Servo library
VOID Attach (Uint8_T PIN, Int min, int max);// Analogue of the method from the library Servo.min.500, max 2400
VOID Detach ();// analogue of the Detach method from the Servo library
VOID Start ();// attach + allows the work to work
VOID Stop ();// DETACH + prohibits the operation of tick
  
Boolean Tick ();// A method that controls the cervical should be interpreted as often as possible.
                                            // Returns True when targetsposition has been achieved.
                                            // has a built -in timer with the period Servo_period
Boolean Tickmanual ();// Method that controls the cervical, without a built -in timer.
                                            // Returns True when the target position is reached
VOID SetSpeed (Int Speed);// Installation of maximum speed (degrees per second)
VOID setaccelle (Float Accel);// Acceleration installation (0.05 - 1.0).With a value of 1 acceleration maximum.0 - disabled
VOID setaccel (Intscel);// Installation of acceleration in degrees/s/s (worker from 1 to ~ 1500).0 - disabled
VOID settarget (int Target);// Installation of the target position in the ISS (500 - 2400)
VOID settargetdeg (Int Target);// Installation of the target position in degrees (0-Max. angle).Depends on Min and Max
VOID Setautodetach (Boolean Set);// VKL/Off of automatic shutdown (DETACH) when the angle is reached.By the silence.ON
VOID setcurrent (Int Target);// Installation of the current position in the ISS (500 - 2400)
VOID setcurrentdeg (Int Target);// Installation of the current position in degrees (0-Max. angle).Depends on Min and Max
VOID setmaxangle (int maxangle);// Installation Max.The angle of the drive
Intcurrent ();// Obtaining the current position in the ISS (500 - 2400)
Intcurrentdeg ();// Obtaining the current position in degrees (0-Max. angle).Depends on Min and Max
Inttarget ();// Obtaining the target position in the ISS (500 - 2400)
int Gettargetdeg ();// Obtaining a target position in degrees (0-Max. Corner).Depends on Min and Max
VOID Smoothstart ();// Call immediately after Attach (pin, target).Surveys the movement of the Servo from an unknown position to the starting.Blocking for 1 second!
`` `

<a id="EXAMPLE"> </a>
## Example
The rest of the examples look at ** Examples **!
`` `CPP
/*
   This code smoothly controls one servant (on pin 2)
   Using a potentiometer (on pin A0)
   Documentation: https://alexgyver.ru/servosmooth/
*/

#include <service of
Servosmooth Servo;

VOID setup () {
  Serial.Begin (9600);
  Servo.attach (2, 600, 2400);// 600 and 2400 - the lengths of pulses in which
  // servo turns in as much as possible in one and the other, depend on the servo itself
  // and usually even indicated by the seller.We indicate them here in order to
  // method settargetdeg () correctly worked out the full range of power turning
  
  Servo.SetSpeed (50);// limit speed
  Servo.Setaccel (0.3);// Install acceleration (acceleration and braking)
  
  Servo.Setautodetach (false);// Disable auto shutdown when reaching the target angle (by default on)
}

VOID loop () {
  // The desired position is set by the settarget (pulse) or settargetdeg (angle), then
  // When calling tick (), automatic cervical movement is carried out
  // with a given acceleration and speed restriction
  servo.tick ();// Here is the movement of the Servo along the built -in timer!

  int NewPos = MAP (ANALOGREAD (0), 0, 1023, 0, 180);// take a potential value 0-180
  Servo.Settargetdeg (Newpos);// and send to the servo
}
`` `

<a id="versions"> </a>
## versions
- V1.1 - automatic shutdown (DETACH) when reaching the goal
- V1.2 - ON/Off Auto Circulation SERVO
- V1.3 - A separate method for installing and reading the current position.Added version of the Attach method
- V1.4 - Additional compatibility
- V1.5 - Fixed Getcurrent and Getcurrentdeg
- V1.6 - initialization is slightly optimized
- V1.7 - Fixed a bug with low speed/acceleration, the code is optimized
- V1.8 - Improved stability
- V1.9 - Added Max Settings.corner of the servo
- V1.10 - Bag is fixed when the current position coincides with the position of Target
    
- v2.0 - algorithm is simplified
- v2.1 - added a change
- V2.2 - Fix of movement in inversion (thanks Vicler) and Write functions (thanks to cheedima)
    
- V3.0
    - Added the floorCranberries new, more smooth algorithm
    - The garbage is cleaned
    - Added support PCA9685
    - "smoothness" is taken to the basic class to simplify the addition of support for new libraries servo
    
- V3.1 - the algorithm is optimized and lightweight, the speed is set in degrees/s
- V3.2 - A bug with a sharp turn at the first Tick was fixed, added SMOOTHSTART
- v3.3 - a bug that arises if not caused tick is fixed
- v3.4 - When setting acceleration in 0, a profile of constant speed is activated
    
- V3.5
    - Improved/fixed work stop
    - corrected errors with rounding
    - Fixed a problem with a slow change in Target
    
- v3.6 - small bugs are fixed, debag was cut out with 3.5
- v3.7 - Added the task of acceleration in degrees/s/s/s
- V3.8 - Fixed TIKA non -return at Autodetach (FALSE)
- V3.9 - ESP32 support

<a id="feedback"> </a>
## bugs and feedback
Create ** Issue ** when you find the bugs, and better immediately write to the mail [alex@alexgyver.ru] (mailto: alex@alexgyver.ru)
The library is open for refinement and your ** pull Request ** 'ow!


When reporting about bugs or incorrect work of the library, it is necessary to indicate:
- The version of the library
- What is MK used
- SDK version (for ESP)
- version of Arduino ide
- whether the built -in examples work correctly, in which the functions and designs are used, leading to a bug in your code
- what code has been loaded, what work was expected from it and how it works in reality
- Ideally, attach the minimum code in which the bug is observed.Not a canvas of a thousand lines, but a minimum code