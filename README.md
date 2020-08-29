# Brew-o-Matic: a node-red based mash controller

## Components of the docker-compose file:
- node-red + dependencies
- brew-o-matic flow
- mosquitto (mqtt server)

## Hardware components you will need in addition:
- thermometer which can send it's value over mqtt
- relay which can be controlled through mqtt
- a heating plate + pot (or an electric pot)

## Brew-o-matic modes
The brew-o-matic offers two modes:

### Simple
The brew-o-matic receives temperature values over mqtt and displays them.
The user can manually define a setpoint and a timer, choose if the thermostat should be engaded or manually toggle the relay output.
If the setpoint temperature is reached (with or without having the thermostat engaged), the timer countdown starts.

### Extended
In addition to the features of the simple mode, the extended mode offers a programmable thermostat.
It allows you to enter, edit, save and load recipes (protocols), in which you can define the following parameters:
- step name
- temperature setpoint
- duration
- alarm

To start, you load (or enter) a recipe and start the program.
The setpoint is changed to the first step and the thermostat is engaged.
Once the setpoint is reached, the timer starts to count down. If the timer elapses,
the setpoint of the next step is loaded until the recipe if finished.
If alarm == 1, then an alarm sound is played, notifying you in case you need to add an ingredient etc.

If you start a recipe, all parameters are logged to a sqlite database.
You can export and download the recorded values for each brewing session, by choosing the date.

![working principle][principle]

[principle]: /home/gabe/oldalbert/mnt/4TBraid_01/sync/Projects/homebrew/brew-o-matic/documentation/images/Brew-o-matic_working_principle.png
 "working principle"
