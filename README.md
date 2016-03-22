pimatic-edimax
=================

[![npm version](https://badge.fury.io/js/pimatic-edimax.svg)](http://badge.fury.io/js/pimatic-edimax)
[![Build Status](https://travis-ci.org/mwittig/pimatic-edimax.svg?branch=master)](https://travis-ci.org/mwittig/pimatic-edimax)

Pimatic Plugin for Edimax WiFi Smart Plugs.

Screenshot
-------------

Example of the device display as provided by the EdimaxSmartPlug

![screenshot](https://raw.githubusercontent.com/mwittig/pimatic-edimax/master/screenshot-1.png)

Configuration
-------------

You can load the plugin by editing your `config.json` to include the following in the `plugins` section. The property
 `interval` specifies the time interval in seconds for polling the state information of the Smart Plugs. For debugging
 purposes you can also set the property `debug`to `true`. For normal operation the latter is not recommended.

    { 
       "plugin": "edimax",
       "interval": 30
    }
    
Then you need to add a Smart Plug device in the `devices` section. The plugin offers two device types:
                                                                   
* EdimaxSmartPlugSimple: This type of device provides basic power switching capabilities (ON/OFF). 
* EdimaxSmartPlug: This type of device additionally provides power metering suitable for Edimax SP-2101W.

As part of the device definition you need to provide the `deviceName` and `password` which have been set using the 
EdiPlug app provided by Edimax. Note, the `deviceName` refers to the `Name` field of the plug settings.

    {
      "id": "sp1",
      "class": "EdimaxSmartPlug",
      "name": "Schaltsteckdose",
      "deviceName": "edimax",
      "host": "192.168.178.65",
      "password": "1234"
    }
       
Advanced Configuration
-------------

### Recover State
    
In my opinion Edimax Smart Plugs lack an essential feature, namely they do not fully recover their last state after a 
power failure. Say, the switch had been turned ON and you have power outage for a few minutes (you can simulate this by 
pulling the smart plug and plugging it to the mains socket again). In this case, the smart plug will remain OFF. How bad 
is this! To deal with this issue the `recoverState` feature (deactivated by default) has been added to automatically 
recover the state after a failure or pimatic has been started. Be warned, however: *Don't plug critical devices such 
as a freezer to the smart plug!* To enable the `recoverState` feature you need to set the property to true as 
shown below:

    {
      "id": "sp1",
      "class": "EdimaxSmartPlug",
      "name": "Schaltsteckdose",
      "deviceName": "edimax",
      "host": "192.168.178.65",
      "password": "1234",
      "recoverState": true
    }

### xLink and xAttributeOptions properties

If you wish to hide the sparkline (the mini-graph) of the attribute display or even hide an attributed this is possible 
 with pimatic v0.8.68 and higher using the `xAttributeOptions` property as shown in the following example. Using the 
 `xLink` property you can also add a hyperlink to the device display.
 
    {
        "id": "sp1",
        "class": "EdimaxSmartPlug",
        "name": "Schaltsteckdose",
        "deviceName": "edimax",
        "host": "192.168.178.65",
        "password": "1234",
        "recoverState": true
        "xLink": "http://fritz.box",
        "xAttributeOptions": [
            {
                "name": "energyToday",
                "displaySparkline": false
            },
            {
                "name": "energyWeek",
                "displaySparkline": false
            },
            {
                "name": "energyMonth",
                "hidden": true
            }
        ]
    }
    
TODO
----

* Investigate, whether or not it is possible to display and program switch schedules with pimatic

History
-------

* 20150413, V0.0.1
    * Initial Version
* 20150413, V0.0.2
    * Enhanced README
* 20150413, V0.0.3
    * Added support for power metering
    * Updated README
* 20150413, V0.0.4
    * Fixed package dependency which caused plugin startup to fail
* 20150416, V0.0.5
    * Improved robustness of the Smart Plug model detection. Now using bluebird-retry
    * Make sure polling is only performed if interval > 0
    * Allow for re-scheduling of updates if Smart Plug supports metering. This will trigger a new status 
      update request if `changeStateTo()` has been called. This way, metering values will be updated right away when the
      Smart Plug has been switched
    * Improved attribute change. Now, a change event is triggered only, if a value has actually changed rather than
      triggering the change event at each interval
    * Improved error handling. Now, errors will be logged properly.  
    * Updated to edimax-smartplug@0.0.6
    * README - fixed some typos
* 20150427, V0.0.6
    * Fixed description of attribute state 
    * Updated bluebird-retry to 0.0.4
    * Added screenshot
* 20150502, V0.0.7
    * Added support for `xOnLabel`, `xOffLabel`, `xLink`, and `xAttributeOptions` extensions as part of the device 
      configuration
    * Added `recoverState` feature
    * Energy values are now read from DB on plugin initialization
    * Reduced error log output. If `debug` is not set on the plugin, only new error states will be logged
    * Documentation of new features, added section on "Advanced Configuration" to README
* 20150508, V0.0.8
    * Fixed bug which caused blocking requests if smart plug not reachable
    * Updated dependencies
* 20150511, V0.0.9    
    * Updated package edimax-smartplug to enforce a default timeout of 20000 msecs to cleanup properly if 
      client is connected but server does not send a response.
* 20150529, V0.1.0
    * Added range checks for interval property. Update device and config schema
    * Adapt timeout to interval if interval is less than 20 secs
    * Disabled socket pooling
* 20150820, V0.2.0
    * Revised license information to provide a SPDX 2.0 license identifier as required by npm v2.1 guidelines on 
      license metadata - see also https://github.com/npm/npm/releases/tag/v2.10.0
    * Updated dependencies
* 20151004, V0.3.0
    * Fixed handling of plugin configuration default for interval
    * Fixed error handling of changeStateTo action. Return a rejected Promise with error message on error
* 20151230, V0.3.1
    * Added protocol debugging feature
    * Refactoring. Now using pimatic-plugin-commons
* 20151231, V0.3.2    
    * Bug fix: Updated "edimax-smartplug" to include fix for issue #1
    * Refactoring
* 20160106, V0.3.3    
    * Bug fix: Fixed handling of energy attributes to resolve issue #2
* 20160305, V0.3.4    
    * Dependency Updates
    * Added travis build descriptor
* 20160312, V0.3.5
    * Fixed typo in configuration example
* 20160322, V0.3.6
    * Fixed compatibility issue with Coffeescript 1.9 as required for pimatic 0.9 (thanks @sweebee)
    * Updated peerDependencies property for compatibility with pimatic 0.9