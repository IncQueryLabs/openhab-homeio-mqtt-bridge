# openhab-homeio-mqtt-bridge
OpenHAB - HomeIO MQTT Bridge allows the control of devices in [HomeIO smart home simulator](https://realgames.co/home-io/) through MQTT. If [OpenHAB](https://www.openhab.org/) smart home automation system is configured up and running with [OpenHAB MQTT Event bus binding](https://docs.openhab.org/addons/bindings/mqtt1/readme.html) add-on, smart home automation rules can be created in OpenHAB for HomeIO. We have already created smart home automation rules for HomeIO in OpenHAB with Drool Complex Event Processor rule engine in the [smarthome-cep-demonstrator](https://github.com/IncQueryLabs/smarthome-cep-demonstrator) project.

## Steps to install:
 
 * Register for a free trial for [HomeIO](https://realgames.co/home-io/) and install it.
 * Install and configure any **MQTT broker** on local host (127.0.0.1).
 * Clone this reposity `git clone https://github.com/IncQueryLabs/openhab-homeio-mqtt-bridge`.
 * Download the [HomeIO SDK](https://realgames.co/docs/homeio/en/sdk-getting-started/). Copy the **EngineIO.dll** from this SDK to the **OpenHAB-HomeIO-MQTT-bridge** folder.
 * *(Optional)* Install and configure [OpenHAB](https://www.openhab.org/) with the [MQTT Event bus binding](https://docs.openhab.org/addons/bindings/mqtt1/readme.html) add-on (configure it to use the same MQTT broker).
 * *(Optional)* Install and configure [smarthome-cep-demonstrator](https://github.com/IncQueryLabs/smarthome-cep-demonstrator). This provides smart home automation rules for HomeIO in OpenHAB with [Drools](https://www.drools.org/) Complex Event Processor.

## Usage notes
This project is originally designed to be used with OpenHAB and smarthome-cep-demonstrator. However, this OpenHAB - HomeIO MQTT Bridge can be used without them.

### Supported HomeIO memory addresses
All HomeIO memory addresses (which can be input, output or memory) are in the [HomeIO documentation](https://realgames.co/docs/homeio/en/memory-addresses/
The ? mark means it is not tested yet, however it should work. Some devices have an simple (bool) and an analog (float) representation. 

All HomeIO inputs are supported:
 
  * brightness sensor (+ analog)
  * roller shades openness
  * light switch
  * up/down switch
  * light switch dimmer
  * door detector
  * motion detector
  * smoke detector
  * thermostat
  * alarm keypad
  * HomeIO time
  * remote buttons?

All HomeIO outputs are supported:

 * light (+ analog)
 * heater (+ analog?)
 * roller shades motors
 * garage door motors?
 * siren
 * alarm keypad

1 HomeIO memory is supported:

 * time scale

### Topic names:

 * in/**memoryName**/state for the inputs and memory
 * out/**memoryName**/state for the outputs

HomeIO device names cannot be used in OpenHAB because in OpenHAB the names are IDs and can only contain alphanumeric and underscore characters. The **memoryName** is the HomeIO name converted into OpenHAB consumable format. The first part of the memory name is the HomeIO room name. The second part is the HomeIO memory name, where spaces, brackets and dashes are converted to underscores. This two parts are connected with an underscore. Examples:

 * **Light Switch 1** in room **A** -> **A_Light_Switch_1**
 * **Up/Down Switch (Up)** in room **D** -> **D_Up_Down_Switch_2_Up**

### Value conversion
OpenHAB and HomeIO uses different values and value ranges for the same type, so this bridge convrets the HomeIO values into OpenHAB acceptable format and only accepts commands in OpenHAB format.
 
 * Input and output float values are the same as in HomeIO, except for the roller shades openness.
 * HomeIO float values for the roller shades openness converted to 0-100 range (Default HomeIO is 0-10).
 * HomeIO output bool values are ON/OFF (ON -> true, OFF -> false).
 * HomeIO input bool values are OPEN/CLOSED (true -> OPEN, false -> CLOSED) except the door detector.
 * Door detector (false -> OPEN, true -> CLOSED), so the OPEN means an opened door.
 * HomeIO DateTime is in  [Round-trip date/time format](https://docs.microsoft.com/en-us/dotnet/standard/base-types/standard-date-and-time-format-strings#Roundtrip)
 
### Example messages
out/**A_Lights**/state **ON** -> Turns **on** the **light** in room A

out/**E_lights_Analog**/state **50** -> Turns the **light** to **50%** in room E

in/**E_Door_Detector_1**/state **OPEN** -> Door 1 **opened** in room E 

in/**D_Thermostat_Room_Temperature**/state **15.63826** -> Room temperature in room D is **15.63826 °C**

in/**G_Motion_Detector**/state **OPEN** -> Motion detector in room G detected **motion**

in/**HomeIO_Date**/state **2017-09-01T12:00:00.0000000** -> The simulated time in HomeIO is **2017.09.01. 12:00**

in/**Time_Scale**/state **50** -> The time scale in HomeIO is 50. (**50x faster** than the real time)
