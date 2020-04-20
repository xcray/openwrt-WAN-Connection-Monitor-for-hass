# openwrt-WAN-Connection-Monitor-for-hass
Monitoring the status of WAN connection on OpenWRT router in HomeAssistant
#### reference:
https://openwrt.org/docs/guide-user/base-system/hotplug
#### configuration.yaml:
```
binary_sensor:
  - platform: mqtt
    state_topic: "openwrt/pppoe-wan"
    name: internet
    device_class: connectivity
    json_attributes_topic: "openwrt/wan-ip"
```
last 3 lines are not essential.
Of cause, there should be one mqtt broker in the configuration:
```
mqtt:
  broker: x.x.x.x #your own broker address
```
#### script under /etc/hotplug.d/ directory of OpenWRT router:
```
#!/bin/sh
[ "$INTERFACE" = "wan" ] || exit 0
[ "$ACTION" = "ifdown" ] && mosquitto_pub -h x.x.x.x -t openwrt/pppoe-wan -m OFF
[ "$ACTION" = "ifup" ] && mosquitto_pub -h x.x.x.x -t openwrt/pppoe-wan -m ON
[ "$ACTION" = "ifupdate" ] && (
    wanip=$(ifconfig pppoe-wan | awk '/inet addr/{print substr($2,6)}')
    mosquitto_pub -h x.x.x.x -t openwrt/wan-ip -m "{\"wan-ip\":\""$wanip"\"}" )
```
last 3 lines are not essential.

***The topics should match between the script and the configuration.***
***Don't forget to replace x.x.x.x with right address***
