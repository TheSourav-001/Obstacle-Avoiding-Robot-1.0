# Obstacle Avoiding Robot 🤖

![Arduino](https://img.shields.io/badge/Platform-Arduino-blue)
![License](https://img.shields.io/badge/License-MIT-green)

The Obstacle Avoiding Robot is an **autonomous mobile system** designed to navigate environments without human intervention. It efficiently detects obstacles using an **HC-SR04 ultrasonic sensor** mounted on a servo motor and changes its direction automatically to avoid collisions. Built with **Arduino** and the **L298N motor driver**, this is a practical project demonstrating **real-time sensor integration, control systems, and robotics fundamentals.**

---



##  Features

* **Fully Autonomous Movement:** Navigates the path and detects obstacles without external control.
* **Intelligent Decision-Making:** Compares distances to the left and right after encountering an obstacle to choose the path with the **greatest clearance**.
* **Dynamic Sensing:** Utilizes a servo motor to dynamically scan the environment (look left/right) for the next movement decision.
* **Modular Code:** Written with helper functions for clean, readable, and easily maintainable logic.

---

## 🛠️ Components & Setup

### Hardware Requirements

| Component | Quantity | Purpose |
| :--- | :--- | :--- |
| **Microcontroller** | 1 | Arduino UNO (or compatible board) |
| **Sensor** | 1 | HC-SR04 Ultrasonic Sensor |
| **Driver** | 1 | L298N Motor Driver Module |
| **Motor** | 2-4 | DC Geared Motors |
| **Actuator** | 1 | Servo Motor (for sensor scan) |
| **Base** | 1 | Chassis & Wheels |
| **Power** | 1 | Battery / Power Supply |

### Software & Libraries

* **Arduino IDE**
* **NewPing Library:** Essential for reliable ultrasonic distance measurements.
* **Servo Library:** Used to control the servo motor for scanning the environment.

---

##  Circuit Diagram & Assembly

The following images illustrate the physical connections and the robot's assembly.

![Circuit Diagram](263b224d-9320-4104-9cc7-fe69e9cfadb2.jpg)
![Project Picture](WhatsApp%20Image%202025-10-15%20at%2002.51.56_ddaf301f.jpg)

---

##  How to Run

1.  **Install Libraries:** Use the Arduino Library Manager to install the **`NewPing`** library. The `Servo` library is typically pre-installed.
2.  **Upload Code:** Open the provided Arduino code (`.ino` file), select the correct board and port, and upload it to the Arduino UNO.
3.  **Power On:** Place the robot on a flat, open surface and connect the battery to the L298N driver and Arduino.
4.  **Observe:** The robot will immediately begin moving forward, scanning its path, and autonomously avoiding obstacles when they are detected (at less than **30cm**).
5.  For more details, visit the [Tutorial Website](https://g.co/gemini/share/80349e0410ac).


---

##  Arduino Code Example

This is the full, functional code used to run the robot.

```c
#include <Servo.h>
#include <NewPing.h>

const int LeftMotorForward = 7;
const int LeftMotorBackward = 6;
const int RightMotorForward = 4;
const int RightMotorBackward = 5;

#define trig_pin A1
#define echo_pin A2

#define maximum_distance 200
boolean goesForward = false;
int distance = 100;

NewPing sonar(trig_pin, echo_pin, maximum_distance);
Servo servo_motor;

void setup(){
  pinMode(RightMotorForward, OUTPUT);
  pinMode(LeftMotorForward, OUTPUT);
  pinMode(LeftMotorBackward, OUTPUT);
  pinMode(RightMotorBackward, OUTPUT);
  
  servo_motor.attach(10);

  servo_motor.write(115);
  delay(2000);
  distance = readPing();
  delay(100);
  distance = readPing();
  delay(100);
  distance = readPing();
  delay(100);
  distance = readPing();
  delay(100);
}

void loop(){
  int distanceRight = 0;
  int distanceLeft = 0;
  delay(50);

  if (distance <= 30){
    moveStop();
    delay(300);
    moveBackward();
    delay(500);
    moveStop();
    delay(300);
    distanceRight = lookRight();
    delay(300);
    distanceLeft = lookLeft();
    delay(300);

    if (distance >= distanceLeft){
      turnRight();
      moveStop();
    }
    else{
      turnLeft();
      moveStop();
    }
  }
  else{
    moveForward(); 
  }
  distance = readPing();
}

int lookRight(){  
  servo_motor.write(50);
  delay(500);
  int distance = readPing();
  delay(100);
  servo_motor.write(115);
  return distance;
}

int lookLeft(){
  servo_motor.write(170);
  delay(500);
  int distance = readPing();
  delay(100);
  servo_motor.write(115);
  return distance;
  delay(100);
}

int readPing(){
  delay(70);
  int cm = sonar.ping_cm();
  if (cm==0){
    cm=250;
  }
  return cm;
}

void moveStop(){
  digitalWrite(RightMotorForward, LOW);
  digitalWrite(LeftMotorForward, LOW);
  digitalWrite(RightMotorBackward, LOW);
  digitalWrite(LeftMotorBackward, LOW);
  analogWrite(9,250);
  analogWrite(10,250);
}

void moveForward(){
  if(!goesForward){
    goesForward=true;
    
    digitalWrite(LeftMotorForward, HIGH);
    digitalWrite(RightMotorForward, HIGH);
  
    digitalWrite(LeftMotorBackward, LOW);
    digitalWrite(RightMotorBackward, LOW); 
    analogWrite(9,250);
    analogWrite(10,250);
  }
}

void moveBackward(){
  goesForward=false;

  digitalWrite(LeftMotorBackward, HIGH);
  digitalWrite(RightMotorBackward, HIGH);
  
  digitalWrite(LeftMotorForward, LOW);
  digitalWrite(RightMotorForward, LOW);
  analogWrite(9,150);
  analogWrite(10,150);
}

void turnRight(){
  digitalWrite(LeftMotorForward, HIGH);
  digitalWrite(RightMotorBackward, HIGH);
  
  digitalWrite(LeftMotorBackward, LOW);
  digitalWrite(RightMotorForward, LOW);
  
  delay(300);
  
  digitalWrite(LeftMotorForward, HIGH);
  digitalWrite(RightMotorForward, HIGH);
  
  digitalWrite(LeftMotorBackward, LOW);
  digitalWrite(RightMotorBackward, LOW);
}

void turnLeft(){
  digitalWrite(LeftMotorBackward, HIGH);
  digitalWrite(RightMotorForward, HIGH);
  
  digitalWrite(LeftMotorForward, LOW);
  digitalWrite(RightMotorBackward, LOW);

  delay(300);
  
  digitalWrite(LeftMotorForward, HIGH);
  digitalWrite(RightMotorForward, HIGH);
  
  digitalWrite(LeftMotorBackward, LOW);
  digitalWrite(RightMotorBackward, LOW);
}
