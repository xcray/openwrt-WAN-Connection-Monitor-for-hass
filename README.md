# openwrt-WAN-Connection-Monitor-for-hass
Monitoring the status of WAN connection on OpenWRT router in HomeAssistant
#### reference:
https://openwrt.org/docs/guide-user/base-system/hotplug
#### configuration.yaml:
```
binary_sensor:
  - platform: mqtt
    state_topic: "openwrt/pppoe-wan"
    payload_on: "ifup"
    payload_off: "ifdown"
    name: internet
    device_class: connectivity
    json_attributes_topic: "openwrt/wan-ip"
```
last 3 lines are not essential.
Of cause, there should be one mqtt broker in the configuration:
```
mqtt:
  broker: mqtt_broker #your own broker address
```
#### script under /etc/hotplug.d/ directory of OpenWRT router:
```
#!/bin/sh
[ "$INTERFACE" = "pppoe-wan" ] || exit 0
mosquitto_pub -h mqtt_broker -t openwrt/pppoe-wan -m "$ACTION"
wanip=$(ifconfig pppoe-wan | awk '/inet addr/{print substr($2,6)}')
mosquitto_pub -h mqtt_broker -t openwrt/wan-ip -m "{\"wan-ip\":\""$wanip"\"}" 
```
last 2 lines are not essential.

* The topics should match between the script and the configuration.*
