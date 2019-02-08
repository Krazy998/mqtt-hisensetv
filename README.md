# mqtt-hisensetv
Details on how to connect to Hisense Smart TV via the built in MQTT broker service.

Hisense Smart TV's (tested on 75P7 model) run a MQTT service on TCP port 36669 which is running mosquitto 1.4.2. This is primarily used to interact with the official hisense remote app.

To access this service connect via your MQTT client

with the following settings
```
Host: IP Address of your TV
Port: 36669
Username: hisenseservice
Password: multimqttservice
```
You can then subscribe to all topics with #.

Here is a sample of the TV state:
Topic:
```
/remoteapp/mobile/broadcast/ui_service/state

{"statetype":"sourceswitch","sourceid":"4","sourcename":"HDMI 2","is_signal":1,"is_lock":0,"hotel_mode":0,"displayname":"HDMI 2"}
```
Another example:
Topic:
```
/remoteapp/mobile/broadcast/platform_service/actions/volumechange

{
	"volume_type":	1,
	"volume_value":	41
}
```
Turning TV to standby:
```
/remoteapp/mobile/broadcast/platform_service/actions/tvsleep
```

Via the MQTT service you can completely control the tv including:

* Power off
* Change Channel / Channel List
* Get TV's EPG Data
* Change Source (HDMI)
* Launch builtin Youtube / Netflix apps
* Mimic any button on the remote (up/down/left/right) volume up/down.


**Please note that:

* Power on  is done via WOL to TV's MAC address (either wired or wireless)

**Known to work with:

Hisense 65P7
Hisense 75P7
Hisense VIDAA U 2.5
