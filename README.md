# ESPhome for April Soil Moisture Sensor

This repository is an ESPHome configuration for the [April Soil Moisture Sensor](https://wiki.aprbrother.com/en/april_soil_moisture_sensor.html). This is primarily designed to work with HomeAssistant; however with a little tweaking it can report anywhere that ESPHome is able to report.

## Goals

The primary goals of this project are:
1. Ease of use for the enduser with little coding experience required. This is a great product at a cheap price - however, I could not find anywhere a proper explanation of how this device works (and hate programming in c++).
2. Redeployability. The code should work with minimum changes for multiple sensors.
3. Battery efficiency. The device has a 600mAh internal battery. I do not want to be pulling these in and out of my plants every month.
4. Simple management. 

## Files

- **default-template.yaml**: This file is the reusable template for all of your sensors.
- **testunit.yaml**: An example configuration for one of your sensors.
- **secrets.yaml**: An example of the secrets which contain passwords etc. Complete with your actual passwords.

## Usage

1. Install ESPHome e.g. `docker pull esphome/esphome` (Tested and working upto v2024.7.1)
2. Copy `default-template.yaml`, `testunit.yaml` and `secrets.yaml` to your ESPHome folder e.g. on HomeAssistant this is `/config/esphome/`
3. Configure your `secrets.yaml`
4. Connect device via USB to your ESPHome host. Install firmware. Click the dropdown next to testunit and install. N.B. Do not install the default-template it will fail (it is only the template configuration). N.B. The device should automatically reboot into CDC mode to receive the firmware however if it does not you may need to hold the IO0 button down while pushing the EN button (the two black buttons on the right of the PCB).
5. Within around 30s logs should become available in ESPHome (and a new device should appear in HomeAssistant if you are using that).
6. (Optional) Depending on the type of soil you should calibrate your soil sensor. See the theory of operation section.

You can duplicate `testunit.yaml` as many times as you wish just remember to change the name of your device as well e.g. `soil-sensor-1.yaml`->device-name: "soil-sensor-1"

## Operation

Once flashed push the black EN button, this should start the device up. It should run once per your wake settings then enter deep sleep and continue this cycle.

Theoretically, the devices are set and forget; however, if you forget to charge them and they turn off due to low power then you will need to push the EN button again once you have charged the device to restart it. This can be annoying, so just charge the devices when battery life starts showing around 10%. Please note that due to the nature of Lithium Ion chemistry this last 10% will likely only last a couple of days (while the other 90% seemingly last much longer comparatively).

If you have a spare like me, you can use the device as an "instant" tester - handy if you have a lot of household plants. I set the wake time to 5 mins and have it sleep for 1440 mins (1 day). Just push the black EN button whenever you want to start testing.

Finally, the battery % will not be accurate when you have the device charging. This will read the charging voltage (which will inevitably always show 100%). Just charge the devices until the red light turns itself off.

## Recommendations

1. Set the `run_duration` and `sleep_duration` to something sensible e.g. 1min run and 360min sleep. With this configuration the device should last around 6 months between charges (n.b. this is based upon my own device which has 60% battery life after 3 months). N.B. Reporting constantly the battery life is around 1 day. 1 Year Update: The battery life indeed seems to be around 6 months per probe with 1 min run and 360 min (6 hour sleep time); however, this only seems to apply to my indoor sensors. The sensors that are outdoors are subject to greater heat cycling (ambient temperature swings are greater) only last around 4 months or so - this is normal and I assume part of lithium ion battery chemistry. One of my sensors which is not shaded and is subject to large temperature swings >40C during the day (could be more due to the black case) has lost significant capacity in its battery - you should be able to find plenty of replacement batteries online just look up `503035 battery` - this is the dimensions of the battery 5mm x 30 mm x 35 mm.
2. I recommend applying a layer of 2 part epoxy to the probe section. The varnish layer applied to the probe section is very thin and likely to degrade significantly after pulling and pushing the probe into the soil over time. This can be done simply by dipping the probe into 2 part epoxy or "painting" it on. 1 Year Update: I didn't do this to one of my sensors and can report no ill effects. There is no corrosion of the probe section and no obvious damage. Obviously, your results may vary if you are pulling out your probes frequently (I'm only doing it when they need charging). In any event, this is probably a good safety measure.
3. If using outdoors in well drained areas, a simple method to protect the device against water is to place the unit in a ziplock bag and push the probe in a hole through the bag. This will not make the device water proof but has worked for me outdoors even in quite heavy rain as long as your soil is well drained (and doesn't immerse the device in water). Further care probably needs to be taken in poorly draining areas and areas subjected to snow or torrential rain. 1 Year Update: I still recommend this method but care should be taken when choosing a zip lock bag. Do not use a biodegradable bag (this will disintegrate in under 3 months). I prefer to use ziplock bags that are designed for storing items (rather than sandwich bags) - these are thicker plastic and less likely to degrade due to UV exposure. Please note that it is likely that these plastics will still degrade and need replacing every time you charge the device.
4. April Brother has released a v2 of the case which includes a neat cutout for the USB port - thank you!  I would recommend using some high quality tape and taping over the USB port to prevent water ingress if using v2 of the case. The rest of this item is kept for people who have the older version. When using the above ziplock method, I have used a dremel tool to cut a slot to expose the usb port outside of the case. Dear April Brother, if you read this, PLEASE PLEASE PLEASE swap the location of the battery terminal and the usb port. This places the USB port 3-4 cm above ground level and less likely to be a point of water ingress.

## Theory of Operation

[Theory of Operation](theory.md)

## To do

1. Enable the temperature sensor.
2. Calibrate the moisture reading with temperature.
3. Calibrate the moisture reading with battery voltage. Theoretically the lower the voltage the lower the moisture reading.
4. Automate OTA updates to the device. This isn't a priority for me since the device is mostly set and forget.

## Power Consumption

Based upon my limited testing, here are my observations regarding power consumption:
- Deep Sleep: ~0.1mA
- Initial wake up from Deep Sleep: ~80mA
- Idle (on but not updating any sensors): ~25mA
- Idle (http portal enabled): 
- Updating sensors (except moisture): ~50mA
- Updating moisture levels: ~80mA

It can be seen that updating moisture levels constantly would consume the entire battery within 4-5 hours. I use these sensors largely as set and forget (except every 6 months when I need to charge them) - accordingly, I recommend using sensible run/ deep sleep times and sensor update times (battery and temperature could be updated only once every 20s so that you only get those a few times when the device is awake.)

## Upstream Issues

If you are messing with the yaml configurations outside of changing the presets in `testunit.yaml` and `secrets.yaml` you may encounter some build errors. This appears to be an upstream issue in ESPHome that can be resolved using the "Clean Build Files".

~~This build will fail on ARM architectures (e.g. Raspberry Pi) because the upstream esp-idf in platformio hasn't been built for AArch64 as of writing (February 23).~~As of ESPHome 2023.10.6 (at the latest), this builds correctly on AArch64. I have not updated my soil sensors though because there is no need to do so.

## Acknowledgements

Volca: [The original inspiration for building this.](https://github.com/volca/april-soil-esphome-module)

[Hrisko, J. (2020). Capacitive Soil Moisture Sensor Calibration with Arduino. Maker Portal.](https://makersportal.com/blog/2020/5/26/capacitive-soil-moisture-calibration-with-arduino)

[Kizito, F. et al (2008) Frequency, electrical conductivity and temperature analysis of a low-cost capacitance soil moisture sensor](https://www.sciencedirect.com/science/article/abs/pii/S0022169408000462)
