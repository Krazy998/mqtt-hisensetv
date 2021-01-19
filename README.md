# mqtt-hisensetv
Details on how to connect to Hisense Smart TV via the built in MQTT broker service.

Hisense Smart TV's (tested on 75P7 model) run a MQTT service on TCP port 36669 which is running mosquitto 1.4.2. This is primarily used to interact with the official hisense remote app.

## NOTE that later versions of Hisense TV's are using a encrpyted MQTT connection on the same port. You need to ensure to set encryption=on and validate=off in your client (as they are using a self signed cert.)

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

Launch PrimeVideo app:
```
Publish:
/remoteapp/tv/ui_service/HomeAssistant/actions/launchapp
{
  "name" : "Amazon",
  "urlType" : 37,
  "storeType" : 0,
  "url" : "amazon"
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

Thank you to @niczoom who provided additional commands using mosquitto_pub/sub executables!


Subscribe to all topics

mosquitto_sub -v -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -t #

# Get the tv state (Publish)
mosquitto_pub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -m 0 -t /remoteapp/tv/ui_service/AutoHTPC/actions/gettvstate.

It needs the -m message paramater filled otherwise you get an error "Error: Both topic and message must be supplied"

Subscribe to topic:
mosquitto_sub.exe -h <TV_IP> -p 36669 -t /remoteapp/mobile/broadcast/ui_service/state -P multimqttservice -u hisenseservice

# Change/Set the volume
# Get current volume:
mosquitto_pub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -t /remoteapp/tv/platform_service/AutoHTPC/actions/getvolume -m ""

# Change/Set Volume (-m = volume level 0-100)
mosquitto_pub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -t /remoteapp/tv/platform_service/AutoHTPC/actions/changevolume -m 50

# Subscribe to topic:
mosquitto_sub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -t /remoteapp/mobile/broadcast/ui_service/volume

# Change/View current source
# Show current state:
mosquitto_pub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -m 0 -t /remoteapp/tv/ui_service/AutoHTPC/actions/gettvstate

# Show all available sources:
mosquitto_pub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -m 0 -t /remoteapp/tv/ui_service/AutoHTPC/actions/sourcelist

# Change source. The 'sourceid' is all thats required to change to a new source. For example to change to HDMI1:
mosquitto_pub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -t /remoteapp/tv/ui_service/AutoHTPC/actions/changesource -m {"sourceid":"3"}

The '' are needed to escape the quotes when run from the command line.

You can use 'sourcename' as well but its not necessary.
If you are then do not put a space in 'HDMI 1' eg: Use "sourcename":"HDMI1". I found using a space, which is what is shown when using 'sourcelist' above, gives an error.

# TV Power
mosquitto_pub.exe -h <TV_IP> -p 36669 -P multimqttservice -u hisenseservice -t /remoteapp/tv/remote_service/AutoHTPC/actions/sendkey -m KEY_POWER

# Other
# Navigation:
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_POWER <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_UP <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_DOWN <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_LEFT <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_RIGHT <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_RETURNS (Back) <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_MENU <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_EXIT <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_OK <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_HOME <br>

# Volume
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_VOLUMEUP <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_VOLUMEDOWN <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_MUTE <br>

# Content interaction
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_FORWARDS (fastforward) <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_BACK (rewind) <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_STOP <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_PLAY <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_PAUSE <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_0 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_1 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_2 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_3 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_4 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_5 <br> 
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_6 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_7 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_8 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_9 <br>
/remoteapp/tv/remote_service/AutoHTPC/actions/sendkey KEY_SUBTITLE <br>



**Please note that:

* Power on  is done via WOL to TV's MAC address (either wired or wireless)

**Known to work with:

Hisense 65P7
Hisense 75P7
Hisense VIDAA U 2.5
