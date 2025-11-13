# Gesture Controlled Robotic Arm 🤖

A robotic arm controlled by hand gestures using the **MPU6050 sensor** and **Arduino UNO**. The project detects the motion of the hand (forward, backward, left, right tilt) to control the arm’s base, shoulder, and elbow using servo motors.

---

## 🔧 Features
- Control robotic arm using hand gestures.
- Smooth progressive motion for shoulder and elbow joints.
- MPU6050 accelerometer + gyroscope based control.
- Toggle LED using a button.
- Real-time motion response through servo adjustments.

---

## 🧰 Components Used
- Arduino UNO  
- MPU6050 Accelerometer + Gyroscope  
- Servo Motors (3x)  
- Push Button  
- LED + Resistor  
- Jumper Wires  
- Power Supply / USB Cable  

---

## ⚙️ Working Principle
The MPU6050 mounted on a glove senses hand orientation. The Arduino reads accelerometer values (X, Y, Z) and interprets the tilt direction:
- **Front/Back tilt:** moves shoulder and elbow joints.  
- **Left/Right tilt:** rotates base.  
- **Button press:** toggles LED.  

Servo positions are updated progressively for smooth and realistic movement.

---

## 💻 Code Overview
```cpp
#include <Wire.h>
#include <Servo.h>

#define MPU_ADDR 0x68  // MPU6050 I2C address

// Servo pins
#define BASE_PIN 6
#define SHOULDER_PIN 5
#define ELBOW_PIN 3
#define LED_PIN 7
#define BUTTON_PIN 8

Servo baseServo;
Servo shoulderServo;
Servo elbowServo;

// Default positions
int basePos = 90;
int shoulderPos = 90;
int elbowPos = 90;

// Button state
bool ledState = false;




void setup() {
  Serial.begin(115200);



  Wire.begin();
  delay(100);


  // Wake up MPU6050
  Wire.beginTransmission(MPU_ADDR);
  Wire.write(0x6B);
  Wire.write(0);
  Wire.endTransmission(true);

  // Attach servos
  baseServo.attach(BASE_PIN);
  shoulderServo.attach(SHOULDER_PIN);
  elbowServo.attach(ELBOW_PIN);

  pinMode(LED_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT_PULLUP);

  digitalWrite(LED_PIN, LOW);
  baseServo.write(basePos);
  shoulderServo.write(shoulderPos);
  elbowServo.write(elbowPos);

  Serial.println("MPU6050 + Progressive Shoulder & Elbow Control (Front/Back Tilt) Ready!");
}

void loop() {
  int16_t ax, ay, az;




  // Read accelerometer
  Wire.beginTransmission(MPU_ADDR);
  Wire.write(0x3B);
  Wire.endTransmission(false);
  Wire.requestFrom(MPU_ADDR, 6, true);





  ax = Wire.read() << 8 | Wire.read();
  ay = Wire.read() << 8 | Wire.read();
  az = Wire.read() << 8 | Wire.read();

  float AccX = ax / 16384.0;
  float AccY = ay / 16384.0;
  float AccZ = az / 16384.0;

  Serial.print("X: "); Serial.print(AccX, 2);
  Serial.print(" | Y: "); Serial.print(AccY, 2);
  Serial.print(" | Z: "); Serial.println(AccZ, 2);

  // ------------------------
  // FRONT/BACK progressive control
  // ------------------------
  if (AccX > 0.25 && AccX <= 0.45) {
    // Slight tilt backward → move shoulder
    Serial.println("Slight Back → Shoulder moves back");
    shoulderPos = constrain(shoulderPos - 2, 60, 120);
  } 
  else if (AccX > 0.45) {
    // Strong tilt backward → move shoulder + elbow





    Serial.println("Strong Back → Shoulder + Elbow move back");
    shoulderPos = constrain(shoulderPos - 2, 60, 120);
    elbowPos = constrain(elbowPos - 2, 60, 120);
  } 
  else if (AccX < -0.25 && AccX >= -0.45) {
    // Slight tilt forward → move shoulder
    Serial.println("Slight Front → Shoulder moves forward");
    shoulderPos = constrain(shoulderPos + 2, 60, 120);
  } 





  else if (AccX < -0.45) {
    // Strong tilt forward → move shoulder + elbow
    Serial.println("Strong Front → Shoulder + Elbow move forward");
    shoulderPos = constrain(shoulderPos + 2, 60, 120);
    elbowPos = constrain(elbowPos + 2, 60, 120);
  }

  // ------------------------
  // LEFT / RIGHT control for Base
  // ------------------------
  if (AccY > 0.3) {
    Serial.println("Left");
    basePos = constrain(basePos + 2, 60, 120);
  } 
  else if (AccY < -0.3) {
    Serial.println("Right");
    basePos = constrain(basePos - 2, 60, 120);
  }








  // ------------------------
  // LED Button Toggle
  // ------------------------
  if (digitalRead(BUTTON_PIN) == LOW) {
    delay(200); // debounce
    ledState = !ledState;
    digitalWrite(LED_PIN, ledState ? HIGH : LOW);
  }


  // ------------------------
  // Apply servo positions
  // ------------------------
  baseServo.write(basePos);
  shoulderServo.write(shoulderPos);
  elbowServo.write(elbowPos);

  delay(200);
}
