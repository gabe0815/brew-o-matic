# Brew-o-matic: a node-red based mash controller

Brew-o-matic is a node-red based mash controller which allows you to automate mashing
temperature profiles for consitent home brews. Convert any electric pot into a programmable thermostat.  
![overview][overview]

![working principle][principle]
![hardware setup][setup]

## Features
* display temperature
* temperature control via relay output
* program sequence of setpoints and timer durations
* load, edit and save new sequences (aka recipes)
* live plotting of temperature and setpoint vs. time
* logging of temperature, setpoint, PID output, heater state etc. in a database
* export of logs


## Getting started
Brew-o-matic was tested on x86-64, Raspberry Pi 4, Raspberry Pi 3 and Raspberry Pi 1.

### Prerequisites
On Linux, install docker and docker-compose. Make sure to search for Raspberry Pi specific instructions since the regular instructions won't work.

### Download the repository

Clone or checkout the repository, then start docker:
```console
# docker-compose -f brew-o-matic.yml up
```
If you're using a Raspberry Pi 1, then use the alternative docker-compose file:

```console
# docker-compose -f brew-o-matic_pi1.yml up
```
### Access Brew-o-matic
In a web browser, go to
```html
 <Your Server IP>:1880/ui
```

You can change the port of node-red in the docker compose file.

## Components of the docker-compose file:
- node-red + dependencies
- Brew-o-matic flow
- mosquitto (mqtt server)

## Hardware components you will need in addition:
- thermometer which can send it's values over mqtt
- relay which can be controlled through mqtt
- an electric pot (or a heating plate with a pot)

### Example setup
I run Brew-o-matic on a Raspberry Pi 1 and use a Sonoff TH10 with a DS18b20 temperature sensor connected through the 2.5 mm jack connector.

The Sonoff is flashed with [TASMOTA](https://github.com/arendst/Tasmota).

![Tasmota][TasmotaMain]

![TasmotaSetup][TasmotaSetup]


You need to edit the mqtt settings to point the Sonoff to the mqtt broker of this docker, or use your existing mqtt broker. In this case, you will also need to adapt the mqtt nodes in the flow.

Since Tasmota doesn't send the temperature values as often as we would like it, we actively poll the sensor every second by sending the payload "10" to the topic "cmnd/brew-o-matic/status" and we extract the temperature by subscribing to "stat/brew-o-matic/STATUS10".

Instead of actively polling the sensor value you can also change the telemtry frequency (aka teleperiod) using the following command on the Tasmota console:
```bash
TelePeriod 10
```



## User interface

Brew-o-matic offers a simple and an extended UI.
The different tabs and their functions are explained below.
If the tabs is part of the extended UI only, then this is indicated.

### Main Tab
Shows temperature, state of the heater (relay), timer and current step of the recipe.
If the setpoint temperature is reached (with or without having the thermostat engaged), the timer countdown starts.


![UI main tab][mainTab]
### Recipes
Allows you to display, start, stop and delete stored recipes.
To show list of available recipes, click on refresh.
![UI recipe tab][recipeTab]

A recipe is a sequence of temperature setpoint and duration, indicating how long a certain temperature should be held until the next step in the sequence is performed.

A recipe contains the following parameters:

Step | Setpoint | Duration | Alarm
--- | --- | --- | ---
mash in | 58 | 5 | 0
protein rest | 57 | 10 | 0
starch conversion 1 | 63 | 45 | 0
starch conversion 2 | 72 | 30 | 0
mash out | 78 | 10 | 1


### Manual
Instead of running a predefined recipe, you can also manually enter a setpoint and a timer duration.
Furthermore, you have  the option to engage the thermostat and let it control temperature.
If the thermostat is not engaged, you can manually control the heater state with the switch.

![UI manual tab][manualTab]

### Editor [extended]
![UI editor tab][editorTab]

If you load a existing recipe, you can edit the parameters and save it.
To enter a new recipe, click on reset. To add a new step in the recipe, add step.
To use the recipe, click on use protocol. Finally, if you click on save protocol, you can enter a name and save it for the next brewing session.

### Log [extended]
![UI log tab][logTab]

If you start a recipe, all parameters are logged to a sqlite database.
You can export and download the recorded values for each brewing session.
Choose the date in the calendar, display the values by clicking on show, and then download the log by clicking on download.

In addition, the Hot-o-meter shows a preview of the chosen recipe and will overlay the current temperature.

## Under the hood

### Temperature control
The Brew-o-matic uses a simple time-proportioned output for the relay.
``` javascript
if (currentTemp < (setpoint-5)) {
        //far from setpoint, keep heating
        pid_output = 1;
    } else if (currentTemp < (setpoint-2)){
        //closer to setpoint, slowdown heating
        pid_output = 0.8;
    } else if (currentTemp < (setpoint)){
        pid_output = 0.5;
    } else {
        pid_output = 0;
    }
```

The cycle time of the time-proportioned output is 30s. With the value of pid_output = 1, it will be on for the full cycle time, with the pid_output = 0.5 it will be on for half the cycle time and off for the other half of the cycle time and so forth.

### Recipes
The recipes are saved as csv files in a persistent storage located in
```bash
~/brew-o-matic/node-red/protocols
```
When you create and save new recipes through the UI, they will be saved in this location.


[overview]: ./documentation/images/Brew-o-matic_overview.jpg
 "Brew-o-matic overview"
[principle]: ./documentation/images/Brew-o-matic_working_principle.png
 "working principle"
[mainTab]: ./documentation/images/UI_tab_main.png "main tab"
[recipeTab]: ./documentation/images/UI_tab_recipe.png "recipe tab"
[manualTab]: ./documentation/images/UI_tab_Manual.png "manual tab"

[editorTab]: ./documentation/images/UI_tab_editor.png "editor"
[logTab]: ./documentation/images/UI_tab_Log.png "log"
[TasmotaMain]: ./documentation/images/Sonoff_Tasmota.png "Tasmota main"
[TasmotaSetup]:./documentation/images/Tasmota_DS18b20.png "Tamota config"
[setup]:./documentation/images/Brew-o-matic_hardware_small.jpg "Hardware setup"
