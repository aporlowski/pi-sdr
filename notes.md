# Notes

## GQRX

- GQRX does not do AM by default. It requires some tuning to get below 22.0 MHZ. See [reddit thread](https://www.reddit.com/r/RTLSDR/comments/ksmv9f/cant_listen_to_am_radio_broadcasts_with_nooelec/) and [user report](http://adrianchadd.blogspot.com/2017/06/gqrx-direct-sampling-configuration.html?m=1)

## NOAA

- [IN NOAA Weather Radio Channels](https://www.weather.gov/nwr/stations?State=IN)
- Bloomington is 162.450 MHz, narrow FM demod

## FM

- Found harmonics for 96.7 (31.94 MHz) and B 96.1 (32.5)

## DMR

- UK equiavlant of P25 (Europe?) fully encrypted in BBC, so not able to listen to it.


## Unorganized

- change the sample rate on gqrx for improved performance
- relook LNA and gain description was not the same for recommended dongle
- noaa weather coverage maps
- most recently 162.500 weather (georgia, south of bedford) was working not 162.45
- get what you pay for when it comes to SDR and perhaps antennas
- update components and add antenna https://www.amazon.com/LM-YN-Omnidirectional-Compatible-Frequency/dp/B01N6GO584/
-  remove small antenna recommendation (doesn't work well with pi-top, too much noise)

## Docker Commands
- launch trunk-recorder as docker container `sudo dockerun -it   --privileged   -v /home/anthony/trunk-recorder:/app   -v /var/run/dbus:/var/run/dbus   -v /var/run/avahi-daemon/socket:/var/run/avahi-daemon/socket   robotastic/trunk-recorder:latest`
- shell to running container `sudo docker exec -it a22c638fb4cb bash`
- commit and save new state as new image `sudo docker commit 7da2253ce59b trunk-recorder-sample`
- launch new state image `sudo docker run -it   --privileged   -v /home/anthony/trunk-recorder:/app   -v /var/run/dbus:/var/run/dbus   -v /var/run/avahi-daemon/socket:/var/run/avahi-daemon/socket   trunk-recorder-sample`
- copy file from container to host `sudo docker cp 7da2253ce59b:/home/anthony/trunk-recorder/bloom/2021/7/25/21256-1627238771_851962500.wav ./`
- list running containers `sudo docker ps`
- list available images `sudo docker images`
- kill a specific container `sudo docker kill CONTID`
- list rtl devices `rtl_eeprom -d 0` `rtl_eeprom -d 1`
- change rtl serial device `rtl_eeprom -s 00000001`



