This is an automatic translation, may be incorrect in some places. See sources and examples!

# ServoSmooth
Library for smooth control of servos
- Addition to the standard Servo library
- Setting the maximum speed of the servo
- Setting the acceleration (acceleration and deceleration) of the servo
- Non-blocking operation
- Trapezoidal speed profile
- When using ESC and BC of the motor, we get a "soft start" of the motor
- Set servo target position by angle (0-180) and pulse length (500-2400)
- Automatic shutdown (detach) when the target is reached
- Soft start when connecting servo
- Support for PCA9685 expander

### Compatibility
Compatible with all Arduino platforms (using Arduino functions)

### Documentation
The library has [extended documentation](https://alexgyver.ru/ServoSmooth/)

## Content
- [Install](#install)
- [Initialization](#init)
- [Usage](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found by the name **ServoSmooth** and installed through the library manager in:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Download library](https://github.com/GyverLibs/ServoSmooth/archive/refs/heads/main.zip) .zip archive for manual installation:
    - Unzip and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unzip and put in *C:\Program Files\Arduino\libraries* (Windows x32)
    - Unpack and putCranberries in *Documents/Arduino/libraries/*
    - (Arduino IDE) automatic installation from .zip: *Sketch/Include library/Add .ZIP library…* and specify the downloaded archive
- Read more detailed instructions for installing libraries [here] (https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE% D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)

<a id="init"></a>
## Initialization
```cpp
ServoSmooth servo;
ServoDriverSmooth servo; // for PCA9685
```

<a id="usage"></a>
## Usage
```cpp
void write(uint16_t angle); // analogue of the method from the Servo library
void writeMicroseconds(uint16_t angle); // analogue of the method from the Servo library
void attach(uint8_t pin); // analogue of the method from the Servo library
void attach(uint8_t pin, int min, int max); // analogue of the method from the Servo library. min by default 500, max 2400
void detach(); // analogue of the detach method from the Servo library
void start(); // attach + enable tick
void stop(); // detach + disables tick
  
boolean tick(); // the method that controls the servo should be polled as often as possible.
                                            // Returns true when the target position has been reached.
                                            // Has a built-in timer with a period of SERVO_PERIOD
boolean tickManual(); // method that controls the servo, without a built-in timer.
                                            // Returns true when target position is reached
void setSpeed(int speed); // set maximum speed (degrees per second)
void setAccel(float accel); // set acceleration (0.05 - 1.0). With a value of 1, the acceleration is maximum. 0 - disabled
void setAccel(int accel); // set acceleration in degrees/sec/sec (working from 1 to ~1500). 0 - disabled
void setTarget(int target); // set target position in µs (500 - 2400)
void setTargetDeg(int target); // set target position in degrees (0-max angle). Depends on min and max
void setAutoDetach(boolean set); // enable/disable automatic shutdown (detach) when reaching a corner. By default on
void setCurrent(int target); // setting the current position in µs (500 - 2400)
void setCurrentDeg(int target); // set the current position in degrees (0-max angle). Depends on min and max
void setMaxAngle(int maxAngle); // set max. drive angle
int getCurrent(); // get the current position in µs (500 - 2400)
int getCurrentDeg(); // get the current position in degrees (0-max angle). Depends on min and max
int getTarget(); // get target position in µs (500 - 2400)
int getTargetDeg(); // get the target position in degrees (0-max angle). Depends on min and max
voidsmoothStart(); // call right after attach(pin, target). Softens the movement of the servo from an unknown position to the starting position. BLOCKING FOR 1 SEC!
```

<a id="example"></a>
## Example
See **examples** for other examples!
```cpp
/*
   This code smoothly controls one servo (on pin 2)
   using a potentiometer (on pin A0)
   Documentation: https://alexgyver.ru/servosmooth/
*/

#include <ServoSmooth.h>
ServoSmooth servo;

void setup() {
  Serial.begin(9600);
  servo.attach(2, 600, 2400); // 600 and 2400 - pulse lengths at which
  // the servo rotates as much as possible in one direction and the other, depending on the servo itself
  // and usually even specified by the seller. We list them here to
  // the setTargetDeg() method correctly worked out the full range of the servo's rotation
  
  servo.setSpeed(50); // limit speed
  servo.setAccel(0.3); // set acceleration (acceleration and deceleration)
  
  servo.setAutoDetach(false); // disable auto-detach (detach) when the target angle is reached (enabled by default)
}

void loop() {
  // the desired position is set by the setTarget (momentum) or setTargetDeg (angle) method, then
  // when tick() is called, the servo is automatically moved
  // with given acceleration and speed limit
  servo.tick(); // this is where the servo moves according to the built-in timer!

  int newPos = map(analogRead(0), 0, 1023, 0, 180); // take the value 0-180 from the potentiometer
  servo.setTargetDeg(newPos); // and send to servo
}
```

<a id="versions"></a>
## Versions
- v1.1 - automatic shutdown (detach) when the goal is reached
- v1.2 - enable/disable servo auto-off
- v1.3 - a separate method for setting and reading the current position. Added a variant of the attach method
- v1.4 - improved compatibility
- v1.5 - fixed getCurrent and getCurrentDeg
- v1.6 - slightly optimized initialization
- v1.7 - fixed bug with low speed/acceleration, code optimized
- v1.8 - improved stability
- v1.9 - added setting max. servo angle
- v1.10 - fixed bug when current position matches target position
    
- v2.0 - simplified algorithm
- v2.1 - direction change added
- v2.2 - fixed movement in inversion (thanks to VICLER) and write functions (thanks to CheDima)
    
- v3.0
    - Added a completely new, smoother algorithm
    - Cleaned up trash
    - Added support for PCA9685
    - "Smoothness" moved to the base class to make it easier to add support for new servo libraries
    
- v3.1 - optimized and simplified algorithm, speed is set in degrees/sec
- v3.2 - fixed a bug with a sharp turn on the first tick, added smoothStart
- v3.3 - fixed a bug that occurred if tick was not called
- v3.4 - setting acceleration to 0 activates constant speed profile
    
- v3.5
    - Improved/fixed stop operation
    - Fixed bugs with rounding
    - Fixed problem with slow change of target
    
- v3.6 - Fixed minor bugs, cutouten debug s 3.5
- v3.7 - Added acceleration task in degrees/sec/sec
- v3.8 - Fixed tick not returning on autoDetach(false)

<a id="feedback"></a>
## Bugs and feedback
When you find bugs, create an **Issue**, or better, immediately write to the mail [alex@alexgyver.ru](mailto:alex@alexgyver.ru)
The library is open for revision and your **Pull Request**'s!