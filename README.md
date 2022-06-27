[![Foo](https://img.shields.io/badge/Version-3.8-brightgreen.svg?style=flat-square)](#versions)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/ServoSmooth?_x_tr_sl=ru&_x_tr_tl=en)  
[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)


# ServoSmooth
Библиотека для плавного управления сервоприводами
- Дополнение к стандартной библиотеке Servo
- Настройка максимальной скорости сервопривода
- Настройка ускорения (разгон и торможение) сервопривода
- Неблокирующая работа
- Трапецеидальный профиль скорости
- При использовании ESC и БК мотора получаем "плавный пуск" мотора
- Установка целевой позиции серво по углу (0-180) и длине импульса (500-2400)
- Автоматическое отключение (detach) при достижении цели
- Плавный пуск при подключении серво
- Поддержка расширителя PCA9685

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

### Документация
К библиотеке есть [расширенная документация](https://alexgyver.ru/ServoSmooth/)

## Содержание
- [Установка](#install)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **ServoSmooth** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/ServoSmooth/archive/refs/heads/main.zip) .zip архивом для ручной установки:
    - Распаковать и положить в *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Распаковать и положить в *C:\Program Files\Arduino\libraries* (Windows x32)
    - Распаковать и положить в *Документы/Arduino/libraries/*
    - (Arduino IDE) автоматическая установка из .zip: *Скетч/Подключить библиотеку/Добавить .ZIP библиотеку…* и указать скачанный архив
- Читай более подробную инструкцию по установке библиотек [здесь](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Обновление
- Рекомендую всегда обновлять библиотеку: в новых версиях исправляются ошибки и баги, а также проводится оптимизация и добавляются новые фичи
- Через менеджер библиотек IDE: найти библиотеку как при установке и нажать "Обновить"
- Вручную: **удалить папку со старой версией**, а затем положить на её место новую. "Замену" делать нельзя: иногда в новых версиях удаляются файлы, которые останутся при замене и могут привести к ошибкам!


<a id="init"></a>
## Инициализация
```cpp
ServoSmooth servo;
ServoDriverSmooth servo;    // для PCA9685
```

<a id="usage"></a>
## Использование
```cpp
void write(uint16_t angle);                 // аналог метода из библиотеки Servo
void writeMicroseconds(uint16_t angle);     // аналог метода из библиотеки Servo
void attach(uint8_t pin);                   // аналог метода из библиотеки Servo
void attach(uint8_t pin, int min, int max); // аналог метода из библиотеки Servo. min по умолч. 500, max 2400
void detach();                              // аналог метода detach из библиотеки Servo
void start();                               // attach + разрешает работу tick
void stop();                                // detach + запрещает работу tick
  
boolean tick();                             // метод, управляющий сервой, должен опрашиваться как можно чаще.
                                            // Возвращает true, когда целевая позиция достигнута.
                                            // Имеет встроенный таймер с периодом SERVO_PERIOD
boolean tickManual();                       // метод, управляющий сервой, без встроенного таймера.
                                            // Возвращает true, когда целевая позиция достигнута
void setSpeed(int speed);                   // установка максимальной скорости (градусы в секунду)
void setAccel(float accel);                 // установка ускорения (0.05 - 1.0). При значении 1 ускорение максимальное. 0 - отключено
void setAccel(int accel);                   // установка ускорения в градусах/сек/сек (рабочее от 1 до ~1500). 0 - отключено
void setTarget(int target);                 // установка целевой позиции в мкс (500 - 2400)
void setTargetDeg(int target);              // установка целевой позиции в градусах (0-макс. угол). Зависит от min и max
void setAutoDetach(boolean set);            // вкл/выкл автоматического отключения (detach) при достижении угла. По умолч. вкл
void setCurrent(int target);                // установка текущей позиции в мкс (500 - 2400)
void setCurrentDeg(int target);             // установка текущей позиции в градусах (0-макс. угол). Зависит от min и max
void setMaxAngle(int maxAngle);             // установка макс. угла привода
int getCurrent();                           // получение текущей позиции в мкс (500 - 2400)
int getCurrentDeg();                        // получение текущей позиции в градусах (0-макс. угол). Зависит от min и max
int getTarget();                            // получение целевой позиции в мкс (500 - 2400)
int getTargetDeg();                         // получение целевой позиции в градусах (0-макс. угол). Зависит от min и max
void smoothStart();              // вызывать сразу после attach(пин, таргет). Смягчает движение серво из неизвестной позиции к стартовой. БЛОКИРУЮЩАЯ НА 1  СЕК!
```

<a id="example"></a>
## Пример
Остальные примеры смотри в **examples**!
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
  servo.attach(2, 600, 2400);  // 600 и 2400 - длины импульсов, при которых
  // серво поворачивается максимально в одну и другую сторону, зависят от самой серво
  // и обычно даже указываются продавцом. Мы их тут указываем для того, чтобы
  // метод setTargetDeg() корректно отрабатывал полный диапазон поворота сервы
  
  servo.setSpeed(50);   // ограничить скорость
  servo.setAccel(0.3);  	// установить ускорение (разгон и торможение)
  
  servo.setAutoDetach(false);	// отключить автоотключение (detach) при достижении целевого угла (по умолчанию включено)
}

void loop() {
  // желаемая позиция задаётся методом setTarget (импульс) или setTargetDeg (угол), далее
  // при вызове tick() производится автоматическое движение сервы
  // с заданным ускорением и ограничением скорости
  servo.tick();   // здесь происходит движение серво по встроенному таймеру!

  int newPos = map(analogRead(0), 0, 1023, 0, 180); // берём с потенцометра значение 0-180
  servo.setTargetDeg(newPos);     					// и отправляем на серво
}
```

<a id="versions"></a>
## Версии
- v1.1 - автоматическое отключение (detach) при достижении цели
- v1.2 - вкл/выкл автоотключения серво
- v1.3 - отдельный метод для установки и чтения текущего положения. Добавлен вариант метода attach
- v1.4 - улучшена совместимость
- v1.5 - исправлены getCurrent и getCurrentDeg
- v1.6 - чуть оптимизирована инициализация
- v1.7 - исправлен баг с низкой скоростью/ускорением, код оптимизирован
- v1.8 - улучшена стабильность
- v1.9 - добавлена настройка макс. угла серво
- v1.10 - исправлен баг когда текущая позиция совпадает с позицией таргета
    
- v2.0 - упрощён алгоритм
- v2.1 - добавлена смена направления
- v2.2 - фикс движения в инверсии (спасибо VICLER) и функций write (спасибо CheDima)
    
- v3.0 
    - Добавлен полностью новый, более плавный алгоритм
    - Почищен мусор
    - Добавлена поддержка PCA9685
    - "Плавность" вынесена в базовый класс для упрощения добавления поддержки новых библиотек серво
    
- v3.1 - оптимизирован и облегчён алгоритм, скорость задаётся в градусах/сек
- v3.2 - исправлен баг с резким поворотом при первом tick, добавлена smoothStart
- v3.3 - исправлен баг, возникающий если не вызывать tick
- v3.4 - при установке ускорения в 0 активируется профиль постоянной скорости
    
- v3.5 
    - Улучшена/исправлена работа stop
    - Поправлены ошибки с округлением
    - Исправлена проблема с медленным изменением target
    
- v3.6 - Исправлены мелкие баги, вырезан дебаг с 3.5
- v3.7 - Добавлено задание ускорения в градусах/сек/сек
- v3.8 - Исправлен невозврат тика при autoDetach(false)

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!


При сообщении о багах или некорректной работе библиотеки нужно обязательно указывать:
- Версия библиотеки
- Какой используется МК
- Версия SDK (для ESP)
- Версия Arduino IDE
- Корректно ли работают ли встроенные примеры, в которых используются функции и конструкции, приводящие к багу в вашем коде
- Какой код загружался, какая работа от него ожидалась и как он работает в реальности
- В идеале приложить минимальный код, в котором наблюдается баг. Не полотно из тысячи строк, а минимальный код
