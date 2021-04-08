# Practica 2 Bosque Arnau
## Apartado A
### Codigo
````
#include <Arduino.h>

struct Button {
  const uint8_t PIN;
  uint32_t numberKeyPresses;
  bool pressed;
};

Button button1 = {0, 0, false};

void IRAM_ATTR isr() {
  button1.numberKeyPresses += 1;
  button1.pressed = true;
}

void setup() {
  Serial.begin(9600);
  pinMode(button1.PIN, INPUT_PULLUP);
  attachInterrupt(button1.PIN, isr, FALLING);
}

void loop() {
  if (button1.pressed) {
      Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
      button1.pressed = false;
  }

  //Detach Interrupt after 1 Minute
  static uint32_t lastMillis = 0;
  if (millis() - lastMillis > 60000) {
    lastMillis = millis();
    detachInterrupt(button1.PIN);
     Serial.println("Interrupt Detached!");
  }
}
````
### Funcionamiento
Podemos observar en el void setup que le aplicamos el button1.PIN como el GPIOPin, este dice al ESP32 que clavija deberá monitorear. Inmediatamente despues se llamamos al void anterior el isr, que será el que dispare la interrupción. 

En el caso de las interrupciones podemos observar que el PIN GPIO lo hemos definido en el mode FALLING, eso quiere decir que nuestros disparadores interrumpiran cuando el pin vaya de HIGH a LOW. 

Finalmente en el Detach lo que hacemos es que el boton que llamemos se le eliminara la funcion que tenia y por lo tanto se le eliminara el interrupt.