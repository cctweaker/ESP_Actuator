# ESP Actuator
Software for the 8 channel underfloor heating controller provided by <a href="https://3dstar.ro/proiecte/esp-actuator">3DStar.ro</a>

# Features
- secure MQTT connection possible, Let's Ecrypt CA cetificate already loaded
- topic structure for multiple buildings (locations) control: <code>location/device_type/device_name/</code>
- channel status saved and recovered on restart from MQTT server
- on / off for each heating channel, with PWM control
- main and backup WiFi connections
- secured, no access point is presented in case both WiFi connections are unavailable
- web interface for setup, can be disabled
- extra information on device status topic: type, ID, supply voltage, AP SSID, AP RSSI, AP mac, etc.


# Hardware
Roll your own or get one from <a href="https://3dstar.ro/proiecte/esp-actuator">3DStar shop</a> in PCB, kit (PCB + components), SMD kit (PCB with soldered SMD components + bags of through hole components) or fully assembled form.

The ESP-07S from AI Thinker is used to send channel and PWM commands to a ATtiny1604 (804 or 404) microcontroller. Each channel is using a low power optotriac to directly control a mains power heating actuator. Each channel can switch up to around 50W. Heated wax mains actuators for water based underfloor heating use around 50W of power when cold and 2W of power when fully engaged.

The ESP takes care of wireless communications (MQTT & HTTP) and has a web interface for programming. The ATtiny does slow PWM and per channel control of the optotriacs.

This project uses mains voltage, please conform to your local regulations!

# Build & upload
There are 2 steps for this project.
Step 1 involves the firmware for the ATtiny that needs to be built using the Arduino IDE and uploaded by a simple UPDI serial adapter. Take care to use a 3.3V adapter!
Step 2 is the firmware for the ESP-07S. This can be built using Arduino IDE or easier using PlatformIO and then uploaded to the ESP.

# Setup
On the first powerup, a WiFi access point is presented. If using the default configuration, it is called "ESP_Actuator_XXXXXX". Default password is abcd1234. Connect to it and you should arrive at the configuration page. If you are not automagically redirected (it's called a sign in page) then you can point your browser to http://192.168.10.1/

- [Device] menu will allow you to change LOC, TIP, NAME variables (these make up the MQTT topic), update URL (if you want to set up your own server for updates), MQTT heartbeat on/off and period and if the webserver should be started.
- [WIFI] menu will let you scan for WiFi networks and set up credentials for both networks. The second one is recommended to be set to your phone's hotspot in case you change the main Wifi network credentials.
- [MQTT] menu is used to set up your MQTT connection
- [Update firmware] - self explanatory, you can use your own server (defined under Device menu) or the official release channel as defined in the firmware.
- [Erase settings] menu will delete all configuration files stored on the ESP but will not restart the device. Menus will still show the current settings from active memory. Use this trick to save at least the Wifi settings then restart. If you restart without saving WiFi credentials then it will behave like a new device.
- [Restart] - reboots the device
- [System information] - shows some infos about the device

# Usage
- MQTT topic: <code>LOC/TIP/NAME</code> where LOC, TIP & NAME are user defined. What follows are possible suffixes and they will be added after the mqtt topic.
- command topic: <code>/cmnd/...</code>
    - <code>/update</code>: perform OTA update
    - <code>/reset</code>: restart ESP device
        
    - <code>/cmnd/cX</code>: per channel control. X is the channel number. Payload can be 0 = off, 1 = on
    - <code>/cmnd/pX</code>: per channel PWM control. X is the channel number. Payload can be 0 to 255
    - <code>/cmnd/pwm</code>: global PWM duration in 0.1 seconds. Payload can pe 0 to 255

- status topic: <code>/stat</code> where the ESP sends periodic updates (heartbeats) with useful information.
- will topic: <code>/will</code> Payload is 1 when device is ON and 0 when powered OFF

# Libraries
- https://github.com/256dpi/arduino-mqtt
- https://github.com/bblanchon/ArduinoJson

# PCB example
<img src="https://github.com/cctweaker/ESP_Actuator/blob/master/hardware/top.jpg?raw=true">
<img src="https://github.com/cctweaker/ESP_Actuator/blob/master/hardware/bottom.jpg?raw=true">