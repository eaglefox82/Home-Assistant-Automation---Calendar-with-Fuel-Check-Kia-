# Home-Assistant-Automation---Calendar-with-Fuel-Check-Kia-
YMAL and Setup for Home Assistant for triggering Notifications on Home Assistant 

This is a guide for setting up Home Assistant for triggering notifications based on your calendar, travel disatance and time from the Waze integration and looking at the driving range from the Kia integration.

This guide also assumes you know how to edit your ```configuation.ymal``` file

***
## Requirements
-----
* Home Assistant Community Store (HACS) installed
* * Follow guide here if not installed  [Guide](https://www.hacs.xyz/docs/use/download/download/#to-download-hacs-ossupervised)
* Way to edit ```configuation.ymal``` - Goolge can help here
* Calendar Integrated into Home Assistant. I use Google Calendar, please google guides for your calendar provider
* Home assistant app installed on your phone
* Your Phone has been assigned to you under the Persons
* Google Nest Speakers setup in your Google Home account - Please google for other types

***
## Setup
-----
As the Waze Travel Time integration requires both a origin and destination, we need to create a dymanic sensor that Waze Travel time can read.
Add the following to your ```configuation.ymal``` file
```
template:
  - sensor:
      - name: "Next Event Location"
        unique_id: next_event_location_with_fallback
        # This state will be used by the Waze sensor as the destination
        state: >
          {% set loc = state_attr('calendar.your_calendar', 'location') %}
          {% if loc is not none and loc | trim != "" %}
            {{ loc }}
          {% else %}
            home_location
          {% endif %}
        attributes:
          is_calendar_event: "{{ state_attr('calendar.you_calendar', 'location') is not none }}"
```
**_Update_** calendar.your_calendar to your calendar entry in Home Assistant:- Example if your calendar is abc123@gmail.com you would see your calendar entry as calendar.abc123_gmail_com

**_Update_** home_location to your current address or even suburb.
What this does is creates a sensor which checks your calendar for the next event and grabs the location or falls back to your home if not location

Now to install the the required plugins: Waze Travel Time, Hyundai/Kia Connect. Both of these are downloaded via HACS and the Google Cast 

After downloading, you will need to add the integrations from *Settings -> Devices and Services -> Add integration* Search and add with the settings below

### Waze Travel Time
Origin = Sensor.your_phone_geocoded_location - Check your device in Home Assistant to grab sensor name
Destination = sensor.next_event_location - This is the name from the sensor we added to the ```configuration.ymal``` file
Region = Australia

What this integration does is grabs your phones location, and the location of the next event and calculates the required travel time

### Hyundai / Kia Connect
Once installed, log in with your username, password and pin which you have setup. These can be reset from the App if you have forgotten

This integration gives use all the information we see in the app, plus we can trigger some actions as well ;) something to look at later

### Google Cast
No major setup here, as should auto discover on your network

Please check your entires for this integrations which makes the next points easier

## Automation
-----
Click on the following:
Settings -> Automations & Scenes -> Create automation
Click on the 3 dots at the top right and select "Edit in YAML"
This will open the code view.
Copy the code from the ```automation.ymal``` file and paste - Don't download and upload, just open, copy and paste

There are a couple of updates required in the script

There is the following which require you update

```actions:
 - variables:
      warn_1_mins: 30   #<-- Sets the Time Prior to the first warning - default is 30 mins
      warn_2_mins: 5 #<-- Sets the time prior to the 2nd warning - default is 5 mins
      fuel_buffer_mins: 15 #<-- Sets the fuel time buffer - the time added to travel time to add fuel - default is 15 mins
      my_calendar: your_calendar #<-- You Calendar Entires
      my_speakers: media_player.speaker_group #<-- Google Home speaker group you wish to play the notification on
      my_kia_range: sensor.tasman_total_driving_range #<-- Double check this, this is the sensor for total driving range I had for my Tasman (will be different for other models)
      my_notify_service: notify.mobile_app_your_mobile #<-- change your_mobile to the phone listed in devices
      my_tts_engine: tts.google_translate_en_com_au #<-- This is Australian, again check the cast sensors
      my_person: person.you #<-- Update you to your name in Home Assistant
```

Note: If you add "Public Transport" to the desciption of your Calendar entry. The driving range is not calucated
