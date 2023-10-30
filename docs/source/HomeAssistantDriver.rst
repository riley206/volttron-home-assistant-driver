.. _HomeAssistant-Driver:

Home Assistant Driver
=====================

The Home Assistant driver enables VOLTTRON to read any data point from any Home Assistant controlled device.
Currently control(write access) is supported only for lights(state and brightness) and thermostats(state and temperature).

The following diagram shows interaction between platform driver agent and home assistant driver.

.. mermaid::

   sequenceDiagram
       HomeAssistant Driver->>HomeAssistant: Retrieve Entity Data (REST API)
       HomeAssistant-->>HomeAssistant Driver: Entity Data (Status Code: 200)
       HomeAssistant Driver->>PlatformDriverAgent: Publish Entity Data
       PlatformDriverAgent->>Controller Agent: Publish Entity Data

       Controller Agent->>HomeAssistant Driver: Instruct to Turn Off Light
       HomeAssistant Driver->>HomeAssistant: Send Turn Off Light Command (REST API)
       HomeAssistant-->>HomeAssistant Driver: Command Acknowledgement (Status Code: 200)

Driver Config
=============

In the driver config file you must enter your Home Assistant IP, access token, and port. Those can be found `here <https://developers.home-assistant.io/docs/auth_api/#long-lived-access-token>`_.
Also make sure registry_config points to the name of your registry stored in the configuration store. 

Here is an example configuration file. 

.. code-block:: json
{
   "driver_config": {
       "ip_address": "Your Home Assistant IP",
       "access_token": "Your Home Assistant Access Token",
       "port": "Your Port"
   },
   "driver_type": "homeassistant",
   "registry_config": "config://light.example.json",
   "interval": 30,
   "timezone": "UTC"
}

Home Assistant Registry file. 
=============

The registry file is a json file. Each JSON object relates to a state or attribute of a single device in Home Assistant. The Entity ID field is the entity we want to get, the entity point is the point that we want (such as state, brightness, or temperature), and the Volttron Point Name is the name that will display within VOLTTRON. This can be the same as the entity point if you use a seperate config and registry for each device. If you want to use one registry and one config for your entire setup, then you will need to create unique VOLTTRON point names. For example, if you have two lights that pull brightness, you might do something like brightness1 amd brightness2.

.. code-block:: json

   [
       {
           "Entity ID": "light.example",
           "Entity Point": "state",
           "Volttron Point Name": "light_state",
           "Units": "On / Off",
           "Units Details": "on/off",
           "Writable": true,
           "Starting Value": true,
           "Type": "boolean",
           "Notes": "lights hallway"
       },
       {
           "Entity ID": "light.example",
           "Entity Point": "brightness",
           "Volttron Point Name": "light_brightness",
           "Units": "int",
           "Units Details": "light level",
           "Writable": true,
           "Starting Value": 0,
           "Type": "int",
           "Notes": "brightness control, 0 - 255"
       }
   ]