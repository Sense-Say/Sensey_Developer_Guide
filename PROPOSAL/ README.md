

### Deep Dive: Project 1 - The "Smart Palengke" Cold-Chain & Spoilage Predictor

This project is a massive evolution of a basic "smart thermometer." By adding gas detection (ammonia from rotting meat) and a mechanical failsafe, you elevate it to an industrial-grade project.

#### 1. The Wiring Guide (ESP32-C3 Supermini)
*   **Input 1: DS18B20 Temp Probe (Digital 1-Wire)** -> Connect to **Pin 3** (Requires a 4.7k resistor between Data and 3.3V).
*   **Input 2: MQ-135 Gas Sensor (Analog)** -> Connect to **Pin 0** (ADC).
*   **Input 3: LDR Photoresistor (Analog)** -> Connect to **Pin 1** (ADC).
*   **Output 1: SG90 Servo Motor (PWM)** -> Connect to **Pin 2**.
*   **Output 2: WS2812B RGB LED** -> **Built-in (Pin 8)**.

#### 2. The Physical Prop for the Classroom Demo
*   **The Cooler:** Buy a small styrofoam cooler (the tiny ones used for ice cream or medical supplies). 
*   **The Setup:** Tape the Servo Motor to the hinge of the cooler so that when the servo arm rotates, it physically pushes the lid shut.
*   **The Demonstration:** Put a piece of wet sponge inside (to represent meat). Run a hairdryer lightly into the cooler. The class will watch the temperature rise on the projector dashboard. Suddenly, the MQ-135 sensor will detect the hot air, the RGB LED will flash Red, and the Servo will violently snap the styrofoam lid shut to "save the meat."

#### 3. Exact GitHub / MicroPython Libraries to Use
*   **Temperature:** You do not need a GitHub repo! MicroPython has this built-in. You just import `ds18x20` and `onewire`.
*   **Gas & Light:** You use standard MicroPython ADC (Analog to Digital) code. `machine.ADC(Pin(0))` for the gas sensor, and `Pin(1)` for the LDR.
*   **GUI Dashboard:** Use **Blynk IoT** ([vshymanskyy/blynk-library-python](https://github.com/vshymanskyy/blynk-library-python) on GitHub). Blynk lets you create a massive digital clock widget on your phone/projector that shows "ESTIMATED SPOILAGE TIME."

#### 4. The Logic (How to program it)
```python
# Simple explanation of your main loop logic:
if temperature > 10.0 and lid_is_open (LDR detects light):
    start_spoilage_countdown()
    led.color = YELLOW
    
if temperature > 15.0 or gas_level > 500:
    # Meat is rotting!
    led.color = RED
    servo.angle = 90 # Snaps the cooler lid shut automatically!
    blynk.send_notification("WARNING: Spoilage Detected!")
```

---

### Deep Dive: Project 3 - The "Tricycle/Jeepney" Overload & Incline Failsafe

This project is visually stunning because it involves a moving vehicle prop. It combines weight data and 3D gyroscope data, making it look like a highly advanced automotive engineering project.

#### 1. The Wiring Guide (ESP32-C3 Supermini)
*   **Input 1: HX711 Load Cell (Digital)** -> Data to **Pin 0**, Clock to **Pin 1**.
*   **Input 2: MPU6050 Gyroscope (I2C)** -> SDA to **Pin 4**, SCL to **Pin 5**.
*   **Input 3: Push Button / Limit Switch (Digital)** -> Connect to **Pin 2** (Acts as the driver's brake pedal).
*   **Output 1: SG90 Servo Motor (PWM)** -> Connect to **Pin 3**.
*   **Output 2: WS2812B RGB LED** -> **Built-in (Pin 8)**.

#### 2. The Physical Prop for the Classroom Demo
*   **The Vehicle:** Get a small toy truck or a flat piece of wood with wheels. 
*   **The Setup:** Mount the **Load Cell (HX711)** under the bed of the truck so you can place heavy books on it. Tape the **MPU6050** flat to the truck. Mount the **Servo Motor** near the back wheel.
*   **The Demonstration:** Place the truck on a plank of wood (your "road"). Stack a heavy book on the truck. The dashboard will show "OVERLOADED: 150%". Then, physically lift one end of the plank of wood to create a steep hill. The MPU6050 will detect the steep incline. Because the truck is *both* overloaded and on a steep hill, the Servo Motor will snap an arm directly into the wheel spokes, physically locking the tires, and the LED will flash rapidly to warn other cars.

#### 3. Exact GitHub / MicroPython Libraries to Use
*   **Weight Scale (HX711):** Go to GitHub and search for `SergeyPiskunov/micropython-hx711`. This library makes it incredibly easy to read grams/kilograms in MicroPython.
*   **Tilt Sensor (MPU6050):** Go to GitHub and search for `adamjezek98/MPU6050-ESP8266-MicroPython`. (It says ESP8266, but it works flawlessly on the ESP32-C3).
*   **GUI Dashboard:** Use **ThingSpeak**. ThingSpeak is perfect for this because it natively draws line-graphs. You can show one line for "Vehicle Weight" and another line for "Road Incline Angle."

#### 4. The Logic (How to program it)
```python
# Simple explanation of your main loop logic:
vehicle_weight = hx711.read_weight()
hill_angle = mpu.read_pitch_angle()

if vehicle_weight > MAX_SAFE_WEIGHT:
    led.color = YELLOW # Warning
    
if vehicle_weight > MAX_SAFE_WEIGHT and hill_angle > 20:
    # Truck is too heavy for this steep hill! Brake failure imminent!
    led.color = RED
    servo.angle = 180 # Deploys the emergency mechanical brake into the wheel
```

---

### How to Present This to Your Teacher Tomorrow

To get these projects approved, you need to hand your teacher a professional-sounding proposal. Choose the one you want to build and submit this text:

#### Proposal Option A (If you choose the Palengke project):
> **Project Title:** IoT Cold-Chain Compliance & Biomarker Spoilage Predictor
> **Hardware:** ESP32-C3 Supermini (MicroPython v1.22)
> 
> **Overview:** To address food security and financial loss in local wet markets during power outages, I am developing an automated cooler failsafe. The system uses an ESP32 to monitor internal cooler temperatures (DS18B20) and detect early-stage ammonia gas release from rotting meat (MQ-135). It ensures compliance by monitoring light levels (LDR) to detect if the cooler was left open. If a critical thermal or gas threshold is breached, the microcontroller dynamically triggers a Servo actuator to forcefully close the lid, preserving the cold-chain. Data is pushed synchronously to a cloud dashboard for vendor monitoring.

#### Proposal Option B (If you choose the Tricycle/Jeepney project):
> **Project Title:** IoT Kinetic Failsafe & Load Distribution Telemetry System
> **Hardware:** ESP32-C3 Supermini (MicroPython v1.22)
> 
> **Overview:** Overloaded PUVs (Public Utility Vehicles) on steep inclines represent a major hazard in the Philippines. I am developing an edge-computing safety interlock. The system utilizes an HX711 Load Cell to calculate real-time cargo mass and an I2C MPU6050 Gyroscope to map the vehicle's spatial incline. The system processes this dual-axis data matrix; if algorithmic safety tolerances are exceeded (e.g., severe overload on a steep downward pitch), it autonomously deploys an electro-mechanical Servo braking failsafe and activates high-visibility LED warnings, mitigating runaway accidents.

Both of these projects are absolute winners. They look like university thesis projects, but because you are using **MicroPython**, you can write the code in less than 100 lines!

---
---
---
---

Since the ESP32-C3 Supermini has a limited number of pins, we will use the **I2C bus** (SDA/SCL) to connect multiple devices (OLED and Sensors) to the same two pins. This is a very "high-tech" way to wire a project.

Here are the **Redone Blueprints** for the Smart Palengke and the PUV Safety System.

---

### Project 1: The "Smart Palengke" Industrial Cold-Chain Monitor
**The Problem:** Preventing food spoilage in wet markets by monitoring internal meat health, ambient conditions, and gas release.

#### **5 INPUTS (Data Collection):**
1.  **DS18B20 Waterproof Probe:** Measures the internal core temperature of the meat/fish.
2.  **MQ-135 Gas Sensor:** Detects Ammonia and VOCs (the "smell" of rotting protein).
3.  **LDR Photoresistor:** Detects if the cooler lid is open (sensing ambient light).
4.  **DHT11 Sensor:** Measures the humidity and temperature *outside* the meat (ambient air).
5.  **TTP223 Touch Sensor:** A touch-sensitive button for the vendor to "log" when they added fresh ice.

#### **5 OUTPUTS (Actuators & Visuals):**
1.  **SG90 Servo Motor:** Automatically pushes the cooler lid shut if spoilage is detected.
2.  **5V Relay Module:** Turns on a backup cooling fan or a misting system.
3.  **Active Piezo Buzzer:** Sounds a "Spoilage Alert" if Ammonia levels cross the threshold.
4.  **0.96" I2C OLED Display:** Shows live data (Temp, Gas Level, Humidity) on the cooler.
5.  **Built-in WS2812B RGB LED:** Changes color based on meat health (Green=Safe, Yellow=Caution, Red=Rotten).

---

### Project 3: The "PUV Safety & Kinetic Failsafe" System
**The Problem:** Preventing tricycle/jeepney accidents caused by overloading, steep inclines, and driver fatigue.

#### **5 INPUTS (Data Collection):**
1.  **HX711 + Load Cell:** Measures the actual weight (mass) of the passengers/cargo.
2.  **MPU6050 Gyroscope (I2C):** Measures the road incline (pitch/tilt) in degrees.
3.  **HC-SR04 Ultrasonic Sensor:** Measures distance from the vehicle behind (proximity alert).
4.  **Potentiometer Dial:** A physical knob for the driver to set the "Weight Limit" based on the vehicle type.
5.  **Limit Switch:** Attached to the seatbelt or brake pedal to verify "Safety Checks" are done.

#### **5 OUTPUTS (Actuators & Visuals):**
1.  **SG90 Servo Motor:** Deploys a mechanical "Emergency Brake" arm into the wheel.
2.  **5V Relay Module:** Simulates an "Ignition Kill" (cuts power to a motor) during a critical hazard.
3.  **Active Piezo Buzzer:** Beeps faster as the vehicle gets closer to an object behind it (Reverse Sensor).
4.  **0.96" I2C OLED Display:** A "Digital Dashboard" showing Speed, Weight, and Incline Angle.
5.  **Built-in WS2812B RGB LED:** Hazard indicator (Flashes Red/Blue like an emergency vehicle during a hazard).

---

### **Supermini Wiring Logic (How to fit 10 I/O on one small board)**

The Supermini has exactly enough pins if you use **I2C**. Pins **4 (SDA)** and **5 (SCL)** will be shared by the **OLED** and the **MPU6050/Sensors**.

| Component Type | Project 1 (Palengke) | Project 3 (PUV Safety) | Supermini Pin |
| :--- | :--- | :--- | :--- |
| **Input 1** | DS18B20 | HX711 (Data) | GPIO 0 |
| **Input 2** | MQ-135 (Analog) | HX711 (Clock) | GPIO 1 |
| **Input 3** | LDR (Analog) | Potentiometer (Analog) | GPIO 2 |
| **Input 4** | DHT11 | HC-SR04 (Echo) | GPIO 3 |
| **Input 5** | Touch Sensor | Limit Switch | GPIO 10 |
| **I2C Bus** | **OLED Display** | **OLED & MPU6050** | **GPIO 4 & 5** |
| **Output 1** | Built-in RGB LED | Built-in RGB LED | **GPIO 8** |
| **Output 2** | SG90 Servo | SG90 Servo | GPIO 6 |
| **Output 3** | 5V Relay | 5V Relay | GPIO 7 |
| **Output 4** | Active Buzzer | Active Buzzer | GPIO 9 |
| **Output 5** | OLED (Part of I2C) | OLED (Part of I2C) | Shared 4 & 5 |

---

### **The "Surprise Factor" Classroom Demo**

#### **For Project 3 (PUV Safety):**
1.  Show the **OLED screen** to the class. It says "Ready to Drive."
2.  Place a weight on the truck. The **OLED** updates: "Weight: 500g (OK)."
3.  Add a second weight. The **built-in LED** turns Yellow. "Weight: 1.2kg (OVERLOAD)."
4.  Tilt the truck's ramp up. The **MPU6050** sees the steep angle. 
5.  **The Surprise:** Suddenly, the **Buzzer** screams, the **LED** flashes Red/Blue, the **Relay** clicks (Engine Kill), and the **Servo** slams the brake down. 
6.  The **GUI Dashboard** on the projector plots the moment the "Accident was Prevented" on a live graph.

---

### **MicroPython GitHub Resources to Use**

To build this, download these specific files and upload them to your Supermini using **Thonny IDE**:

1.  **For the OLED (Output 4/5):** Search GitHub for `stlewis/micropython-ssd1306`. (Essential for the GUI display).
2.  **For the Weight Scale (Input 1):** Search GitHub for `SergeyPiskunov/micropython-hx711`.
3.  **For the Gyro (Input 2):** Search GitHub for `adamjezek98/MPU6050-ESP8266-MicroPython`.
4.  **For the Dashboard:** Use the **Microdot** library (`miguelgrinberg/microdot`). This allows you to create a web page *inside* the ESP32 that your teacher can visit on their phone.

### **Final Teacher Proposal (Submit this for Approval)**

> **Project Title:** Multi-Sensor IoT Kinetic Safety & Failsafe Matrix (PUV Edition)
> **Platform:** ESP32-C3 Supermini (MicroPython)
> **Requirement Check:** 5 Distinct Inputs, 5 Distinct Outputs.
>
> **Technical Logic:** 
> This project demonstrates high-density sensor fusion. It utilizes a **Load Cell (Input 1)** and **Gyroscope (Input 2)** to calculate the center of gravity and safety threshold of a transport vehicle. It incorporates **Proximity (Input 3)**, **User calibration (Input 4)**, and **Mechanical interlocks (Input 5)**. 
>
> The system responds through 5 autonomous outputs: A mechanical **Emergency Brake (Output 1)**, an **Ignition Kill-switch (Output 2)**, an **Acoustic Warning (Output 3)**, a **Real-time Telemetry OLED (Output 4)**, and an **RGB Visual Status Matrix (Output 5)**. 
>
> Data is streamed via WebSockets to a **Microdot-hosted GUI Dashboard**, providing live visualization of the vehicle's safety state. This solves the Philippine social problem of PUV accidents caused by overloading and steep-terrain brake failure.

This project is very "busy"—lots of wires, moving parts, and a screen. It will look significantly more advanced than the other students' projects.

---
---
---
---
## **DETAILED EXPLANATION**
---
This is a professional, in-depth technical proposal for **Project 1: The "Smart Palengke" Industrial Cold-Chain Monitor**. 

This project is designed to be a "System-on-a-Box" that transforms a standard styrofoam cooler into an intelligent, IoT-connected medical/food-grade storage unit. It specifically addresses the **"Cold Chain" problem** in the Philippines—where the gap between the fisherman/farmer and the consumer is often where food goes bad due to tropical heat.

---

### **Part 1: In-Depth Project Explanation**

**The Technical Concept:**
The "Smart Palengke" system moves away from simple temperature monitoring and moves into **Multi-Modal Biosensing**. Instead of just asking "Is it hot?", the system asks "Is the biological matter actually decaying?" 

By using five distinct sensors (Inputs), the ESP32-C3 Supermini creates a "Safety Profile" for the stored food. It uses **Edge Computing** (processing the data locally on the chip) to make split-second decisions—like slamming a lid shut or sounding an alarm—while simultaneously using **Asynchronous Web-Server protocols** to send that data to a GUI dashboard. This ensures that even if the Wi-Fi drops, the meat is still protected by the local hardware logic.

---

### **Part 2: In-Depth Discussion of the 5 Inputs**

Each input has been selected to provide a different "dimension" of data.

1.  **DS18B20 Waterproof Probe (The Core Biometric):**
    *   *Use:* This is a "One-Wire" digital sensor. Unlike standard sensors, it is encased in stainless steel and is waterproof. It must be physically inserted into the ice or placed in direct contact with the meat.
    *   *Technical Importance:* It provides the **Internal Temperature**. Ambient air temperature fluctuates, but the core temperature of the food is the only thing that matters for safety. It is accurate to ±0.5°C.

2.  **MQ-135 Gas Sensor (The Spoilage Detector):**
    *   *Use:* This sensor detects Ammonia (NH3), Alcohol, and Benzene. When protein (fish or meat) begins to rot, it releases Ammonia gas *before* the human nose can even smell it.
    *   *Technical Importance:* This acts as a **Chemical Early Warning**. If the temperature is low but Ammonia levels are rising, it indicates a bacterial contamination issue regardless of the coldness.

3.  **LDR Photoresistor (The Compliance Monitor):**
    *   *Use:* Placed inside the cooler lid. When the lid is closed, it’s dark (High Resistance). When opened, it senses light (Low Resistance).
    *   *Technical Importance:* This tracks **Human Error**. Every time a vendor opens the lid to show a customer the fish, cold air escapes. The system logs how long the lid was left open, providing a "Compliance Score" for the day.

4.  **DHT11/DHT22 Sensor (The Environmental Context):**
    *   *Use:* Measures the humidity and temperature of the *outside* market air.
    *   *Technical Importance:* It calculates the **Thermal Stress Level**. If the outside humidity is 90% and the temperature is 35°C (standard Philippine weather), the ice will melt 3x faster. The system uses this to predict when the vendor needs to buy more ice.

5.  **TTP223 Capacitive Touch Sensor (The HMI Interrupt):**
    *   *Use:* A "No-Moving-Parts" button hidden under the plastic/styrofoam.
    *   *Technical Importance:* It acts as a **Maintenance Logger**. When the vendor adds new ice, they tap this sensor. The system records the timestamp, resetting the "Ice Life" algorithm on the dashboard.

---

### **Part 3: In-Depth Discussion of the 5 Outputs**

The outputs allow the system to "talk back" and take physical action.

1.  **SG90 Servo Motor (The Mechanical Actuator):**
    *   *Use:* A high-torque mini motor connected to a 3D-printed or cardboard arm.
    *   *Technical Importance:* **Automated Mitigation**. If the LDR (Input 3) detects the lid has been open for more than 60 seconds, or if the MQ-135 (Input 2) detects gas, the Servo physically pushes the lid closed. This is the "Surprise Factor" for your presentation.

2.  **5V Relay Module (The Cooling Override):**
    *   *Use:* An electromagnetic switch that controls a secondary 5V Fan or a DC Water Pump.
    *   *Technical Importance:* **Active Cooling**. While the cooler relies on ice (Passive), the Relay can trigger a small fan to circulate the cold air or a pump to drain excess melted water, keeping the meat from sitting in "dirty" water.

3.  **Active Piezo Buzzer (The Auditory Alert):**
    *   *Use:* A 5V buzzer that produces a sharp 2kHz tone.
    *   *Technical Importance:* **Immediate Local Feedback**. It uses different "Beep Patterns." One beep for "Ice getting low," and a continuous siren for "Meat Spoilage Detected."

4.  **0.96" SSD1306 OLED Display (The Local GUI):**
    *   *Use:* A 128x64 pixel screen using the I2C protocol.
    *   *Technical Importance:* **Real-Time Telemetry**. It allows the vendor to see the "Health Score" of their product without needing to check their phone. It makes the device look like a professional industrial tool.

5.  **Built-in WS2812B RGB LED (The Visual Status Matrix):**
    *   *Use:* An addressable LED located directly on the Supermini (Pin 8).
    *   *Technical Importance:* **Ambient Notification**. Since it's very bright, it can be seen from across the market.
        *   *Green:* All systems optimal.
        *   *Yellow Pulse:* Warning (Lid open or Temp rising).
        *   *Red Flash:* Critical Spoilage/Ice Melted.

---

### **Part 4: The IoT GUI Dashboard (Visualization)**

Using **Microdot (MicroPython)**, the ESP32-C3 will host a website on its own IP address. When you (or your teacher) connect to the ESP32's Wi-Fi, you see:

*   **A Live Gauge:** Showing the "Core Temperature."
*   **A Status Indicator:** "MEAT HEALTH: 98%" (This percentage drops as Ammonia levels rise).
*   **A Button:** To remotely "Lock/Unlock" the Servo motor lid.
*   **A History Graph:** Showing the temperature fluctuations of the last 10 minutes.

---

### **Technical Pin-Mapping (For your records):**

| Component | Pin (GPIO) | Protocol |
| :--- | :--- | :--- |
| **DS18B20** | 0 | 1-Wire |
| **MQ-135** | 1 | Analog (ADC) |
| **LDR** | 2 | Analog (ADC) |
| **DHT11** | 3 | Digital (Single-Bus) |
| **Touch Sensor**| 10 | Digital Input |
| **Servo** | 6 | PWM |
| **Relay** | 7 | Digital Out |
| **Buzzer** | 9 | Digital Out (or PWM) |
| **OLED** | 4 (SDA) / 5 (SCL)| I2C |
| **RGB LED** | 8 | WS2812B Protocol |

---
---
---
---

This is the professional, in-depth technical proposal for **Project 3: The "PUV Safety & Kinetic Failsafe" Matrix**.

This project is a high-tech "Safety Black Box" designed for Public Utility Vehicles (PUVs) like Jeepneys and Tricycles. It specifically targets the dangerous combination of **overloading** and **steep Philippine terrain** (like the roads in Baguio, Antipolo, or Tagaytay). It transforms a vehicle into a "smart machine" that can sense its own weight and the angle of the road to prevent catastrophic brake failure.

---

### **Part 1: In-Depth Project Explanation**

**The Technical Concept:**
The "PUV Safety Matrix" utilizes **Sensor Fusion**. It doesn't look at data points in isolation; it looks at the *relationship* between them. For example: 
*   1,000kg of weight on a flat road is **Safe**.
*   1,000kg of weight on a 30-degree downhill incline is **Critical**.

The ESP32-C3 Supermini acts as the "Electronic Control Unit" (ECU). It calculates the **Kinetic Danger Level** in real-time. If the vehicle is overloaded beyond its mechanical limit while on a steep slope, the system realizes that human brakes might fail. It then triggers an **Autonomous Failsafe**—physically locking the wheels or cutting the engine power before an accident can happen.

---

### **Part 2: In-Depth Discussion of the 5 Inputs**

1.  **HX711 Load Cell Amplifier (The Mass Meter):**
    *   *Use:* The Load Cell is placed under the vehicle's chassis or passenger bench. The HX711 converts the tiny electrical changes caused by weight into a 24-bit digital signal.
    *   *Technical Importance:* It provides the **Live Payload Data**. This is the only way to mathematically prove the vehicle is "overloaded" rather than just guessing based on the number of people.

2.  **MPU6050 6-Axis Gyro/Accelerometer (The Inclinometer):**
    *   *Use:* This I2C sensor measures "Pitch" (the front-to-back tilt of the vehicle).
    *   *Technical Importance:* It provides **Spatial Awareness**. It tells the ESP32 if the vehicle is currently climbing a mountain or descending a steep hill. This is critical because the heavier a vehicle is, the more gravity pulls it down a slope, increasing the force required to stop.

3.  **HC-SR04 Ultrasonic Sensor (The Proximity Radar):**
    *   *Use:* Mounted on the rear bumper, it sends out "pings" of sound to measure the distance to objects behind the vehicle.
    *   *Technical Importance:* It acts as an **Active Reverse Guard**. Many PUV accidents happen during reversing in crowded terminals. The system integrates this distance data into the dashboard for the driver.

4.  **Analog Potentiometer (The Threshold Tuner):**
    *   *Use:* A physical knob on the driver's dashboard.
    *   *Technical Importance:* It allows for **Dynamic Calibration**. Different vehicles (e.g., a small tricycle vs. a larger jeepney) have different weight capacities. The driver can turn this knob to set the "Maximum Capacity" for that specific vehicle, which the ESP32 then uses as the limit in its safety calculations.

5.  **Limit Switch / Mechanical Button (The Brake/Belt Interlock):**
    *   *Use:* A heavy-duty click switch mounted behind the brake pedal or on the seatbelt buckle.
    *   *Technical Importance:* It provides **Mechanical Verification**. The system won't allow the vehicle to "start" (via the relay) unless the limit switch confirms the driver has performed a safety check (e.g., foot on the brake or seatbelt clicked).

---

### **Part 3: In-Depth Discussion of the 5 Outputs**

1.  **SG90 Servo Motor (The Mechanical Brake Failsafe):**
    *   *Use:* A high-precision motor that rotates 180 degrees.
    *   *Technical Importance:* **Emergency Actuation**. In the event of a "Critical Overload + Steep Slope" scenario, the Servo deploys a mechanical block or an emergency lever. This is the "Hero" of your presentation—it shows the system taking physical control to save lives.

2.  **5V Relay Module (The Ignition/Power Kill):**
    *   *Use:* An electromagnetic switch capable of cutting a high-voltage circuit.
    *   *Technical Importance:* **Power Intervention**. If a driver attempts to drive an overloaded vehicle, the Relay stays "Open," meaning the engine or motor cannot start. It acts as a "Gatekeeper" for safety.

3.  **Active Piezo Buzzer (The Hazard Audio):**
    *   *Use:* A sound generator that changes frequency.
    *   *Technical Importance:* **Multi-Level Alerting**. 
        *   *Slow Beeps:* Vehicle is reversing.
        *   *Fast Beeps:* Vehicle is close to an obstacle.
        *   *Constant Tone:* CRITICAL DANGER / BRAKE OVERLOAD.

4.  **0.96" SSD1306 OLED Display (The Digital Dashboard):**
    *   *Use:* A high-contrast screen that uses the I2C protocol.
    *   *Technical Importance:* **Driver Visualization**. It replaces old analog gauges with a digital readout of: `WT: 1200kg`, `ANG: 22°`, `STAT: SAFE`. This makes the project look "High-End" and modern.

5.  **Built-in WS2812B RGB LED (The Visual Warning Matrix):**
    *   *Use:* The addressable LED on the Supermini (Pin 8).
    *   *Technical Importance:* **External Hazard Signaling**.
        *   *Blue:* Normal operation.
        *   *Yellow:* Approaching weight limit.
        *   *Flashing Red/Blue:* Emergency Failsafe Deployed (Warning other drivers on the road).

---

### **Part 4: The IoT GUI Dashboard (Visualization)**

Using **Microdot** and **WebSockets** in MicroPython, the ESP32-C3 hosts a mobile-friendly dashboard. During your presentation, you can open this on your phone and mirror it to the projector:

*   **Weight Gauge:** A needle that moves in real-time as you add weight to the Load Cell.
*   **Incline Horizon:** A graphic that tilts left and right as you tilt the vehicle prop.
*   **Safety Log:** A text box that says: *"10:15 AM - Overload detected; Failsafe engaged."*
*   **Remote Override:** A button on the website that allows a "Fleet Manager" to remotely unlock the vehicle's engine.

---

### **Technical Pin-Mapping (ESP32-C3 Supermini):**

| Component | Pin (GPIO) | Protocol |
| :--- | :--- | :--- |
| **HX711 (Weight)** | 0 (Data) / 1 (CLK) | Serial (2-Wire) |
| **Potentiometer** | 2 | Analog (ADC) |
| **HC-SR04 (Radar)** | 3 (Echo) / 10 (Trig) | Digital Pulse |
| **OLED & MPU6050** | 4 (SDA) / 5 (SCL) | **I2C Bus (Shared)** |
| **Servo (Brake)** | 6 | PWM |
| **Relay (Ignition)**| 7 | Digital Out |
| **Buzzer** | 9 | Digital Out / PWM |
| **RGB LED** | 8 | WS2812B |
| **Limit Switch** | 20 / 21 (Internal) | Digital Input |

**Note on Wiring:** Using Pin 4 and 5 for the **I2C Bus** is the "Secret Sauce." It allows you to run both the OLED screen and the Gyroscope on just 2 pins, saving the rest of the pins for your other sensors.

---

### **Final Project Comparison for Teacher Approval:**

*   **Project 1 (Palengke):** Focuses on **Biomedical/Chemical** safety (Rotten food/Gases). It is "Stationary" IoT.
*   **Project 3 (PUV Safety):** Focuses on **Kinetic/Mechanical** safety (Physics/Motion). It is "Mobile" IoT.
