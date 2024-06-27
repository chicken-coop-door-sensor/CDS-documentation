# Chicken Coop Door Sensor for FarmLite Automatic Door

Introduction

The commercial FarmLite Chicken Coop Door system is an excellent automatic door opener. This project aims to supplement
that system by creating a passive external monitoring facility. This document outlines the design and implementation of
a passive sensor system for monitoring the status of a FarmLite Chicken Coop Door using an ESP32 and Amazon Web
Services (AWS). The system aims to provide real-time notifications about the door's open or closed status through text
messages and visual indicators, ensuring the safety and security of the chickens without modifying the door mechanism or
the chickens themselves.

Environmental Assumptions

- The chicken door open/close mechanism will not be modified
- The sensor will be passive (i.e. it takes no active part in opening/closing the chicken entrance)
- No chicken modifications  (i.e. no chips or leg bracelets)
- All times are in the `America/Chicago` (US Central Time) timezone
- Use latitude 35.4834, Longitude -86.4603 (Shelbyville, Tn) for sunrise/sunset calculations

Software Assumptions

- The ESP32 (or "Controller") will report door status only
- Another ESP32 (or "Coop Snooper") will remotely indicate door status
- Lambda functions on AWS will be responsible for most decision-making.
- Controller code will be kept as simple as possible.
- The Controller will be Wi-Fi attached, allowing it to send status information to the cloud for further processing
- The Coop Snooper will be Wi-Fi attached to enable MQTT subscriptions

Limitations

- The system, once running, cannot require any manual intervention.
- There is no way to remotely access the Controller once installed.
- The installation will be several miles and an hours drive from my house.
- Onsite visits are necessary to modify the Controller's code.
- Again, the Controller code is kept extremely simple to avoid manual intervention

Overview There are 4 ways the sensor will communicate chicken coop door status.

- Text messages
- Email
- Illuminating an LED in the coop window.
- Illuminating an LED on the "Coop Snooper" appliance

Error Conditions - these are the situations that require user notification.

- Door closure failure at sunset (sent from the Sensing Lambda)
- Door open failure at sunrise (sent from the Sensing Lambda)
- Missing keep-alive messages, indicating a Controller failure (sent from the Monitoring Lambda)
- Sunrise/Sunset times retrieval failure (sent from the Twilight Lambda)
- Status disagreement between the two optocouplers, indicating a Controller connectivity problem
  (sent from the Sensing Lambda)

Component List

- An ESP32 (the "Controller") running micropython
- A second ESP32 (the "Coop Snooper") running an AWS IoT app written in C
- Infrared Slotted Optical Optocoupler (the “sensor”) x 2
- 10mm LED bulb x2 (one for Coop Snooper and one for Controller)
- Amazon AWS account (to handle logic + text messaging)

Hardware Descriptions

- The Controller will be an [ESP32-WROOM-32U](https://a.co/d/059iSc5r)
- The Controller will have an [external antenna](https://a.co/d/01gcFkfI)
- The Coop Snooper will be a [seeed studio xiao esp32s3](https://a.co/d/0a92d6XJ)
- The optocouplers will be [these](https://a.co/d/040jaQtl)
- The LEDs will be [these](https://a.co/d/0dOozM0c)

Status Notification Logic

- If AWS receives a "door open" status message from the Controller, and it is more than a configurable number of minutes
  after sunset, it sends an alert. Otherwise, it does nothing.
- Conversely, if AWS receives a "door closed" status message, and it is more than a configurable number of minutes after
  sunrise, it sends an alert. Otherwise, it does nothing.

Roles and Responsibilities

**Controller LED Component** The window LED will *always* be illuminated. When the chicken entrance is open, it will be
red. When the door is closed, it will be green. This approach makes it obvious at night if the door has been left open.
Additionally, if the light is ever off, it indicates something is broken, providing another level of status checking.

**Controller Optocoupler Components** Two optocouplers are used. With only one coupler, if the connection from the
microController to the sensor is disconnected, the microController assumes the door is closed. To avoid that, a second
coupler is used for redundancy.  *Both* couplers need to be in agreement to set the LED color or send status. If the
couplers do not agree, an alert is sent.

**Controller** The ESP32 Controller has three responsibilities:
reading the sensor, sending status messages, and setting the local LED. Whenever the door closes or opens, the
Controller senses this and will set the LED and send an HTTP REST message to AWS. The LED colors are RED for 'open',
GREEN for '
closed' and flashing BLUE for sensor failure. In addition, a heartbeat message containing the current status is sent
about every 45 minutes.

**Coop Snooper** This stand-alone appliance is a remote reporter of the current door status. It is an AWS IoT Thing
which subscribes to a MQTT topic which conveys the current status. Mounted on the appliance will be a single, 10mm RGB
LED which will be permanently lit. The colors of the LED will reflect the current coop door status (i.e. RED = open,
GREEN = closed, flashing BLUE = internal failure). The Coop Snooper is meant to be used remotely in the farmhouse to
give status-at-a-glance of the coop door. It does not need to be on the same Wi-Fi LAN as the Controller. In fact, it
does not even need to be at the farm.

**Amazon Web Services (AWS)** AWS is where most of the processing takes place. Messages from the Controller are
intercepted by a special URL defined there. The URL is supplied by the AWS API Gateway service. The rest of the logic
involves the following AWS lambda functions.

- Sensing Lambda - The gateway service forwards all messages to a lambda function we will call the “Sensing Lambda”. The
  Sensing Lambda will decide when to alert end users. It will do this by looking at the latest door status message and
  comparing it to the time of day. Again, if the door status is open and it is after sunset, then send end-user alerts.

- Twilight Lambda - This function queries the US Naval Observatory Clock API once a day to retrieve sunrise and sunset
  times. These times are stored in a small DynamoDB table. The Sensing Lambda then queries this table for the sunrise
  and sunset values.

- Monitoring Lambda - Activity of the Sensing Lambda will be monitored by a lambda function we will call the “Monitoring
  Lambda”. The monitoring lambda will look at Cloudwatch log groups `/aws/lambda/chicken-coop-door-sensor`
  and `/aws/lambda/get-twilight-times`. It will verify the `/aws/lambda/get-twilight-times` has executed error free
  within the last 24 hours. It will also verify `/aws/lambda/chicken-coop-door-sensor` has executed error free within
  the last 10 minutes. Send an SNS message if there are any exceptions to these parms

Coop Snooper Appliance Software Design

- It is an AWS IoT Thing called `coop-snooper`
- The ESP32 code is written in C
- The code is ESP-IDF/FreeRTOS
- The cert files it uses are `coop-snooper.cert.pem`, `coop-snooper.private.key`, and `root-CA.crt`
- The MQTT topic is subscribes to is `coop/status`
- The attached LED is RGB
- RED = GPIO 9
- GREEN = GPIO 8
- BLUE = GPIO 7
- The IoT endpoint is `ay1nsbhuqfhzk-ats.iot.us-east-2.amazonaws.com`
- The Wi-Fi SSID is `disconnected_2dot4`
- The Wi-Fi Password is `Y0uN3v3rC@nT311`
- The IoT endpoint, Wi-Fi SSID, and Password are kept in a separate `include` file encrypted with
  `git-crypt`
- The LED will be YELLOW until the Wi-Fi connection is established and MQTT is working.
