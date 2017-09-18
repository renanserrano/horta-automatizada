# Horta Automatizada
Conceito para a contrução de uma horta que detecta umidade da terra, do ambiente, temperatura e iluminação. Sua lógica pode ser aplicada em outros projetos.

A instrução a seguir descorrerá da seguinte maneira:
1. Materiais
2. Sensores
3. Montagem do painel lcd
4. Código para testar o painel lcd
5. Código básico do painel
6. Montagem do DHT e código básico
7. Código do DHT para exibir no lcd
8. Montagem sensor LDR (iluminação do ambiente)
9. Código LDR
10. Código do LDR, DHT e LCD
11. Montagem higrômetro
12. Código higrômetro
13. Código do higrômetro, LDR, DHT e LCD
14. Montagem rele (ativador do esguicho de água)
15. Código rele
16. Código do rele, higrômetro, LDR, DHT e LCD
17. Montagem Válvula de vasão

## Materiais
* Painel LCD: [JHD204A](http://www.alldatasheet.com/datasheet-pdf/pdf/276144/JHD/JHD204A.html) - 20x4
Nos exemplos do arduino, existe o LiquidCrystal - Hello World
* Arduino Uno
* Jumpers macho-macho e femea-macho
* Protoboard (para prototipagem)
* 1 Resistor 330 Ohms (para o painel)
* 1 Resistor 10k para o método Pull-UP (caso o sensor esteja longe da placa)
* 1 Potenciômetro 10k (usá-lo somente se o painel não tem auto-contraste)
* Rele 5V 1 canal(para liberaçao da agua de irrigação)
* Vávula solenóide água 12VDC ou 110V

###### Sensores:
* 1 Sensor de temperatura e umidade ambiente: DHT22 (não é necessário, no projeto ele esta instalado na placa do projeto, mas o ideal é instalar ele na estufa ou no ambiente externo proximo da horta). A versão 22 é mais precisa [Comprar](https://www.filipeflop.com/produto/sensor-de-umidade-e-temperatura-am2302-dht22/) | [Datasheet](https://www.sparkfun.com/datasheets/Sensors/Temperature/DHT22.pdf)
* 1 Sensor LDR já com resistor
* 1 Sensor de umidade no solo Higrômetro: DN1619

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
| 2 | VDD/VCC | VCC     |
| 15 |anodo com resistor de 330 |VCC|
| 16 |catodo| ground  |
| 3 | V0 | Potenciômetro 10k pino 2 (meio) |
| - | - | Potenciometro 10k pino 1 (positivo) |
| - | - | Potenciometro 10k pino 3 (ground) |

(VCC: 5V ou 3V)
<img src="https://www.arduino.cc/en/uploads/Tutorial/LCD_Base_bb_Fritz.png" width="500"/>

## Arduino teste: Hello World!
Para testar o painel LCD
`Definir o Arduino Uno e a porta correta` +
Lembre-se sempre de definir o tamanho do painel em `lcd.begin(16, 2);` ou `lcd.begin(20,4);`

```C
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

void setup() {
    lcd.begin(20, 4);
    lcd.print("hello, world!");
}

void loop() {
    lcd.setCursor(0, 1);
    lcd.print(millis() / 1000);
}
```
## Código LCD
```C
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

void setup() {
    lcd.begin(20, 4);
}

void loop() {
    delay(100);
}

lcd.setCursor(0, 0);
  lcd.print("Temperatura ");
  
  lcd.setCursor(0, 1);
  lcd.print("Umidade ");
  }
```

## Arduino código: DHT
Para testar o DHT
`Incluir Biblioteca: DHT Sensor Library by Adafruit` e 
`Incluir Biblioteca: Adafruit Unified Sensor` (a última da lista)

| DHT | DHT  |Ardu/Proto|
| :---| :---:|  ---:   |
|Saída| Nome | Pin     |
| 1   |VDD/VCC| 5V     |
| 2   | DATA |  7      |
| 3   | NULL |   -     |
| 4   | GND  |ground/negativo|


```C
#include "DHT.h"
DHT dht(7, DHT22);

void setup() {
  dht.begin();
}

void loop() {
  delay(2000);
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  
  if (isnan(h) || isnan(t)) {
  Serial.println("Failed to read from DHT sensor!");
  return;
}

  // Incluir este caso haja mais de um sensor, tirar a média em Celsius
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
`lcd.print(t);` quando não coloca entre "aspas", significa que você está referenciado/chamando a variável citada anteriormente.

## Código com LCD + DHT
Os dois código
```C
#include "DHT.h"
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
DHT dht(7, DHT22);

void setup() {
    lcd.begin(20, 4);
    dht.begin();
}

void loop() {
    delay(100);
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  
  if (isnan(h) || isnan(t)) {
  Serial.println("Failed to read from DHT sensor!");
  return;
}

lcd.setCursor(0, 0);
  lcd.print("Temperatura ");
  lcd.print(t); //referencia ao float
  lcd.print("C");
  
  lcd.setCursor(0, 1);
  lcd.print("Umidade ");
  lcd.print(h);
  lcd.print("%");
  }
```
Para farenheit:
`float t = dht.readTemperature(); //Se colocar (true) vira farenheit` dai tbm precisa alterar o ` if (isnan(h) || isnan(t)) {` para ` if (isnan(h) || isnan(f))`


## Sendor LDR
| LDR | Arduino|
| :---| ---:|
| GND | GND|
| VCC | VCC |

```C
const int SensorLuz = A0;
int luz;

void setup() {
	}

void loop() {
luz = analogRead(SensorLuz);
luz = map(luz,0,1023,0,100) // ele de fábrica lê de 0 a 1023 e agora vai ler de 0 a 100

lcd.setCursor(0,2);
lcd.print("Luminosidade");
lcd.print(luz);
lcd.print(~);
}
```

## Código LDR + DHT + LCD
```C
#include "DHT.h"
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
DHT dht(7, DHT22);

const int SensorLuz = A0;
int luz;

void setup() {
  lcd.begin(20, 4);
  dht.begin();
}

void loop() {
  delay(100);
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    lcd.println("Failed");
    return;
  }

    luz = analogRead(SensorLuz);
    luz = map(luz,0,1023,0,100); // ele de fábrica lê de 0 a 1023 e agora vai ler de 0 a 100

    lcd.setCursor(0, 0);
    lcd.print("Temperatura ");
    lcd.print(t);
    lcd.print("C");

    lcd.setCursor(0, 1);
    lcd.print("Umidade ");
    lcd.print(h);
    lcd.print("%");

    lcd.setCursor(0, 2);
    lcd.print("Luminosidade ");
    lcd.print(luz);
    lcd.print("% ");
}
```
`  float t = dht.readTemperature(); //Se colocar (true) vira farenheit` e `luz = map(luz,0,1023,0,100); // ele de fábrica lê de 0 a 1023 e agora vai ler de 0 a 100`

## Higrômetro - sensor de umidade da terra
| Higrômetro | Arduino|
| :---| ---:|
| GND | GND|
| VCC | VCC |
| A0 | A1 |

```C
const int SensorUmidade = A1;
int TerraUmidade;

void setup() {

}

void loop() {
  TerraUmidade = analogRead(SensorUmidade);
  TerraUmidade = map(umidade,0,1023,100,0);

  lcd.setCursor(0,3);
  lcd.print("Umidade ");
  lcd.print(TerraUmidade);
  lcd.print("%");

}
```
`//aqui se inverter a posição (umidade,0,1023,0,100) ele vai ler oposto`

## Código Higrômetro + LDR + DHT + LCD
```C
#include "DHT.h"
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
DHT dht(7, DHT22);

const int SensorLuz = A0;
int luz;

const int SensorUmidade = A1;
int TerraUmidade;

void setup() {
  lcd.begin(20, 4);
  dht.begin();
}

void loop() {
  delay(100);
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    lcd.println("Failed");
    return;
  }

    luz = analogRead(SensorLuz);
    luz = map(luz,0,1023,0,100);

    TerraUmidade = analogRead(SensorUmidade);
    TerraUmidade = map(umidade,0,1023,100,0);

    lcd.setCursor(0, 0);
    lcd.print("Temperatura ");
    lcd.print(t);
    lcd.print("C");

    lcd.setCursor(0, 1);
    lcd.print("Umidade ");
    lcd.print(h);
    lcd.print("%");

    lcd.setCursor(0, 2);
    lcd.print("Luminosidade ");
    lcd.print(luz);
    lcd.print("% ");

    lcd.setCursor(0,3);
    lcd.print("Umidade ");
    lcd.print(TerraUmidade);
    lcd.print("%");
}
```

## Válvula
Existe opção de LED e Rele 5V 1 canal, no rele tem um jumper para definir se ele fica sempre em High ou em Low, repare que no código, ele esta em High

| LED | Arduino| Rele   |
| :---| :---:  | ----:  |
| VCC | 13     |  IN    |
| GND |resisGND| DC-    |
| -   | VCC    |  DC+   |


```C
const int valvula = 13;

void setup() {
	pinMode(valvula,OUTPUT);

}

void loop() {
	if ((TerraUmidade < 50) && (luz < 10)){
		digitalWrite (valvula, HIGH);
		delay(500);
	}
	else {
		digitalWrite(valvula, LOW);
	}
}
```

## Código Rele/LED + Higrômetro + LDR + DHT + LCD
```C
#include "DHT.h"
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
DHT dht(7, DHT22);

const int SensorLuz = A0;
int luz;

const int SensorUmidade = A1;
int TerraUmidade;

const int valvula = 13;

void setup() {
  lcd.begin(20, 4);
  dht.begin();
  pinMode(valvula, OUTPUT);
}

void loop() {
  delay(100);
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    lcd.println("Failed");
    return;
  }

  luz = analogRead(SensorLuz);
  luz = map(luz, 0, 1023, 0, 100);

  TerraUmidade = analogRead(SensorUmidade);
  TerraUmidade = map(TerraUmidade, 0, 1023, 100, 0);

  lcd.setCursor(0, 0);
  lcd.print("Temperatura ");
  lcd.print(t);
  lcd.print("C");

  lcd.setCursor(0, 1);
  lcd.print("Umidade ");
  lcd.print(h);
  lcd.print("%");

  lcd.setCursor(0, 2);
  lcd.print("Luminosidade ");
  lcd.print(luz);
  lcd.print("% ");

  lcd.setCursor(0, 3);
  lcd.print("Umidade ");
  lcd.print(TerraUmidade);
  lcd.print("%");

  if ((TerraUmidade < 50) && (luz < 10)) {
    digitalWrite (valvula, HIGH);
    delay(500);
  }
  else {
    digitalWrite(valvula, LOW);
  }
}
```

## Solenóide
Válvula de vasão solenóide água 12VDC ou 110V.
A 12VDC, tem uma perna prédefinada para o GND e outra para o VCC

| 12VDC | Fonte Arduino| Rele |
| :---| :---:  | ----:  |
| VCC| -   | NO |
| GND|GND  | -  |
| -  | VCC | COM |

A porta NO, significa Normalmente Aberto (Normally Open), o circuito está aberto, ou seja, não está dando contato e como consequência, esta desativado.

| 110V | Tomada  | Rele   |
| :---  | :---:  | ----:  |
| VCC   | -      |  NO    |
| GND   |GND     | -      |
| -     | VCC    |  COM   |
