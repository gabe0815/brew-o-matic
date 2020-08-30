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
- a heating plate + pot (or an electric pot)


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
Also you have the option to engage the thermostat and let it control temperature.
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
You can export and download the recorded values for each brewing session, by choosing the date.

The Hot-o-meter shows a preview of the chosen recipe and will overlay the current temperature.


[principle]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/Brew-o-matic_working_principle.png
 "working principle"
[mainTab]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/UI_tab_main.png "main tab"
[recipeTab]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/UI_tab_recipe.png "recipe tab"
[manualTab]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/UI_tab_Manual.png "manual tab"

[recipe]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/Recipe.png "recipe"
[editorTab]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/UI_tab_editor.png "editor"
[logTab]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/UI_tab_Log.png "log"
