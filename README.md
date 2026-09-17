# 🚧 ESP32 Automatic Conveyor Product Sorting System

**Mode:** Simulation Mode (Virtual Mode -- Method 1)\
**Platform:** ESP32\
**Interface:** Web Dashboard\
**Communication:** Wi-Fi + HTTP API

## 📌 Project Overview

This project simulates an **automatic conveyor belt system for sorting
products by color** using an ESP32.

It is designed for demonstrations and logic testing before the physical
hardware is available. The system reproduces the expected control flow
of a real conveyor sorting machine, including:

-   Product detection through a simulated IR sensor
-   Color identification through a simulated color sensor
-   Servo-based sorting logic
-   Product counting by color
-   State Machine transitions
-   Real-time monitoring through a Web Dashboard
-   Serial Monitor logging

Instead of controlling physical motors, sensors, and servos, the ESP32
prints simulated actions to the Serial Monitor and updates the
dashboard.

------------------------------------------------------------------------

## 🎯 Project Objectives

-   Understand how an embedded control system operates
-   Practice designing a **Finite State Machine**
-   Simulate sensor input without physical components
-   Test sorting logic before assembling hardware
-   Display system data through a browser-based dashboard
-   Prepare the software structure for future hardware integration

------------------------------------------------------------------------

## 🧠 System State Machine

The system uses four main states:

``` text
STANDBY
   |
   | Simulated object detected
   v
DETECTING
   |
   | Color detection completed
   v
SORTING
   |
   | Servo sorting completed
   v
RESET
   |
   | Conveyor stopped and servo reset
   v
STANDBY
```

### State Descriptions

  State               Description
  ------------------- ------------------------------------------------
  `STATE_STANDBY`     System is idle and ready for the next product
  `STATE_DETECTING`   Simulates IR detection and conveyor activation
  `STATE_SORTING`     Simulates color detection and servo sorting
  `STATE_RESET`       Resets the servo and stops the conveyor

------------------------------------------------------------------------

## ⚙️ Sorting Logic

The simulated system supports three color inputs:

  ------------------------------------------------------------------------
  Color                      Servo Angle Action           Counter
  ---------------- --------------------- ---------------- ----------------
  `RED`                             180° Sort product     Increases red
                                         into the red     count
                                         container        

  `GREEN`                             0° Sort product     Increases green
                                         into the green   count
                                         container        

  Other                              90° Hold servo in    No
                                         the middle       color-specific
                                         position         count
  ------------------------------------------------------------------------

Every detected product increases the total product counter.

### Example

``` text
Detected: RED
Servo: 180°
Red Count: +1
Total Count: +1
```

------------------------------------------------------------------------

## ⏱️ Simulation Timing

The program uses delays to simulate the time required for a product to
move through the system.

  ------------------------------------------------------------------------
  Step                                      Duration Simulated Action
  --------------------- ---------------------------- ---------------------
  Detection                                   700 ms Product moves from
                                                     the entrance to the
                                                     IR sensor

  Sorting                                     700 ms Product reaches the
                                                     color sensor and the
                                                     servo sorts it

  Reset                                       500 ms Servo returns to idle
                                                     and conveyor stops
  ------------------------------------------------------------------------

The browser animation uses approximately the same timing as the ESP32
simulation logic.

> **Note:** The current implementation uses `delay()`, which blocks the
> ESP32 while the simulation is running. This is acceptable for a basic
> demonstration, but a real hardware version should preferably use
> `millis()` for non-blocking control.

------------------------------------------------------------------------

## 🧩 Hardware and Software

### Current Simulation Version

No physical components are required.

-   ESP32 development board
-   Wi-Fi network
-   Computer or smartphone
-   Arduino IDE or Wokwi
-   Web browser
-   Serial Monitor

### Planned Physical Version

The simulation can later be connected to:

-   IR obstacle or break-beam sensor
-   Color sensor
-   DC motor and motor driver
-   Servo motor
-   Conveyor belt mechanism
-   Power supply
-   Product collection containers

------------------------------------------------------------------------

## 🌐 Web Dashboard

The ESP32 hosts a web dashboard that can be accessed through its local
IP address.

The dashboard displays:

-   Current system state
-   Conveyor status
-   Servo status
-   Red product count
-   Green product count
-   Total product count
-   Recent system logs
-   Animated conveyor model

### Dashboard Controls

  Button                 Function
  ---------------------- -----------------------------------------
  Simulate Object (IR)   Randomly selects either RED or GREEN
  Simulate Red           Directly tests the RED sorting branch
  Simulate Green         Directly tests the GREEN sorting branch

The dashboard prevents repeated button presses while an animation is
running.

------------------------------------------------------------------------

## 🔌 HTTP API

The ESP32 provides three main routes.

### `GET /`

Returns the HTML dashboard.

### `GET /simulate?color=RED`

Runs the simulated sorting process for the selected color.

Example:

``` text
http://ESP32_IP/simulate?color=RED
```

Supported values include:

``` text
RED
GREEN
```

### `GET /status`

Returns the current system status in JSON format.

Example response:

``` json
{
  "state": "Standby",
  "conveyor": "DUNG",
  "servo": "NGHỈ",
  "servoAngle": 180,
  "lastColor": "RED",
  "countRed": 1,
  "countGreen": 0,
  "countTotal": 1,
  "log": "System log..."
}
```

------------------------------------------------------------------------

## 🚀 How to Run

### 1. Configure Wi-Fi

Update the Wi-Fi credentials in the Arduino code:

``` cpp
const char* WIFI_SSID     = "YOUR_WIFI_NAME";
const char* WIFI_PASSWORD = "YOUR_WIFI_PASSWORD";
```

**Security recommendation:** Do not publish real Wi-Fi credentials in a
public repository.

### 2. Upload the Code

1.  Open the project in Arduino IDE or Wokwi.
2.  Select the appropriate ESP32 board.
3.  Install or enable the required libraries.
4.  Upload the code to the ESP32.

### 3. Open Serial Monitor

Set the baud rate to:

``` text
115200
```

The Serial Monitor will display the ESP32's local IP address after
connecting to Wi-Fi.

### 4. Open the Dashboard

Open the displayed IP address in a browser on a device connected to the
same Wi-Fi network.

Example:

``` text
http://192.168.1.100
```

### 5. Test the System

Use the dashboard buttons to simulate:

-   Object detection
-   Red product sorting
-   Green product sorting

Observe the state transitions, counters, animation, and Serial Monitor
logs.

------------------------------------------------------------------------

## 📁 Suggested Project Structure

``` text
ESP32-Conveyor-Sorting/
├── conveyor_sorting_simulation.ino
└── README.md
```

The current version stores the dashboard HTML, CSS, and JavaScript
directly inside the Arduino source code.

For a more advanced version, the web interface could be separated into
external files stored in ESP32 flash memory using LittleFS.

------------------------------------------------------------------------

## 🔄 Future Hardware Integration

When the physical circuit is available, the existing State Machine and
sorting logic can be retained.

The following changes will be required:

1.  Replace simulated IR detection with real sensor input.
2.  Replace simulated color detection with real sensor readings.
3.  Replace Serial Monitor servo messages with actual servo control.
4.  Replace simulated conveyor status with motor driver control.
5.  Add safety handling for sensor errors and mechanical jams.
6.  Replace blocking `delay()` calls with a non-blocking timing system.

Example of future servo integration:

``` cpp
// Simulation version
Serial.println("[SIMULATE] Servo moves to 180 degrees");

// Physical version
servo.write(180);
```

------------------------------------------------------------------------

## ⚠️ Current Limitations

-   The system is a simulation and does not control physical hardware.
-   `delay()` blocks the ESP32 during the sorting sequence.
-   There is no authentication for the web dashboard.
-   The system assumes that only one product is being processed at a
    time.
-   Color validation is basic and only explicitly handles `RED` and
    `GREEN`.
-   Wi-Fi credentials are currently stored in the source code.
-   The system does not include emergency-stop functionality.

------------------------------------------------------------------------

## 🧪 Learning Outcomes

This project demonstrates practical concepts in:

-   ESP32 programming
-   Wi-Fi connectivity
-   Embedded web servers
-   HTTP endpoints
-   JSON data formatting
-   JavaScript `fetch()`
-   Real-time dashboard polling
-   Finite State Machines
-   Sensor and actuator simulation
-   Automation and industrial control logic

------------------------------------------------------------------------

## 📜 License

This project is intended for educational demonstrations, prototyping,
and experimentation.
