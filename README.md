# rogers

Wireless sensor for water counter without impulsion emmiter, with metal wheel for deciliter.

Project's goal is to provide a wireless sensor which interface water counter like this one:

![](https://www.cazabox.com/6704-thickbox_default/compteur-d-eau-divisionnaire-eau-froide-aquadis-itron.jpg)

# Software

## High level requirements

|Identifier|Description|
|----------|-----------|
| HLR1 | Rogers emits data using X10 counter protocol, on the 433Mhz bandwith|
| HLR2 | Rogers provides two measurements: remaining battery voltage and current counter value in liter|
| HLR3 | Rogers sends an message for each liter|
| HLR3 | Rogers sends an message each hour with total water consumption and battery voltage in millivolts|


## Low level software requirements

|Identifier|Description|
|----------|-----------|
|LLR1| Rogers sense battery voltage using ADC channel in millivolts|
|LLR2| Rogers is awaken when `PB2` pin is driven low|
|LLR3| Rogers emits battery voltage in millivolt using X10Meter protocol |
|LLR4| Rogers emits total water consumption in liters using X10Meter protocol|
|LLR5| Rogers detects metal disc movement through the CNY70 wired on `PA0`|
|LLR6| Rogers count a new consumed liter when the CNY70 value follow a model OUT_OF_DISK_ANALOG_VALUE -> ON_DISK_ANALOG_VALUE -> OUT_OF_DISK_ANALOG_VALUE |
|LLR7| If for some reason Rogers is locked in a CNY70 value model detection for more than 10 seconds, then detection is aborted |
 
# Hardware

## Architecture

```
                |--------|
 Battery -----> |        |
                |        | 
 Sensor ------> | Rogers | -- 433Mhz emmiter -->
                |        |
 Ext. signal -> |        |
                |--------|
 
 ```
* Battery is a 18650
 * Sensor is a [CNY70](https://docs.rs-online.com/dc23/0900766b80e2fbf3.pdf)
 * Ext. signal will be triggered by a sound detection module like [this one](https://electropeak.com/sound-sensor-ky-037)
 * Rogers itself will be implemented on a ATTiny84a mcu

|Identifier|Description|
|----------|-----------|
|HWR1| CNY70 wired on `PA0` throught a voltage divider using a 10k resistor. Analog value should be around 400 when out of metal disk, around 700 on the disk  |
|HWR2| Rogers detects metal disc movement through the CNY70 wired on `PA0` using 10k resistor |


# Non functional requirements

|Identifier|Description|
|----------|-----------|
| NFR1     | Rogers runs on battery, and should stay in deep sleep mode when there is no water circulation|
| NFR2     | Rogers does not start if battery voltage is too low to avoid deep discharge |


# Constants

|Name      | Value | Description|
|----------|-------|------------|
| ON_DISK_ANALOG_VALUE    | [700;750]      |  Analog value read when the sensor is  facing the metal disk           |
| OUT_OF_DISK_ANALOG_VALUE     | [380;420]      |   Analog value read when the sensor is not facing the metal disk         |
