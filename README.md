# Maket-Module-Solar_power_plant
 Модуль солнечной электростанции для макета демонстрации альтернативных источников электроэнергии

## Список компонентов

* Солнечная панель
* Микроконтроллер Arduino
* Сервомотор
* Резистор 1кОм - 2 шт.


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

![circuit (2)](https://github.com/user-attachments/assets/9a3a9cce-010f-40cf-a27a-c24e0de0f166)

### 3D-модель



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