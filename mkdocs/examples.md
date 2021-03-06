## How to embed the AutoConnect

Here holds two case examples. Both examples perform the same function. Only how to incorporate the **AutoConnect** into the sketch differs. Also included in the sample folder, HandlePortal.ino also shows how to use the [PageBuilder](https://github.com/Hieromon/PageBuilder) library for HTML assemblies.

## What does this example do?

Uses the web interface to light the LED connected to the **[NodeMCU](https://github.com/nodemcu/nodemcu-devkit-v1.0)** module D0 port (which could be referred to as *BUILTIN_LED*), the following animation is it.

Access to the ESP8266 module connected WiFi from the browser then the page contains the current value of the D0 port would be displayed. The page has the buttons to switch the port value. The LED blinks according to the value of the button that was clicked. This example is a typical sketch of manipulating ESP8266's GPIO via WLAN.

<img data-gifffer="../images/ac2.gif" />

Embed AutoConnect library into this sketch. There are few places to be changed. And you can use AutoConnect's captive portal function to establish a connection freely to other WiFi spots.

## Embed AutoConnect

### <i class="fa fa-code" aria-hidden="true"></i> Pattern A.

Bind to ESP8266WebServer, performs handleClient with handleRequest.

<img src="../images/handleClient.svg" />

!!! hint "In what situations should the handleRequest be used."
    It is something needs to be done immediately after the handle client. It is better to call only AutoConnect::handleClient whenever possible.

### <i class="fa fa-code" aria-hidden="true"></i> Pattern B.

Declare only AutoConnect, performs handleClient.

<img src="../images/handlePortal.svg" />

## Used with MQTT as a client application

The effect of AutoConnect is not only for ESP8266/ESP32 as the web server. It has advantages for something WiFi client as well. For example, AutoConnect is also effective for publishing MQTT messages from various measurement points. Even if the SSID is different for each measurement point, it is not necessary to modify the sketch.

This example tries to publish the WiFi signal strength of ESP8266 with MQTT. It uses the [ThingSpeak](https://thingspeak.com/) for MQTT broker. ESP8266 publishes the RSSI value to the channel created on ThingSpeak as [MQTT client](https://github.com/knolleary/pubsubclient). This example is well suited to demonstrate the usefulness of AutoConnect, as RSSI values are measured at each access point usually. Just adding a few lines of code makes it unnecessary to upload sketches with the different SSIDs rewrite for each access point.

<img src="../images/ChannelStatus.png" width="70%"/>

### Advance procedures

- Arduino Client for MQTT - It's the [PubSubClient](https://github.com/knolleary/pubsubclient), install it to Arduino IDE. If you have the latest version already, this step does not need.
- Create a channel on ThingSpeak.
- Get the Channel API Keys from ThingSpeak, put its keys to the sketch.

The ThingSpeak is the open IoT platform. It is capable of sending data privately to the cloud and analyzing, visualizing its data. If you do not have an account of ThingSpeak, you need that account to proceed further. ThingSpeak has the free plan for the account which uses within the scope of this example.[^1] You can sign up with the [ThingSpeak sign-up page](https://thingspeak.com/users/sign_up).

!!! warning "Whether you should do sign-up or not."
    You are entrusted with the final judgment of account creation for ThingSpeak. Create an account at your own risk.

[^1]:As of March 21, 2018.

#### Create a channel on ThingSpeak

Sign in ThingSpeak. Select **Channels** to show the **My Channels**, then click **New Channel**.

At the **New Channel** screen, enter each field as a below. And click **Save Channel** at the bottom of the screen to save.

- Name: ```ESP8266 Signal Strength```
- Description: ```ESP8266 RSSI publish```
- Field1: ```RSSI```

<img src="../images/CreateChannel.png" width="70%"/>

#### Get Channel ID and API Keys

The channel successfully created, you can see the channel status screen as a below. **Channel ID** is displayed there.[^2]

[^2]:'454951' in the example above, but your channel ID should be different.

<img src="../images/ChannelID.png" width="70%"/>

Here, switch the channel status tab to **API Keys**. The API key required to publish the message is the **Write API Key**.

<img src="../images/APIKeys.png" width="70%"/>

The last key you need is the **User API Key** and can be confirmed it in the user profile. Pull down **Account** from the top menu, select **My profile**. Then you can see the ThingSpeak settings and the **User API Key** is displayed middle of this screen.

<img src="../images/USERKey.png" width="70%"/>

### The sketch, Publishes messages

The complete code of the sketch is [mqttRSSI.ino](https://github.com/Hieromon/AutoConnect/blob/master/examples/mqttRSSI/mqttRSSI.ino) in the [AutoConnect repository](https://github.com/Hieromon/AutoConnect). Replace the following #define in a sketch with **User API Key**, **Write API Key** and **Channel ID**. After Keys updated, compile the sketch and upload it.

```arduino
#define MQTT_USER_KEY        "****************"  // Replace to User API Key.
#define CHANNEL_ID           "******"            // Replace to Channel ID.
#define CHANNEL_API_KEY_WR   "****************"  // Replace to the write API Key.
```

### Publish messages

After upload and reboot complete, the message publishing will start via the access point now set. The message carries RSSI as the current WiFi signal strength. The signal strength variations in RSSI are displayed on ThingSpeak's Channel status screen.

### How embed to your sketches

For the client sketches, the code required to connect to WiFi is the following four parts only.

1. \#include directive[^3]
    
    Include ```AutoConnect.h``` header file behind the include of ```ESP8266WiFi.h```.

    <img src="../images/include.png" width="55%"/>

2. Declare AutoConnect
    
    The declaration of the [**AutoConnect variable**](api.md#autoconnect) is not accompanied by ESP8266WebServer.

    <img src="../images/declare.png" width="55%"/>

3. Invokes "begin()"
    
    Call [**AutoConnect::begin**](api.md#begin). If you need to assign a static IP address, executes [**AutoConnectConfig**](api.md#autoconnectconfig-api) before that. 

    <img src="../images/begin.png" width="55%"/>

4. Performs "handleClent()" in "loop()"
    
    Invokes [**AutoConnect::handleClient()**](api.md#handleclient) at inside ```loop()``` to enable the AutoConnect menu.

    <img src="../images/handleClient.png" width="55%"/>

[^3]:```#include <ESP8266WebServer.h>``` does not necessary for uses only client.

<script>
  window.onload = function() {
    Gifffer();
  }
</script>
