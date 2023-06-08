# Theoretical power consumption per component
*All data listed below has been referenced from the components respective datasheets.*

# ATSAMD21 microcontroller
**standby:**

TYP: 4,06µA-55,2µA (3,3V)
MAX: 100µA

**idle:**

1,07mA-2,33mA (3,3V)

**regular:**

6,5mA-7mA (3,3V)

*This is the highest mentioned consumed current, which is during ACTIVE state while running a CoreMark algorithm at 85°C. Mentioned in datasheet table 36-8, pg 942.*

**measurement process:**

The microcontroller wakes up from sleep mode to perform measurements and then exchange data via LoRa. The duration of the microcontroller being out of sleep mode depends on the duration of the measurements and the LoRa communication.

In addition, a variable for the time interval between wake-ups could be adjusted via LoRa. This can be customized based on the actual energy consumption during usage.

# RN2483 Lora module
**sleep mode:** 

1,6µA-2,6µA (3,3V-3,6V)

**idle/standby:**

2,8mA-3,1mA (3,3V-3,6V)

**regular:**

868MHz: *highest setting*: 38,9mA (3,3V)
433MHz: *highest setting*: 32.9mA (3,3V)

**receive:**

14,22mA-14,69mA (3,3-3,6V)

**measurement process:**

*Refer to LoRa operation*

# SCD41 CO2 sensor

**sleep mode:** 



**idle/standby:**

*There is no data available for this mode in the component datasheet, but according to eMariete:*
*(https://emariete.com/en/sensor-co2-sensirion-scd40-scd41-2/#:~:text=According%20to%20my%20own%20measurements,period%20has%20been%201.20mA.)*

171µA during idle mode at 1 single shot per minute

**regular:**

15mA-18mA (3,3V)
11mA-13mA (5V)

**low power periodic:**

3,2mA-3,5mA (3,3V)
2,8mA-3mA (5V)

**periodic single shot:**

0,45mA-0,5mA (3,3V)
0,36mA-0,4mA (5V)

**measurement process:**

On startup: the component starts up at 2,25V. 1000ms after startup, the component is in idle mode. From idle mode, measurements can be executed. For periodic single shot it takes 5 seconds to aquire measured data.

# SGP41 VOC&NOx sensor
**idle/standby:**

34µA-105µA (3,3V)

**conditioning mode:**

4,2mA-4,6mA (3,3V)

**regular:**

3,0mA-3,4mA (3,3V)

**measurement process:**

0,6ms power-up time, after which the sensor enters idle mode. From idle mode we can put the component into conditioning mode. In conditioning mode a standard of 10s is used and is also the conditioning time maximum. After conditioning, the instruction can be given to measure the rough data, which takes 1s. Afterwards, the instruction can be given to return to idle mode.

# SPS30 particulate matter sensor
**sleep mode:** 

38µA-50µA (5V)

**idle/standby:**

330µA-360µA (5V)

**regular:**

55mA-65mA (5V)

**startup-measurement**

80mA (5V)

**measurement process:**

Upon startup, the sensor enters idle mode. When a measurement command is given, it takes 20ms to transmit the command. After that, it takes 200ms to initialize the measurement and 1s to perform the measurement. Subsequently, a stop measurement command can be given, which takes 20ms, followed by a sleep command, which takes 5ms.

Additionally, there is a default routine that runs once a week (every 604,800s) to activate the fan in the component and blow out the dust. This process takes 10s. The time interval in seconds can be adjusted to increase the interval.

# BME280 humidity & pressure sensor
**sleep mode:** 

0,1µA-0,3µA

**idle/standby:**

0,2µA-0,5µA

**regular:**

humidity measurement: 340µA
pressure measurement: 714µA
temperature measurement: 350µA

**measurement process:**

2ms after the startup voltage is exceeded, the sensor is ready to receive commands. We can instruct the sensor to perform measurements in forced measurement mode, where we take a short set of measurements and put the component back to sleep. The maximum measurement time for a single measurement of the three parameters is 9,3ms. If we were to take 5 measurements to obtain an average value, it would take approximately 50ms. Afterward, the sensor can be put back into sleep mode.

# GY-NEO6M GPS module
**sleep mode:** 

Backup battery current: 22µA (1,8V)

**regular:**

11,20mA-67mA


**measurement process:**

After the module is started, a cold start can be performed. The cold start takes 27s, and then measurement data starts coming in at a rate of 5Hz. After a few measurements, we can enter the deselect command, and after 50ms, the module will stop performing measurements, allowing us to put it back into Backup current mode.

# Theoretical power usage per routine
In this iteration of the project, we are using stationary applications, so no GPS module will be used. The power consumption of this module will not be taken into account in the calculations below.


**Active:**

Here the power usage will be calculated for every series of measurements

ATSAMD21: <60s at 7mA (=0,117mAh)
+
RN2483: <20s at 38,9mA (=0,216mAh)
+
SCD41: 6s at 5mA (=0,008mAh)
+
SGP41: 10s at 4,6mA (=0,013mAh) and <5s at 3,4mA (=0,005mAh)
+
SPS30: 200ms at 80mA (=0,004mAh) and <5s at 65mA (=0,09mAh) (ALSO 1x/week 10s long at 80mA (=0,222mAh))
+
BME280: <1s at 714µA (=0.0002mAh)
=
0,6752mAh

To get rid of inefficiëncies in code and hardware, we will round this up to 1mAh.

**Standby:**

Here the base power usage during standby is calculated. This will be a constant base power usage of our application.

ATSAMD21: 100µA
+
RN2483: 2,6µA
+
SCD41: 171µA (idle during periodic single shot)
+
SGP41: 105µA
+
SPS30: 50µA
+
BME280: 0,3µA
=
428,9µA

To get rid of possible inaccurate data or calculations and for ease of other calculations, we will consider this at 1mA.

**Combined:**

Our application continually uses 428,9µA with an additional 0,6752mAh everytime a cycle of measurements is performed.

If we put a buffer on these values, we will consider out application with a constant power usage of 1mA and an additional power usage of 1mA every time a measurement is performed.