# Horta Automatizada
Conceito para a contrução de uma horta que detecta umidade da terra, do ambiente, temperatura e iluminação. Sua lógica pode ser aplicada em outros projetos.

## Materiais
* Painel LCD: [JHD204A](http://www.alldatasheet.com/datasheet-pdf/pdf/276144/JHD/JHD204A.html) - 16x4
Nos exemplos do arduino, existe o LiquidCrystal - Hello World
* Arduino Uno
* 12 Jumpers
* Protoboard
* 1 Resistor 330 Ohms
* 1 Resistor 10k para o método Pull-UP (caso o sensor esteja longe da placa)
* 1 Potenciômetro: usá-lo somente se o painel for 16x2 onde não tem auto-contraste

###### Sensores:
* 1 Sensor de temperatura e umidade ambiente: DHT22 (não é necessário, no projeto ele esta instalado na placa do projeto, mas o ideal é instalar ele na estufa ou no ambiente externo proximo da horta). A versão 22 é mais precisa [Comprar](https://www.filipeflop.com/produto/sensor-de-umidade-e-temperatura-am2302-dht22/) | [Datasheet](https://www.sparkfun.com/datasheets/Sensors/Temperature/DHT22.pdf)
* 1 Sensor de umidade no solo Higrômetro: DN1619
* 1 Sensor LDR

## Montagem
###### Painel de LCD: soltar o barramento macho e encaixar na protoboard
| LCD | LCD  |Arduino/Proto|
| :---|:---: |---:     |
|Saída| Nome | Pin     |
| 4 |  RS  |   12      |
| 6 |Enable|   11      |
| 11 |  D4  |    5     |
| 12 |  D5  |    4     |
| 13 |  D6  |    3     |
| 14 |  D7  |    2     |
| 5 |  R/W |  ground   |
| 1 |  VSS |  ground   |
| 2 | VDD/VCC | 5V     |
| 15 |10k resistor positivo |end 5V + ground|
| 16 |10k resistor| negativo/ground  |
| 3 | Potenciômetro 5V e ground(negativo) | - |

<img src="https://www.arduino.cc/en/uploads/Tutorial/LCD_Base_bb_Fritz.png" width="500"/>

## Arduino código: Hello World!
Para testar o painel LCD
`Definir o Arduino Uno e a porta correta`
`Incluir Biblioteca: DHT Sensor Library by Adafruit`

```
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

void setup() {
  // put your setup code here, to run once:
    lcd.begin(16, 2);
    lcd.print("hello, world!");
}

void loop() {
  // put your main code here, to run repeatedly:
    lcd.setCursor(0, 1);
    lcd.print(millis() / 1000);
}
```

## Arduino código: DHT
Para testar o DHT
`Incluir Biblioteca: DHT Sensor Library by Adafruit` e 
`Incluir Biblioteca: Adafruit Unified Sensor`

| DHT | DHT  |Ardu/Proto|
| :---| :---:|  ---:   |
|Saída| Nome | Pin     |
| 1   |VDD/VCC| 5V     |
| 2   | DATA |  7      |
| 3   | NULL |   -     |
| 4   | GND  |ground/negativo|


```
#include "DHT.h"
// #define DHTPIN 7 //Porta data que esta conectado no arduino
// #define DHTTYPE DHT22
DHT dht(7, DHT22);

void setup() {
  dht.begin();
}

void loop() {
  delay(2000);
  float h = dht.readHumidity();
  float t = dht.readTemperature(); //Se colocar (true) vira farenheit
  
  if (isnan(h) || isnan(t) || isnan(f)) {
  Serial.println("Failed to read from DHT sensor!");
  return;
}

  // Caso haja mais de um sensor, tirar a média em Celsius
  //float hic = dht.computeHeatIndex(t, h, false);
  
  lcd.setCursor(0, 0);
  lcd.print("Temperatura ");
  lcd.print(t); //referencia ao float
  lcd.print("˚C");
  
  lcd.setCursor(0, 1);
  lcd.print("Umidade ");
  lcd.print(h);
  lcd.print("%");
  }
  
```

## Código com LCD + DHT
Os dois código
```
#include <LiquidCrystal.h>
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

DHT dht(7, DHT22);

void setup() {
  // put your setup code here, to run once:
    lcd.begin(16, 2);
    dht.begin();
}

void loop() {
    delay(100);
  float h = dht.readHumidity();
  float t = dht.readTemperature(); //Se colocar (true) vira farenheit
  
  if (isnan(h) || isnan(t) || isnan(f)) {
  Serial.println("Failed to read from DHT sensor!");
  return;
}

lcd.setCursor(0, 0);
  lcd.print("Temperatura ");
  lcd.print(t); //referencia ao float
  lcd.print("˚C");
  
  lcd.setCursor(0, 1);
  lcd.print("Umidade ");
  lcd.print(h);
  lcd.print("%");
  }
```
###### Método Pull-up
`Evita interferências quando o sensor está a longas distâncias do arduino: incluir um resistor de 10k do pino Data (2) para o power VCC (5V)`
