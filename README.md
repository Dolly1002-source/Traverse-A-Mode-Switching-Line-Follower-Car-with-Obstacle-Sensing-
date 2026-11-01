# Traverse: A Mode-Switching Line Follower Car with Obstacle Sensing

A dual-mode robotic car built around a **TM4C123GH6PM** (ARM Cortex-M4F) microcontroller. In **autonomous mode** it follows a line using infrared sensors and uses an ultrasonic sensor to detect obstacles ahead. In **manual mode** it is driven wirelessly over Bluetooth from a smartphone. The goal was a low-cost, flexible platform that sits between basic hobby kits and expensive autonomous vehicles.

> **Authors:** Vishva Gondalia, Reshika Nimmala, and Skanda Mandya Kumar
> California State University, Fullerton

📄 **Paper:** [Traverse_conference_paper.pdf](Traverse_conference_paper.pdf)

---

## Features

- **Two operating modes:** autonomous line following with obstacle sensing, and manual Bluetooth control
- **Line following** with an infrared sensor array at the front of the car
- **Obstacle detection** with an HC-SR04 ultrasonic sensor mounted on an SG90 servo that scans the path ahead
- **Motor control** using PWM for speed and direction (forward, reverse, left, right)
- **Wireless control** from a smartphone using an HC-05 Bluetooth module

## Hardware

| Component | Role |
|---|---|
| TM4C123GH6PM LaunchPad | Main controller (80 MHz Cortex-M4F, 256 KB flash, 32 KB SRAM, PWM and UART peripherals) |
| HC-05 Bluetooth module | Wireless serial link (Bluetooth 2.0 + EDR, SPP, 9600 baud default, about 10 m range) |
| L298N motor driver | Drives the two DC motors (5–35 V motor supply, up to 2 A per channel) |
| 2 × DC motors | Wheel drive |
| IR sensors | Line detection |
| HC-SR04 ultrasonic sensor | Distance measurement (2–400 cm range, 40 kHz) |
| SG90 servo | Rotates the ultrasonic sensor to scan left and right |
| Battery or power bank | Power supply |
| Smartphone | Manual control through a serial Bluetooth terminal app |
| Arduino Uno + joystick (optional) | Alternative wireless transmitter described in the paper |

> The paper names both the L298N and the L293D as the motor driver. The circuit description of the L298N is detailed, so check which one your build used.

## How It Works

### Manual mode

1. A smartphone running the **Serial Bluetooth Terminal** app sends a command when a button (M1, M2, M3, and so on) is pressed.
2. The car's HC-05 module receives the command and passes it to the TM4C123 over serial.
3. The microcontroller interprets the command and sets PWM and direction signals on the motor driver.
4. The car moves forward, backward, or stops.

### Autonomous mode

1. The IR sensor array reports the position of the black or white line as digital signals.
2. The TM4C123 adjusts motor speed and direction to stay aligned with the line.
3. The HC-SR04, mounted on the servo, measures the distance to objects ahead.
4. When an obstacle is detected, the car pauses to avoid it, then resumes line tracking.

Distance is computed from the echo time as `distance = (time × speed of sound) / 2`.

## Test Results

The car was tested in both modes, with serial output captured through PuTTY (autonomous mode) and the phone app log (manual mode).

| Metric | Reported value |
|---|---|
| Total distance travelled | 70 cm |
| Total time | 9 s |
| Average speed | 7.78 cm/s |
| Maximum speed | 10 cm/s |
| Bluetooth control response | 120 ms |
| Motor response time | < 150 ms |
| Bluetooth range | 8–10 m |
| Line detection accuracy | about 90% on a high-contrast track |
| Obstacle detection range | 40–50 cm |
| Battery runtime | 42 min |
| Average power consumption | 1.8 W |

Sample ultrasonic readings in autonomous mode included 40 cm, 39 cm, and 25 cm, while the IR array detected the line continuously.

The paper also compares the car with two commercial kits (a Bluetooth-only car kit and a 4WD kit with ultrasonic and IR sensing) on control modes, sensing, runtime, and cost.

## Limitations

- The test track was short (70 cm), so the results show basic functionality rather than performance over long or complex courses.
- Several figures in the performance table are stated as specifications or estimates and are not backed by detailed test procedures in the paper.
- The HC-05 is a classic Bluetooth module, so power comparisons with Wi-Fi or Bluetooth Low Energy do not apply to the measured hardware.



## Authors

- **Reshika Nimmala** — [@Dolly1002-source](https://github.com/Dolly1002-source)
- Vishva Gondalia
- Skanda Mandya Kumar
