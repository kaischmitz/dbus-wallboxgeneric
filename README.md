# dbus-wallboxgeneric
Integrate any wallbox into Victron Energies Venus OS

## Purpose
With the scripts in this repo it should be easily possible to install, uninstall, restart a service that sets up the DBUS structure for any wallbox in VenusOS and GX devices from Victron.
The idea is to fill this structure later on via MQTT.
Forked from dbus-goecharger by vikt0rm.


## How it works
### Why? ###
I am running a Vestel EVC04 wallbox. It can be controlled via Modbus TCP, however per manufacturer's spec only one single connection to the Modbus instance is allowed.
The entire charging control in my case is running in Nodered which has the connection to the wallbox via Modbus TCP.
So in order to integrate the measured values from the wallbox into the VRM portal I wanted to write the values from my Nodered instance to the Venus OS or VRM directly.

### Details / Process
What is the script doing:
- Running as a service
- connecting to DBus of the Venus OS `com.victronenergy.evcharger.generic_{DeviceInstanceID_from_config}`
- Paths are added to the DBus with default value 0 - including some settings like name, etc
- After that a "loop" is started which pulls go-eCharger data every 750ms from the REST-API and updates the values in the DBus
- After successful DBus connection all data points need to be updated via MQTT

## Install & Configuration
### Get the code
Just grap a copy of the main branche and copy them to a folder under `/data/` e.g. `/data/dbus-wallboxgeneric`.
After that call the install.sh script.

The following script should do everything for you:
```
wget https://github.com/vikt0rm/dbus-goecharger/archive/refs/heads/main.zip
unzip main.zip "dbus-goecharger-main/*" -d /data
mv /data/dbus-goecharger-main /data/dbus-goecharger
chmod a+x /data/dbus-goecharger/install.sh
/data/dbus-goecharger/install.sh
rm main.zip
```
⚠️ Check configuration after that - because service is already installed an running and with wrong connection data (host) you will spam the log-file

### Change config.ini
Within the project there is a file `/data/dbus-goecharger/config.ini` - just change the values - most important is the deviceinstance under "DEFAULT" and host in section "ONPREMISE". More details below:

| Section  | Config vlaue | Explanation |
| ------------- | ------------- | ------------- |
| DEFAULT  | AccessType | Fixed value 'OnPremise' |
| DEFAULT  | SignOfLifeLog  | Time in minutes how often a status is added to the log-file `current.log` with log-level INFO |
| DEFAULT  | Deviceinstance | Unique ID identifying the shelly 1pm in Venus OS |
| ONPREMISE  | Host | IP or hostname of on-premise Shelly 3EM web-interface |


## Usefull links
- https://github.com/goecharger/go-eCharger-API-v1
- https://github.com/victronenergy/dbus_modbustcp/blob/master/CCGX-Modbus-TCP-register-list.xlsx
- https://github.com/trixing/venus.dbus-twc3

## Discussions on the web
This module/repository has been posted on the following threads:
- https://community.victronenergy.com/questions/128552/go-echarger-integration-in-venus-os.html
