# Traverse: A Mode-Switching Line Follower Car with Obstacle Sensing

Traverse is a small robotic car built on the **TM4C123GH6PM LaunchPad** (ARM Cortex-M4F), written in bare-metal C. It runs in two modes and switches between them on a Bluetooth command:

- **Autonomous mode:** follows a line with an IR sensor and stops when an obstacle is within 25 cm.
- **Manual mode:** driven from a smartphone over Bluetooth (HC-05).

The project was built at California State University, Fullerton, as a low-cost platform that sits between basic hobby kits and expensive autonomous vehicles.

📄 **Project report:** [Traverse report.pdf](Traverse%20report.pdf)

<!-- Add a photo or demo GIF of the car here, for example:
![Traverse car](docs/traverse.jpg) -->

---

## Features

- Two operating modes, switched at any time over Bluetooth
- Line detection with an IR sensor
- Obstacle detection with an HC-SR04 ultrasonic sensor, measured with a hardware timer in edge-capture mode
- Wireless control from a phone through an HC-05 module on UART5
- Status LEDs: **green** while the car is moving, **red** while it is stopped
- Status messages sent back to the phone (for example, `Switched to Manual Mode`)

## Hardware

| Component | Role |
|---|---|
| TM4C123GH6PM LaunchPad | Main controller (80 MHz Cortex-M4F, 256 KB flash, 32 KB SRAM) |
| HC-05 Bluetooth module | Wireless serial link to the phone (9600 baud) |
| L298N motor driver | Drives the two DC motors |
| 2 × DC motors | Wheel drive |
| IR sensor | Line detection |
| HC-SR04 ultrasonic sensor | Distance measurement (2–400 cm range) |
| Battery or power bank | Power supply |
| Smartphone | Runs the *Serial Bluetooth Terminal* app |

## Pin Connections

| Signal | TM4C123 pin | Connected to |
|---|---|---|
| Motor IN1 | PB0 | L298N IN1 |
| Motor IN2 | PB1 | L298N IN2 |
| Motor IN3 | PB2 | L298N IN3 |
| Motor IN4 | PB3 | L298N IN4 |
| Ultrasonic TRIG | PA4 | HC-SR04 Trig |
| Ultrasonic ECHO | PB6 (T0CCP0) | HC-SR04 Echo |
| IR sensor | PE0 | IR sensor output |
| Bluetooth RX | PC4 (U5RX) | HC-05 TX |
| Bluetooth TX | PC5 (U5TX) | HC-05 RX |
| Red LED | PF1 | On-board LED |
| Green LED | PF3 | On-board LED |

## Bluetooth Commands

Connect to the HC-05 from the *Serial Bluetooth Terminal* app and send one character:

| Command | Action |
|---|---|
| `A` | Switch to autonomous mode (default at power-on) |
| `M` | Switch to manual mode |
| `1` | Drive forward (manual mode only) |
| `2` | Stop (manual mode only) |

## How It Works

### Autonomous mode

On every pass through the main loop, the car:

1. Fires the ultrasonic sensor and times the echo pulse with Timer0.
2. Converts the pulse width to distance: `distance = (time × 34300 cm/s) / 2`.
3. Stops if an obstacle is **25 cm or closer**.
4. Otherwise reads the IR sensor: it drives forward while the line is detected and stops when the line is lost.

### Manual mode

1. The phone sends a single-character command.
2. The HC-05 passes it to the TM4C123 over UART5.
3. The firmware drives the motor pins and replies with a status message.

## Building and Flashing

1. Install **Keil µVision 5** and the **TM4C123 device pack** (Tiva C Series).
2. Open `UART_Echo.uvprojx`.
3. Build the project (**F7**).
4. Connect the LaunchPad over USB and flash it (**F8**) with the Stellaris ICDI debugger.
5. Pair your phone with the HC-05 (default PIN `1234` or `0000`) and send commands.

## Repository Structure

| File | Description |
|---|---|
| `main.c` | Application code: mode switching, motors, ultrasonic, IR, UART |
| `startup_TM4C123.s` | Startup code and vector table (vendor-provided) |
| `system_TM4C123.c` | System and clock setup (vendor-provided) |
| `RTE_Components.h` | Keil run-time environment configuration |
| `UART_Echo.uvprojx`, `UART_Echo.uvoptx` | Keil µVision project files |
| `Traverse report.pdf` | Full project report |

## Test Results

These figures come from the project report. They were measured on a short (70 cm) high-contrast test track.

| Metric | Result |
|---|---|
| Average speed | 7.78 cm/s (70 cm in 9 s) |
| Bluetooth command response | ~120 ms |
| Bluetooth range | 8–10 m |
| Line detection accuracy | ~90% |
| Obstacle detection range | 40–50 cm |
| Battery runtime | ~42 min |
| Average power consumption | ~1.8 W |

## Limitations

- **Short test track:** the 70 cm track shows basic functionality, not performance on long or complex courses.
- **Single IR sensor:** the car stops when it loses the line instead of steering back toward it.
- **On/off motor control:** the motors run at full speed or stop, with no PWM speed control.
- **Blocking distance reading:** if no echo returns, the distance measurement waits indefinitely.

## Future Work

- Add left, right and reverse movement
- Add PWM speed control
- Add an IR sensor array for proper steering
- Mount the ultrasonic sensor on an SG90 servo to scan left and right and steer around obstacles
- Add a timeout to the ultrasonic measurement

## Authors

- **Reshika Nimmala** ([@Dolly1002-source](https://github.com/Dolly1002-source))
- Vishva Gondalia
- Skanda Mandya Kumar

California State University, Fullerton



