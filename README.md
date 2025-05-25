# Garbage Segregation and Bin Level indication
<p>
&nbsp;&nbsp;&nbsp;&nbsp; In this, Garbage is seperated by two DRY and WET. Both are indivdually disposed in seperate box by the sensors and microcontroller. This is done with online simulation webapp - Tinkercad.
</p>

---
## Here is the references...

Circuit Connections :-

<img src=https://github.com/lingeshkumarkamaraj/Home-Automation/blob/main/Home%20automation.png> 

Serial monitor :-

![Image](https://github.com/lingeshkumarkamaraj/Home-Automation/blob/main/Blynk.png)

Working :- 

[<img width="300" height="300" src="https://img.icons8.com/color/96/start.png" alt="video"/>](https://youtu.be/dd_do7k_1IA)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [DOWNLOAD](https://github.com/lingeshkumarkamaraj/Home-Automation/raw/refs/heads/main/home%20automation.mp4) 

---
Code :-
```
#include <Servo.h>

Servo servo1;

// Ultrasonic Sensor 1 (Waste Detection)
const int trigPin1 = 12;
const int echoPin1 = 11;

// Ultrasonic Sensor 2 (Bin Level Check)
const int trigPin2 = 10;
const int echoPin2 = 9;

// LED for Bin Full Indication
const int ledPin = 7;

// Soil Moisture Sensor
const int soilPin = A0;

// Variables
long duration;
int distance = 0;
int distanceBin = 0;
int soil = 0;
int fsoil;
int maxDryValue = 30; // Change this threshold as per sensor calibration
int Ultra_Distance = 200; // Detect garbage within 200 cm
int Bin_Full_Distance = 10; // Bin is full if waste is within 10 cm of the sensor

void setup() {
    Serial.begin(9600);
    pinMode(trigPin1, OUTPUT); 
    pinMode(echoPin1, INPUT); 

    pinMode(trigPin2, OUTPUT);
    pinMode(echoPin2, INPUT);
    
    pinMode(ledPin, OUTPUT);
    servo1.attach(8);

    Serial.println("Soil Sensor     Ultrasonic 1     Servo     Bin Status");
}

void loop() {
    // Get distance from first ultrasonic sensor (Garbage detection)
    digitalWrite(trigPin1, LOW);
    delayMicroseconds(2);
    digitalWrite(trigPin1, HIGH);
    delayMicroseconds(10);
    digitalWrite(trigPin1, LOW);
    duration = pulseIn(echoPin1, HIGH);
    distance = duration * 0.034 / 2;

    // Get soil moisture value
    soil = analogRead(soilPin);
    soil = constrain(soil, 485, 1023);
    fsoil = map(soil, 485, 1023, 100, 0); // Convert to percentage

    // Print sensor values in serial monitor
    Serial.print("Soil Moisture: ");
    Serial.print(fsoil);
    Serial.print("%    Garbage Distance: ");
    Serial.print(distance);
    Serial.print(" cm");

    // If garbage is detected within 200 cm
    if (distance > 1 && distance < Ultra_Distance) {
        if (fsoil > maxDryValue) {
            Serial.println("     ==> WET Waste");
            servo1.write(170); // Move servo to wet waste position
        } else {
            Serial.println("     ==> DRY Waste");
            servo1.write(-170);  // Move servo to dry waste position
        }
    } else {
        servo1.write(90); // Keep servo in neutral position
    }

    // Check if bin is full
    digitalWrite(trigPin2, LOW);
    delayMicroseconds(2);
    digitalWrite(trigPin2, HIGH);
    delayMicroseconds(10);
    digitalWrite(trigPin2, LOW);
    duration = pulseIn(echoPin2, HIGH);
    distanceBin = duration * 0.034 / 2;

    Serial.print("    Bin Distance: ");
    Serial.print(distanceBin);
    Serial.println(" cm");

    if (distanceBin < Bin_Full_Distance && distanceBin > 1) {
        Serial.println("     ==> BIN FULL! LED ON");
        digitalWrite(ledPin, HIGH);  // Turn on LED if bin is full
    } else {
        Serial.println("     ==> BIN EMPTY! LED OFF");
        digitalWrite(ledPin, LOW);  // Turn off LED if bin is empty
    }

    delay(500); // Small delay for continuous real-time monitoring
}

```
---
[TINKER LINK](https://www.tinkercad.com/things/8X2fFIpEiTK-embedded)
---
