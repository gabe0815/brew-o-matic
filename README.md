# Brew-o-matic: a node-red based mash controller

![working principle][principle]

## Getting started
Brew-o-matic was tested on x86-64, Raspberry Pi 4, Raspberry Pi 3 and Raspberry Pi 1.

Clone or checkout the repository, then start docker:
```bash
docker-compose -f brew-o-matic.yml up
```
If you're using a Raspberry Pi 1, then use the alternative docker-compose file:

```bash
docker-compose -f brew-o-matic_pi1.yml up
```

## Components of the docker-compose file:
- node-red + dependencies
- brew-o-matic flow
- mosquitto (mqtt server)

## Hardware components you will need in addition:
- thermometer which can send it's values over mqtt
- relay which can be controlled through mqtt
- an electric pot (or a heating plate with a pot)

### Example setup
I run Brew-o-matic on a Raspberry Pi 1 and use a Sonoff Basic with a DS18b20 temperature sensor.

The Sonoff basic is flashed with [TASMOTA](https://github.com/arendst/Tasmota).

![Tasmota][TasmotaMain]

![TasmotaSetup][TasmotaSetup]

![TasmotaMQTT][TasmotaMQTT]

You need to edit the mqtt settings to point the Sonoff to the mqtt broker of this docker, or use your existing mqtt broker. In this case, you will also need to adapt the mqtt nodes in the flow.

Since Tasmota doesn't send the temperature values as often as we would like it, we actively poll the sensor every second by sending the payload "10" to the topic "cmnd/brew-o-matic/status" and we extract the temperature by subscribing to "stat/brew-o-matic/STATUS10".


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

A recipe is a sequence of temperature setpoints and durations, indicating how long a certain temperature should be held until the next step in the sequence is performed.

A recipe contains the following parameters:

Step | Setpoint | Duration | Alarm
--- | --- | --- | ---
mash in | 58 | 5 | 0
protein rest | 57 | 10 | 0
starch conversion 1 | 63 | 45 | 0
starch conversion 2 | 72 | 30 | 0
mash out | 78 | 10 | 1

![Recipe][recipe]

### Manual
Instead of running a predefined recipe, you can also manually enter a setpoint and a timer duration.
Furhermore, you havek  the option to engage the thermostat and let it control temperature.
If the thermostat is not engaded, you can manually control the heater state with the switch.

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

[principle]: ./documentation/images/Brew-o-matic_working_principle.png
 "working principle"
[mainTab]: ./documentation/images/UI_tab_main.png "main tab"
[recipeTab]: ./documentation/images/UI_tab_recipe.png "recipe tab"
[manualTab]: ./documentation/images/UI_tab_Manual.png "manual tab"

[recipe]: ./documentation/images/Recipe.png "recipe"
[editorTab]: ./documentation/images/UI_tab_editor.png "editor"
[logTab]: ./documentation/images/UI_tab_Log.png "log"
[TasmotaMain]: ./documentation/images/Sonoff_Tasmota.png "Tasmota main"
[TasmotaSetup]:./documentation/images/Tasmota_DS18b20.png "Tamota config"
[TasmotaMQTT]: ./documentation/images/Tasmota_mqtt.png "Tasmota mqtt"
