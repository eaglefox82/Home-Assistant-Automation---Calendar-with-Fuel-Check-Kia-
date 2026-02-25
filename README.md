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
*Update* calendar.your_calendar to your calendar entry in Home Assistant:- Example if your calendar is abc123@gmail.com you would see your calendar entry as calendar.abc123_gmail_com
*Update* home_location to your current address or even suburb.
What this does is creates a sensor which checks your calendar for the next event and grabs the location or falls back to your home if not location

Now to install the the required plugins: Waze Travel Time, Hyundai/Kia Connect. Both of these are downloaded via HACS and the Google Cast 

After downloading, you will need to add the integrations from *Settings -> Devices and Services -> Add integration* Search and add with the settings below

#### Waze Travel Time
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

