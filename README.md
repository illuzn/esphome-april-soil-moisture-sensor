# ESPhome for April Soil Moisture Sensor

This repository is an ESPHome configuration for the [April Soil Moisture Sensor](https://wiki.aprbrother.com/en/april_soil_moisture_sensor.html)

## Goals

The primary goals of this project are:
1. Ease of use for the enduser with little coding experience required. This is a great product at a cheap price - however, I could not find anywhere a proper explanation of how this device works (and hate programming in c++).
2. Redeployability. The code should work with minimum changes for multiple sensors.
3. Battery efficiency. The device has a 600mAh internal battery. I do not want to be pulling these in and out of my plants every month.
4. Simple management. 

## Files

**default-template.yaml**: This file is the reusable template for all of your sensors.
**testunit.yaml**: An example configuration for one of your sensors.
**secrets.yaml**: An example of the secrets which contain passwords etc. Complete with your actual passwords.

## Usage

1. Install ESPHome e.g. `docker pull esphome/esphome`
2. Copy `default-template.yaml`, `testunit.yaml` and `secrets.yaml` to your ESPHome folder e.g. on HomeAssistant this is `/config/esphome/`
3. Configure your `secrets.yaml`
4. Install firmware. Click the dropdown next to testunit and install. N.B. Do not install the default-template it will fail (it is only the template configuration).
5. Within around 30s logs should become available in ESPHome (and a new device should appear in HomeAssistant if you are using that).
6. (Optional) Depending on the type of soil you should calibrate your soil sensor. See the theory of operation section.

You can duplicate `testunit.yaml` as many times as you wish just remember to change the name of your device as well e.g. `soil-sensor-1.yaml`->device-name: "soil-sensor-1"

## Recommendations

1. Set the `run_duration` and `sleep_duration` to something sensible e.g. 1min run and 360min sleep. With this configuration the device should last around 6 months between charges (n.b. this is based upon my own device which has 60% battery life after 3 months). N.B. Reporting constantly the battery life is around 1 day.
2. I recommend applying a layer of 2 part epoxy to the probe section. The varnish layer applied to the probe section is very thin and likely to degrade significantly after pulling and pushing the probe into the soil over time. This can be done simply by dipping the probe into 2 part epoxy or "painting" it on. 
3. If using outdoors in well drained areas, a simple method to protect the device against water is to place the unit in a ziplock bag and push the probe in a hole through the bag. This will not make the device water proof but has worked for me outdoors even in quite heavy rain as long as your soil is well drained (and doesn't immerse the device in water). Further car probably needs to be taken in poorly draining areas, areas subjected to snow or torrential rain.
4. When using the above ziplock method, I have used a dremel tool to cut a slot to expose the usb port outside of the case. Dear April Brother, if you read this, PLEASE PLEASE PLEASE swap the location of the battery terminal and the usb port. This places the USB port 3-4 cm above ground level and less likely to be a point of water ingress.

## Theory of Operation

[Theory of Operation](theory.md)

## To do

1. Enable the temperature sensor.
2. Calibrate the moisture reading with temperature.
3. Automate OTA updates to the device. This isn't a priority for me since the device is mostly set and forget.

## Upstream Issues

If you are messing with the yaml configurations outside of changing the presets in `testunit.yaml` and `secrets.yaml` you may encounter some build errors. This appears to be an upstream issue in ESPHome that can be resolved using the "Clean Build Files".

## Acknowledgements
