# Chicken Coop Door Sensor Documentation

## 1. Introduction

### Overview of the System

The chicken coop door sensor system is designed to monitor the status of a chicken coop door, ensuring it is securely
closed at night and open during the day. This system uses a passive sensor setup involving an ESP32 controller and an
infrared slotted optical optocoupler to detect the door's position. It communicates the door status via text messages
and a visual LED indicator.

### Purpose and Benefits

The primary purpose of this system is to enhance the safety and security of chickens by ensuring the coop door is
properly managed without modifying the door mechanism or the chickens themselves. The benefits of this system include:

- Real-time notifications of door status through text messages.
- Visual indication of door status via an LED light, providing immediate feedback.
- Automated monitoring and alerts to prevent door closure or opening failures.
- Minimal intervention required for installation and maintenance.

### System Components

The system comprises several key components:

- **ESP32 Controller:** Acts as the central processing unit, reading sensor data and sending status messages.
- **Infrared Slotted Optical Optocoupler:** Detects the open or closed state of the chicken coop door.
- **10mm LED Bulb:** Provides a visual indication of the door status (red for open, green for closed).
- **Amazon Web Services (AWS):** Handles the logic and text messaging for status notifications.

## 2. System Requirements

### Hardware Requirements

- ESP32 (controller)
- Infrared slotted optical optocoupler (sensor)
- 10mm LED bulb
- Power supply for the ESP32 and LED

### Software Requirements

- Micropython firmware for ESP32
- AWS account with access to API Gateway, Lambda functions, CloudWatch, and SNS (Simple Notification Service)
- Code editor for writing and uploading code to the ESP32

### AWS Account Setup

- Create an AWS account if you don’t have one.
- Set up the required services: API Gateway, Lambda, CloudWatch, and SNS.
- Configure necessary permissions and roles for the Lambda functions.

## 3. Parts List

### Main Components

- **[ESP32-WROOM-32D Microcontroller](https://a.co/d/9vEc9dk)**
    - Specifications
        - **Processor**: Dual-core Xtensa® 32-bit LX6 microprocessor, up to 240 MHz
        - **Memory**:
            - 448 KB ROM
            - 520 KB SRAM
            - 16 KB SRAM in RTC
        - **Flash**: 4 MB embedded SPI flash
        - **Operating Voltage**: 2.2V to 3.6V
        - **Power Consumption**:
            - Active mode: 160-240 mA
            - Deep sleep mode: 10 µA
            - Power down mode: 5 µA
        - **Wireless Connectivity**:
            - **Wi-Fi**: 802.11 b/g/n, supports WPA/WPA2/WPA 3 <span style="color:red">(NOTE: This supports 2.4GHz
              ONLY)</span>
            - **Bluetooth**: v4.2 BR/EDR and BLE (Bluetooth Low Energy)- **Infrared Slotted Optical Optocoupler (Sensor)
              **

- **[LED Indicator](https://a.co/d/7vtlXnA)**
    - Specifications
        - Lens: 10mm Diameter / Frosted / Round
        - Emitting Color: RGB (Common Cathode)
        - Luminous Intensity: R:1000-2000mcd G:4000-5000mcd B:3000-4000mcd
        - Viewing Angle: 120 Degree
        - Forward Voltage / Current: R:2V-2.2V G:3V-3.2V B:3V-3.2V | 20mA (each color)

- **[Infrared Slotted Optical Optocoupler](https://a.co/d/bjWC8yz)**
    - Specifications
        - IR Infrared Slotted Optical Optocoupler Module
        - Photo Interrupter Sensor
        - Operates between 3.3V and 5V
        - Slotted design indicates that it can detect interruptions in the light path, typically used to measure the
          speed of rotating objects or detect the presence of objects within the slot.

### Additional Components

- Wires: For making connections between components.
- Breakout board: For mounting the esp32
- Power supply: To provide necessary voltage and current to the ESP32 and LED.

## 4. Hardware Setup

- **Schematic Diagram**
  - Circuit diagram with all connections
- **Step-by-Step Assembly Instructions**
  - Mounting the sensor
  - Connecting the ESP32
  - Connecting the LED
  - Powering the system

## 5. Software Setup

- **Installing Micropython on ESP32**
- **Writing the ESP32 Code**
  - Reading the sensor
  - Sending status messages
  - Code examples
- **Configuring AWS**
  - Setting up AWS API Gateway
  - Creating Lambda functions
  - Setting up CloudWatch monitoring
  - Configuring SNS for text alerts

## 6. Installation in the Chicken Coop

- Selecting a location for the sensor and LED
- Mounting instructions
- Weatherproofing considerations
- Ensuring proper alignment of the sensor

## 7. Testing and Troubleshooting

- **Testing the sensor**
  - Verifying sensor readings
  - Checking LED indicators
- **Testing AWS integration**
  - Sending test messages
  - Verifying Lambda function execution
- **Common Issues and Solutions**
  - Sensor not reading correctly
  - LED not lighting up
  - AWS notifications not received

## 8. Maintenance and Upgrades

- **Regular maintenance tasks**
  - Checking sensor alignment
  - Ensuring LED functionality
  - Verifying AWS connectivity
- **Potential upgrades**
  - Adding more sensors
  - Enhancing notification options

## 9. Safety and Best Practices

- Electrical safety guidelines
- Handling and care for components
- Best practices for reliable operation

## 10. Appendix

- Glossary of terms
- Additional resources
  - Links to datasheets
  - Relevant tutorials
- Contact information for support

## 11. References

- Documentation references
- Online resources
- Technical support forums
