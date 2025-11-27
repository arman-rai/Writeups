At first it didn't show any ports open except ssh and I tried bruteforcing using hydra, but it  didn't show up even after 5 minutes
Rescanned and got this
```
PORT     STATE SERVICE REASON  VERSION
1883/tcp open  mqtt    syn-ack
|_mqtt-subscribe: ERROR: Script execution failed (use -d to debug)

```
https://en.wikipedia.org/wiki/MQTT
This was an IoT lab? 

Used mosquitto to read and publish data
Used this blog and got the flag
https://babdcatha.net/index.php/2023/03/03/tryhackme-bugged/

Hacktricks ma pani thiyo, try gare python script haru tara chalena, sed
https://book.hacktricks.wiki/en/network-services-pentesting/1883-pentesting-mqtt-mosquitto.html?highlight=mqtt#pentesting-mqtt
