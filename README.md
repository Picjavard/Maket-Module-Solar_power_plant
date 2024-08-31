# Maket-Module-Solar_power_plant
 Модуль солнечной электростанции для макета демонстрации альтернативных источников электроэнергии

## Список компонентов

* Солнечная панель - https://sl.aliexpress.ru/p?key=nrgsrmt
* Микроконтроллер Arduino - https://sl.aliexpress.ru/p?key=OQhsrnW
* Сервомотор - https://sl.aliexpress.ru/p?key=pUgsrxQ
* Резистор 10кОм - 2 шт. - https://sl.aliexpress.ru/p?key=KjhsrsY

## 3D-модели для печати

Thingiverse: https://www.thingiverse.com/thing:6749891

![Солнечная панель](https://github.com/user-attachments/assets/e9326493-2ad9-49cf-a8d0-80d526a6be48)

![СЭС1](https://github.com/user-attachments/assets/c871635e-f5cc-4269-89ca-6b0ef366a592)

![СЭС2](https://github.com/user-attachments/assets/0db41f75-a3d7-449d-a527-c47978a39f3f)

## Подключение

### OLED-дисплей

| OLED-дисплей	| Arduino Nano |	Arduino Micro / Pro Micro |
| :---:| :---:| :---:|
| GND	| GND |	GND |
| VCC	| 5V |	5V |
| SCL	| A5 | 3 |
| SDA	| A4 |	2 |

### Сервомотор


| Сервомотор	| Arduino Nano / Micro / Pro Micro |
| :---:| :---:| 
| GND (черный/коричневый)	| GND |	
| VCC (красный)	| 5V / VIN |	
| SIG (желтый/оранжевый)	| 9 | 

### Солнечная панель

![circuit (3)](https://github.com/user-attachments/assets/fb4c3fdf-6baf-4b4d-bb6f-68ae6708311c)

## Код

```cpp
#include <ServoSmooth.h>
ServoSmooth servo;

#include <GyverOLED.h>
GyverOLED<SSD1306_128x64, OLED_NO_BUFFER> oled;

#include <TimerMs.h>
// (период, мс), (0 не запущен / 1 запущен), (режим: 0 период / 1 таймер)
TimerMs tmrMeasure(10000, 1, 1);
TimerMs tmrDisplay(1000, 1, 1);

boolean flag;

void setup() {
  //Serial.begin(9600);

  oled.init();              // инициализация
  oled.clear();             // очистка дисплея
  oled.autoPrintln(false);  // отключить автопереход на новую строку
  oled.setScale(4);         // масштаб текста
  oled.setCursor(0, 0);     // установка позиции курсора
  oled.print("Init");
  oled.update();            // обновить экран

  delay(200);
  
  servo.attach(9);      // подключить
  servo.setSpeed(20);   // ограничить скорость
  servo.setAccel(0.1);  // установить ускорение (разгон и торможение)
  servo.setTargetDeg(90);
  delay(500);
  
  tmrMeasure.setPeriodMode();
  tmrDisplay.setPeriodMode();
}

void loop() {
  servo.tick();

  if (tmrMeasure.tick()) {  // каждые 10 сек
    flag = !flag;
    servo.setTargetDeg(flag ? 20 : 160);  // двигаем на углы 50 и 120
  }
  
  if (tmrDisplay.tick()) { // каждую 1 сек
    float voltage = (float)(analogRead(0) * 5.0) / 1024;
    oled.clear();
    oled.setCursor(0, 0);
    //oled.print(servo.getCurrentDeg());
    oled.setCursor(0, 4);
    oled.print(voltage);
    oled.print("В");
    oled.update();
    //Serial.println(voltage);
  }
}

```
