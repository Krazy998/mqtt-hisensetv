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
```
Publish:
/remoteapp/tv/ui_service/HomeAssistant/actions/gettvstate

Subscribe:
/remoteapp/mobile/broadcast/ui_service/state
JSON output:
{"statetype":"sourceswitch","sourceid":"4","sourcename":"HDMI 2","is_signal":1,"is_lock":0,"hotel_mode":0,"displayname":"HDMI 2"}
```
How to get TV sources lists:
```
Publish:
/remoteapp/tv/ui_service/HomeAssistant/actions/sourcelist

Subsribe:
/remoteapp/mobile/HomeAssistant/ui_service/data/sourcelist
JSON output:
[{"sourceid":"4","sourcename":"HDMI 2","displayname":"HDMI 2","is_signal":"1","is_lock":"0","hotel_mode":"0"},{"sourceid":"0","sourcename":"TV","displayname":"TV","is_signal":"1","is_lock":"0","hotel_mode":"0"},{"sourceid":"3","sourcename":"HDMI 1","displayname":"HDMI 1","is_signal":"0","is_lock":"0","hotel_mode":"0"},{"sourceid":"5","sourcename":"HDMI 3","displayname":"HDMI 3","is_signal":"0","is_lock":"0","hotel_mode":"0"},{"sourceid":"6","sourcename":"HDMI 4","displayname":"HDMI 4","is_signal":"0","is_lock":"0","hotel_mode":"0"},{"sourceid":"2","sourcename":"COMPONENT","displayname":"COMPONENT","is_signal":"0","is_lock":"0","hotel_mode":"0"},{"sourceid":"1","sourcename":"AV","displayname":"AV","is_signal":"0","is_lock":"0","hotel_mode":"0"}]
```
Launch Youtube app:
```
Publish:
/remoteapp/tv/ui_service/HomeAssistant/actions/launchapp
{
  "name" : "YouTube",
  "urlType" : 37,
  "storeType" : 0,
  "url" : "youtube"
}
```

Launch Netflix App:
```
/remoteapp/tv/ui_service/HomeAssistant/actions/launchapp
{
  "name" : "Netflix",
  "urlType" : 37,
  "storeType" : 0,
  "url" : "netflix"
}
```

Turning TV to standby:
```
Publish:
/remoteapp/tv/remote_service/HomeAssistant/actions/sendkey

KEY_POWER
```
Change Source:
```
/remoteapp/tv/ui_service/homeAssistant/actions/changesource
```
To TV:
```
{
  "sourceid" : "0",
  "sourcename" : "TV"
}
```
To HDMI 2:
```
{
  "sourceid" : "4",
  "sourcename" : "HDMI 2"
}
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
