# M5Stack M5NanoC6 Zigbee Sniffer

Instructions for configuring the [M5NanoC6](https://www.amazon.com/MARALANG-M5NanoC6-Low-Power-Development-ESP32-C6FH4/dp/B0D7674RRF) to be used as a Zigbee Sniffer.


![M5NanoC6](https://gainsec.com/wp-content/uploads/2024/08/nanoc6.jpg?v=1723594418)

## Notes

I believe it is working as intended, but I have to configure some ZigBee devices and double check. Lmk if I'm mistaken!

### Prerequisites

```
apt cmake, python3.11, spinel
pip3 install idf-component-manager --upgrade
```

## Instructions

```
cd /opt
mkdir WIRELESS
cd WIRELESS
git clone --recursive https://github.com/espressif/esp-idf.git
cd esp-idf
./install.sh
chmod +x export.sh
./export.sh
```

Now you can try 

```
cd examples/openthread/ot_rcp
idf.py set-target esp32c6
```

If that doesn’t work as it didn’t for me 

```
source ~/esp-idf/export.sh
```

Now if that still doesn’t work

```
source ~/.zshrc
```

Now it should definitely work, and you can try to confirm 

```
idf.py --help
```

Now we need to add two entries to *sdkconfig.defaults* file 

```
nano examples/openthread/ot_rcp/sdkconfig.defaults
```

Add the following under the *Openthread* Section

```
CONFIG_OPENTHREAD_NCP=y
CONFIG_OPENTHREAD_ENABLE_RAW_LINK_API=y
```

FYI, the M5NanoC6 should be seen as a JTag/Serial USB Debug Device in Kali and is often defaulted to (especially if its the only USB plugged in): */dev/ttyACM0*

Now lets properly compile

```
idf.py set-target esp32c6
idf.py build
```

![Compiling](https://gainsec.com/wp-content/uploads/2024/08/1-compiling.png?v=1723595006)

Then flash

```
idf.py flash
```

![Flashing](https://gainsec.com/wp-content/uploads/2024/08/2-flashing.png?v=1723595023)

You should get a success message after its done.

![Flashed](https://gainsec.com/wp-content/uploads/2024/08/3-flashed.png?v=1723595055)

Now lets see if Spinel-CLI connects to the M5NanoC6 properly

```
spinel-cli.py -u /dev/ttyACM0 -b 115200
```

Now the CLI menu should open, confirming its working properly. Now *CRTL+C* to exit that

![Spinel-CLI](https://gainsec.com/wp-content/uploads/2024/08/4-spinel-cli-working.png?v=1723595842)

Now while still having the M5NanoC6 plugged in, press its button.

Now lets start the sniffer!

```
sudo sniffer.py -c 11 -n 1 --crc -u /dev/ttyACM0
```

If you get an error after it states initializing sniffer... "cannot initialize sniffer" you should unplug and replug in the M5NanoC6, then try again. If that doesn't work, unplug, replug in and then press the button before trying again.

It should then work!

![Sniffer](https://gainsec.com/wp-content/uploads/2024/08/5-sniffing.png?v=1723595721)

In the next post (or maybe I'll edit this one) I will show data captured as I don't actually have any Zigbee devices (maybe that DTM for the Core2 will come in handy for this).

Additionally, be sure to set up Wireshark to properly display the packets and data using the Links below:

* [ZigBee2MQTT Sniffing](https://www.zigbee2mqtt.io/advanced/zigbee/04_sniff_zigbee_traffic.html)
* [Wireshark Part of the Espressif Zigbee SDK Documentation](https://docs.espressif.com/projects/esp-zigbee-sdk/en/latest/esp32/developing.html)

## Resources 

* [Interesting Overview of Zigbee](https://www.msxfaq.de/sonst/iot/zigbee.htm)
* [ESPRESSIF Zigbee SDK Documentation](https://docs.espressif.com/projects/esp-zigbee-sdk/en/latest/esp32/developing.html)
* [ESPRESSIF OpenThread Radio Co-Processor OT_RCP](https://github.com/espressif/esp-idf/blob/master/examples/openthread/ot_rcp/README.md)
* [PySpinel Sniffer Documentation](https://openthread.io/guides/pyspinel/sniffer)

Post on my own site about this: [M5NanoC6 Zigbee Sniffer Easy 2024](https://gainsec.com/2024/08/13/m5nanoc6-zigbee-sniffer/)

## Author

* **Jon Gaines** - *Creator* - [GainSec](https://github.com/GainSec) - Managing Security Consultant @NetSPI

## To Do

* Confirm sniffer is working as expected (I'd appeciate if you tell me its working properly or at least capturing data!)

## License

This project is licensed under the GNU License - see the [LICENSE.md](LICENSE.md) file for details
