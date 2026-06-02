This is an automatic translation and may be incorrect in some places. See the source README and examples for authoritative information.

[![latest](https://img.shields.io/github/v/release/GyverLibs/ServoSmooth.svg?color=brightgreen)](https://github.com/GyverLibs/ServoSmooth/releases/latest/download/ServoSmooth.zip)
[![PIO](https://badges.registry.platformio.org/packages/gyverlibs/library/ServoSmooth.svg)](https://registry.platformio.org/libraries/gyverlibs/ServoSmooth)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD%24%E2%82%AC%20%D0%9F%D0%BE%D0%B4%D0%B4%D0%B5%D1%80%D0%B6%D0%B0%D1%82%D1%8C-%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B0-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/ServoSmooth?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# ServoSmooth
Library for smooth servo control
- Supplement to the standard Servo library
- Setting the maximum speed of the servo drive
- Adjustment of acceleration (acceleration and braking) of the servo
- Non-blocking work
- Trapeceidal speed profile
- When using ESC and BK motor we get a "smooth start" of the engine
- Setting the target position of the servo at the angle (0-180) and the pulse length (500-2400)
- Automatic shutdown (detach) when the goal is reached
- Smooth start when connecting the servo
- Support for PCA9685 extender

### Compatibility
Compatible with all Arduino platforms (Arduino features are used)

### Documentation.
There's a library[extended documentation](https://alexgyver.ru/ServoSmooth/)

## Contents
- [Installation](#install)
- [Initialization](#init)
- [Use of use](#usage)
- [Example](#example)
- [Versions](#versions)
- [Bugs and feedback](#feedback)

<a id="install"></a>
## Installation
- The library can be found under the name **ServoSmooth** and installed through the library manager in:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
      > Once the library is connected, **platformio.ini** must add **arduino-libraries/Servo** and **Wire**. Thus, the list should consist of at least 3 libraries:
        ```
        lib_deps = 
            gyverlibs/ServoSmooth
            arduino-libraries/Servo
            Wire
        ```
- [Download the library](https://github.com/GyverLibs/ServoSmooth/archive/refs/heads/main.zip).zip archive for manual installation:
    - Unpack and put in *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Unpack and put in *C:\Program Files\Arduino\libraries* (Windows x32)
    - Unpack and put in *Documents/Arduino/libraries/ *
    - (Arduino IDE) Automatic installation from .zip: *Sketch/Connect library/Add .ZIP library...* and specify downloaded archive
- Read more detailed instructions for installing libraries[here](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Update
- I recommend always updating the library: new versions fix errors and bugs, as well as optimize and add new features.
- Through the library manager IDE: find the library as when installing and click "Update"
- Manually: **Delete the folder with the old version** and then put the new one in its place. “Replacement” can not be done: sometimes new versions delete files that will remain when replaced and can lead to errors!

<a id="init"></a>
## Initialization
```cpp
ServoSmooth servo;
ServoDriverSmooth servo;    // for PCA9685
```

<a id="usage"></a>
## Use of use
```cpp
void write(uint16_t angle);                 // Servo analogue
void writeMicroseconds(uint16_t angle);     // Servo analogue
void attach(uint8_t pin);                   // Servo analogue
void attach(uint8_t pin, int min, int max); // similar method from the Servo library. Shut up. 500, max 2400
void detach();                              // Detach from the Servo Library
void start();                               // attach + allows the tick to work
void stop();                                // Detach + prevents the tick from working
  
boolean tick();                             // The method controlling the servo should be interviewed as often as possible.
                                            // Returns true when the target position is reached.
                                            // It has a built-in timer with a SERVO PERIOD period.
boolean tickManual();                       // The method that controls the servo without a built-in timer.
                                            // Returns true when the target position is reached
void setSpeed(int speed);                   // Maximum speed setting (degrees per second)
void setAccel(float accel);                 // Acceleration installation (0.05 - 1.0). At 1 the acceleration is maximum. 0 - disabled
void setAccel(int accel);                   // installation of acceleration in degrees / sec / sec (working from 1 to ~ 1500). 0 - disabled
void setTarget(int target);                 // setting the target position in μs (500 - 2400)
void setTargetDeg(int target);              // setting the target position in degrees (0-max. angle). Depends on min and max
void setAutoDetach(boolean set);            // On/off automatic shutdown (detach) when reaching the angle. Shut up. plug-in
void setCurrent(int target);                // setting the current position in μs (500 - 2400)
void setCurrentDeg(int target);             // setting the current position in degrees (0-max. angle). Depends on min and max
void setMaxAngle(int maxAngle);             // Max installation. drive-angle
int getCurrent();                           // obtaining the current position in μs (500 - 2400)
int getCurrentDeg();                        // obtaining the current position in degrees (0-max. angle). Depends on min and max
int getTarget();                            // obtaining the target position in μs (500 - 2400)
int getTargetDeg();                         // obtaining the target position in degrees (0-max. angle). Depends on min and max
void smoothStart();              // Call immediately after attach (pin, target). It softens the movement of the servo from an unknown position to the starting position. Blocking for 1 sec!
```

<a id="example"></a>
## Example
For more examples see **examples**!
```cpp
/*
   Данный код плавно управляет одной сервой (на пине 2)
   при помощи потенциометра (на пине А0)
   Документация: https://alexgyver.ru/servosmooth/
*/

#include <ServoSmooth.h>
ServoSmooth servo;

void setup() {
  Serial.begin(9600);
  servo.attach(2, 600, 2400);  // 600 and 2400 are the pulse lengths at which
  // The servo is rotated as much as possible in one and the other direction, depending on the servo itself.
  // They are usually even listed by the seller. We're putting them here so that
  // setTargetDeg() correctly worked out the full range of rotation of the serva
  
  servo.setSpeed(50);   // speed
  servo.setAccel(0.3);  	// set up acceleration (acceleration and braking)
  
  servo.setAutoDetach(false);	// turn off auto-detach when the target angle is reached (on by default)
}

void loop() {
  // The desired position is setTarget (pulse) or setTargetDeg (angle), then
  // When calling the tick() the servo is automatically moved
  // predetermined acceleration and speed limit
  servo.tick();   // This is where the servo moves on the built-in timer!

  int newPos = map(analogRead(0), 0, 1023, 0, 180); // Take from the potentometer value 0-180
  servo.setTargetDeg(newPos);     					// And send it to the servo.
}
```

<a id="versions"></a>
## Versions
- v1.1 Automatic detachment when the goal is reached
- v1.2 - on/off auto-off servo
- v1.3 is a separate method for setting and reading the current position. Added version of the attach method
- v1.4 - improved compatibility
- v1.5 - fix getCurrent and getCurrentDeg
- v1.6 - slightly optimized initialization
- v1.7 - Fixed low speed/acceleration bug, code optimized
- v1.8 - improved stability
- v1.9 - Added setting max. servo
- v1.10 - fix the bug when the current position coincides with the target position
    
- v2.0 - simplified algorithm
- v2.1 - change of direction added
- v2.2 - Fix inversion motion (thanks VICLER) and write functions (thanks CheDima)
    
- v3.0 
    - Added a completely new, smoother algorithm
    - Cleaned up trash.
    - Added support for PCA9685
    - Smoothness placed in base class to simplify adding support for new servo libraries
    
- v3.1 - optimized and lightweight algorithm, speed is set in degrees / sec
- v3.2 - Fixed a bug with a sharp turn at the first tick, added smoothStart
- v3.3 - fixes a bug that occurs if you do not call the tick
- v3.4 - when the acceleration is set to 0, the constant speed profile is activated
    
- v3.5 
    - Improved/fixed work stop
    - Rounding errors corrected
    - Fixed the problem with slow target change
    
- v3.6 - Minor bugs corrected, debug removed from 3. 5
- v3.7 - Added acceleration task in degrees/sec/sec
- v3.8 - AutoDetach(false) non-return
- v3.9 - ESP32 support

<a id="feedback"></a>
## Bugs and feedback
If you find bugs, create **Issue**, or better write to the mail immediately.[alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
The library is open for revision and your **Pull Requests*!

When reporting bugs or incorrect work of the library, it is necessary to specify:
- Library version
- What is used by the IC
- SDK version (for ESP)
- Arduino IDE version
- Are embedded examples that use features and designs that cause bugs in your code working correctly?
- What code was downloaded, what work was expected from it and how it works in reality
- Ideally, attach the minimum code in which the bug is observed. Not a canvas of a thousand lines, but a minimum code.
