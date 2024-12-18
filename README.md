# Prova

## 1. Descrição do Projeto
Este projeto simula semáforos usando sensores de luz (LDR), que, durante condições de baixa luminosidade, o sistema entra em modo noturno.


### 2: Programação
```
#include <WiFi.h>
#include <HTTPClient.h>

#define led_green 2 // Pin used to control the green LED
#define led_red 40 // Pin used to control the red LED
#define led_yellow 9 // Pin used to control the yellow LED

const int buttonPin = 18;  // Pin to which the button is connected
int buttonState = 0;  // Variable to read the button state
// int lastButtonState = LOW;

// unsigned long lastDebounceTime = 0;  // the last time the output pin was toggled
// unsigned long debounceDelay = 50;  

const int ldrPin = 4;  // Pin to which the LDR is connected
int limit = 600;

void setup() {
  // Initial configuration of pins to control the LEDs as OUTPUTs of the ESP32
  pinMode(led_green, OUTPUT);
  pinMode(led_red, OUTPUT);
  pinMode(led_yellow, OUTPUT); // Added for the yellow LED

  // Initialization of inputs
  pinMode(buttonPin, INPUT); // Initialize the button pin as an input
  pinMode(ldrPin, INPUT); // Initialize the LDR pin as an input

  digitalWrite(led_green, LOW);
  digitalWrite(led_red, LOW);
  digitalWrite(led_yellow, LOW); // Added for the yellow LED

  Serial.begin(9600); // Configuration for debugging via serial interface between ESP and computer with baud rate of 9600

  WiFi.begin("Wokwi-GUEST", ""); // Connect to the open WiFi network with SSID Wokwi-GUEST

  while (WiFi.status() != WL_CONNECTED) {
    delay(100);
    Serial.print(".");
  }
  Serial.println("Connected to WiFi successfully!"); // Considering it exited the loop above, the ESP32 is now connected to WiFi

  // Check button state
  buttonState = digitalRead(buttonPin);
  if (buttonState == HIGH) {
    Serial.println("Button pressed!");
  } else {
    Serial.println("Button not pressed!");
  }

  if (WiFi.status() == WL_CONNECTED) { // If the ESP32 is connected to the Internet
    HTTPClient http;

    String serverPath = "http://www.google.com.br/"; // HTTP request endpoint

    http.begin(serverPath.c_str());

    int httpResponseCode = http.GET(); // HTTP request result code

    if (httpResponseCode > 0) {
      Serial.print("HTTP Response: ");
      Serial.println(httpResponseCode);
      String payload = http.getString();
      Serial.println(payload);
    } else {
      Serial.print("Error code: ");
      Serial.println(httpResponseCode);
    }
    http.end();
  } else {
    Serial.println("WiFi disconnected");
  }
}

void loop() {
  int ldrStatus = analogRead(ldrPin);
  buttonState = digitalRead(buttonPin);

  if (ldrStatus <= limit) {
    Serial.print("It's dark, turn on the LED");
    Serial.println(ldrStatus);
  } else {
    Serial.print("It's bright, turn off the light");
    Serial.println(ldrStatus);
  }

  if (ldrStatus > limit) {
    // Conventional mode
    digitalWrite(led_green, HIGH);
    delay(3000); // Green on for 3 seconds
    digitalWrite(led_green, LOW);

    digitalWrite(led_yellow, HIGH);
    delay(2000); // Yellow on for 2 seconds
    digitalWrite(led_yellow, LOW);

    digitalWrite(led_red, HIGH);
    delay(5000); // red on for 5 seconds

    // check if the button was pressed while the red is on
    if (buttonState == HIGH && ldrStatus >= limit) {
      delay(1000); // Wait 1 second after the buton is pressed
      digitalWrite(led_red, LOW);
      digitalWrite(led_green, HIGH);
      delay(3000); // Green on for 3 seconds
      digitalWrite(led_green, LOW);
    }

    digitalWrite(led_red, LOW);
  } else {
    // Night mode
    digitalWrite(led_yellow, HIGH);
    delay(1000); // Yellow on for 1 second
    digitalWrite(led_yellow, LOW);
    delay(1000);
  }
}

//Sources:
//https://docs.arduino.cc/built-in-examples/digital/Debounce/
```
## 3. Funcionamento


<div align="center">
<sub>Figura 1 - ESP 32 - Modo Convencional - Modo Diurno</sub>
<img src="./image.png" width="100%">
<sup>Fonte: Material produzido pelos autores (2024)</sup>
</div>

<div align="center">
<sub>Figura 2 - ESP 32 - Led amarelo piscando - Modo Noturno</sub>
<img src="./image2.png" width="100%">
<sup>Fonte: Material produzido pelos autores (2024)</sup>
</div>

NÃO CONSIGO TIRAR OS PRINTS, WOKWI CONECTA ESPORADICAMENTE. As duas imagens foram adicionadas com atraso se comparadas ao envio do formulário pois o WOKWI não conectava, serginho foi avisado sobre isso no momento da prova.




