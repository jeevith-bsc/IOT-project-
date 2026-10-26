  Arduino Bluetooth Robot Car
  Arduino Uno + HC-05 + L298N
  4 DC motors (2 left, 2 right)

  HC-05 TXD -> Arduino D2
  HC-05 RXD <- Arduino D3 through voltage divider

  L298N:
  ENA -> D5
  IN1 -> D8
  IN2 -> D9
  IN3 -> D10
  IN4 -> D11
  ENB -> D6
*/

#include <SoftwareSerial.h>

// Bluetooth: Arduino RX, TX
SoftwareSerial bluetooth(2, 3);

// L298N motor driver pins
const int ENA = 5;
const int IN1 = 8;
const int IN2 = 9;
const int IN3 = 10;
const int IN4 = 11;
const int ENB = 6;

// Motor speed: 0 to 255
int motorSpeed = 180;

void setup() {
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENB, OUTPUT);

  Serial.begin(9600);
  bluetooth.begin(9600);

  stopCar();

  Serial.println("Bluetooth Robot Car Ready!");
}

void loop() {
  if (bluetooth.available()) {
    char command = bluetooth.read();

    // Support lowercase and uppercase commands
    if (command >= 'a' && command <= 'z') {
      command = command - 'a' + 'A';
    }

    Serial.print("Command: ");
    Serial.println(command);

    switch (command) {
      case 'F':  // Forward
        forward();
        break;

      case 'B':  // Backward
        backward();
        break;

      case 'L':  // Turn left
        left();
        break;

      case 'R':  // Turn right
        right();
        break;

      case 'S':  // Stop
        stopCar();
        break;

      case '0':
        motorSpeed = 0;
        setSpeed();
        break;

      case '1':
        motorSpeed = 100;
        setSpeed();
        break;

      case '2':
        motorSpeed = 140;
        setSpeed();
        break;

      case '3':
        motorSpeed = 180;
        setSpeed();
        break;

      case '4':
        motorSpeed = 220;
        setSpeed();
        break;

      case '5':
        motorSpeed = 255;
        setSpeed();
        break;
    }
  }
}

void setSpeed() {
  analogWrite(ENA, motorSpeed);
  analogWrite(ENB, motorSpeed);
}

void forward() {
  setSpeed();

  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);

  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}

void backward() {
  setSpeed();

  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);

  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

void left() {
  setSpeed();

  // Left wheels backward, right wheels forward
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);

  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}

void right() {
  setSpeed();

  // Left wheels forward, right wheels backward
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);

  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

void stopCar() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);

  analogWrite(ENA, 0);
  analogWrite(ENB, 0);
}

