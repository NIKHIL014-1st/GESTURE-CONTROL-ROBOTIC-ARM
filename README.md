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

