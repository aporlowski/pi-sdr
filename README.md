# pi-sdr

Turn your Raspberry Pi into a portable P25 radio reciever using software defined radio (SDR). We demonstrate its use to listen to FM radio, NOAA Weather Radio, and Project 25 (P25) emergency-use radio channels.

This tutorial will be a comprehensive introduction to Raspberry Pi single-board computers (SBC), software defined radio, and P25 radio.

Teachers and students can use this tutorial to learn about these concepts through an hands-on project. Concerned citizens, journalists, and Virtual Operations Support Team (VOST) members can use this tutorial to setup a radio to monitor emergency radio traffic in their area.

P25 recievers on the market, such as the Bluetail Technolgies P25rx, typically cost $250 or more. This can be prohibitively expensive for individuals interested in monitoring emergency radio communication. Some webistes, such as [openmhz.com](https://openmhz.com/systems) provide recorded P25 communications traffic, however coverage for your area is not guaranteed. With a cheap SDR dongle and a computer such as the Raspberry Pi, you can set up your own P25 scanner. Building your own P25 scanner is a fun hands-on project to learn about P25, SDR, and single-board computers. 

> See this [site](https://wiki.radioreference.com/index.php/APCO_Project_25#Scanner_Support_FDMA_and_TDMA) for a list of P25 scanners and thier supported technology (phase I, II, etc) 

## Components



### Portable Model

These components provide a portable Rasbperry Pi based radio including a case, battery, built-in speaker, small utility screen, SDR dongle, and antenna. Bluetooth and audio jacks from the Pi provide audio output, and wifi allows configuration via secure shell (ssh) from any computer.

- [Raspberry Pi 4 Model B Starter Kit 8GB](https://www.amazon.com/dp/B08DJ9MLHV?psc=1&smid=A30ZYR2W3VAJ0A&ref_=chk_typ_imgToDp) $94.99 (cheaper, smaller RAM models should work as well)
- [Samsung EVO 32 GB micro SD card](https://www.amazon.com/dp/B06XWN9Q99?psc=1&smid=ATVPDKIKX0DER&ref_=chk_typ_imgToDp) $7.49
- [PI-TOP [4] DIY Edition Case](https://www.amazon.com/dp/B08N6B8M1H) $99.95
- [RTL-SDR Blog V3 Dongle and Antenna Kit](https://www.amazon.com/RTL-SDR-Blog-RTL2832U-Software-Defined/dp/B011HVUEME/) $39.95 (in the tutorial below [this](https://www.amazon.com/gp/product/B00UAB79WG/) cheaper $ 19.99 SDR dongle was used instead. The reccommended dongle should provide better radio reception with less manual frequency corerection and less frequency drift compared to a cheaper dongle. It should also require no deviation from the tutorial)
- **Total $242.38 not including tax, or $139.9 with existing Pi and SD card** (You can redudece the price by $40 or more by using cheaper Raspberry Pi's and SDR dongles)

### Car Plug in Model

If you simply want to plug your Pi radio into a car (or other USB power source and bluetooth speaker), then you will only need your Pi, the SDR dongle and antenna kit, and an inexpensive case.

- [Raspberry Pi 4 Model B Starter Kit 8GB](https://www.amazon.com/dp/B08DJ9MLHV?psc=1&smid=A30ZYR2W3VAJ0A&ref_=chk_typ_imgToDp) $94.99 (cheaper, smaller RAM models should work as well)
- [Samsung EVO 32 GB micro SD card](https://www.amazon.com/dp/B06XWN9Q99?psc=1&smid=ATVPDKIKX0DER&ref_=chk_typ_imgToDp) $7.49
- [Raspberry Pi 4 Case iUniker] (https://www.amazon.com/iUniker-Raspberry-Aluminium-Heatsink-Supply/dp/B07D3S4KBK/) $10.95
- [RTL-SDR Blog V3 Dongle and Antenna Kit](https://www.amazon.com/RTL-SDR-Blog-RTL2832U-Software-Defined/dp/B011HVUEME/) $39.95 
- **Total $153.38 not including tax, $50.90 with existing Pi and SD card**

### Minimum Required Hardware

With an existing computer running Linux you simply need and SDR dongle and antenna to begin monitoring P25 radio communications. 

- [RTL-SDR Blog V3 Dongle and Antenna Kit](https://www.amazon.com/RTL-SDR-Blog-RTL2832U-Software-Defined/dp/B011HVUEME/) $39.95 (in the tutorial below [this](https://www.amazon.com/gp/product/B00UAB79WG/) cheaper $ 19.99 SDR dongle was used instead. The reccommended dongle should provide better radio reception, and require no deviation from the tutorial)
- **Total $19.99-39.99 not including tax**

## Setup

### Install Operating System on Raspberry Pi

1. Download the Raspberyy Pi Imager [software](https://www.raspberrypi.org/software/)
2. Insert your micro SD card into an SD card reader and plug into your laptop
3. Launch the Pi Imager software
4. `Choose OS` > `Other General Purpose OS` > `Ubuntu` > `Ubuntu Desktop 21.04 (RPI 4/400)`
5. `Choose Storage` > select your SD card
6. `Write`

You know have Ubuntu Desktop installed on your Pi. Insert the SD card into the Pi and connect a keyboard, mouse, monitor, and finally the power supply.

### Update and Upgrade your OS

We need to update the software and software repositories to ensure we have the latest software on our OS. Run the commands below to update and reboot.

```sudo apt update```

`sudo apt full-upgrade`

`sudo reboot`

### Install the [GQRX](https://gqrx.dk/) Open Source SDR Reciever Software

1. Follow the GQRX [installation instructions](https://gqrx.dk/download/install-ubuntu) repeated below
2. First we need to remove existing installations of the software. There should be none so you can skip these commands or answer `n` to these commands if using a fresh OS.

```
sudo apt-get purge --auto-remove gqrx      
sudo apt-get purge --auto-remove gqrx-sdr    
sudo apt-get purge --auto-remove libgnuradio*   
```

3. Next we need to add multip Personal Package Archive to install the software. GQRX and its required components are distributed by its authors in PPA instead of using standard Ubuntu repositories. This gives the authors more control over the software repository, but requires we add the repositories to our OS so the software can be located.

```
sudo add-apt-repository -y ppa:bladerf/bladerf
sudo add-apt-repository -y ppa:myriadrf/drivers
sudo add-apt-repository -y ppa:myriadrf/gnuradio
sudo add-apt-repository -y ppa:gqrx/gqrx-sdr
sudo apt-get update
```

4. We can now install the GQRX software using apt-get

```
sudo apt-get install gqrx-sdr
```
5. Next we will install the libvolk2-bin profiler to optimize our machines radio processing 

```
sudo apt-get install libvolk2-bin   (replaced libvolk1-bin)
volk_profile
```

This creates a congiuration file to `$HOME/.volk/volk_config` that contains the best architecture and function to process radio signals on your computer. 

### Install OP25 Software

1. First we need to install Git so we can download the software.

```
sudo apt install git
```

2. Next we clone the [boatbod fork](https://github.com/boatbod/op25.git) of the [OP25 software](http://osmocom.org/projects/op25/wiki)

```
cd Documents
git clone https://github.com/boatbod/op25.git
```

3. Now we install the software using the provided script

```
cd op25
./install.sh
sudo reboot
```

### Scan for Signals using GQRX

Launch GQRX
gqrx
pick device RTL2838UHIDIR
OK

step 1 listen to fm radio
96700 kHz
mode WFM (mono) or WFM (stereo)

search for red control channel listed here https://www.radioreference.com/apps/db/?sid=8084
in this case it appears to be 858.4875c
input freq and press "play" e.g. start dsp


the freq may be off (mine was off by 24 khz) 
on narrow fm it should sound like digital noize (see youtube video)
ppm or khz offset


./setTrunkFreq.sh 858.4875    (this modiefies op25.sh, but we may need to modify further for -q and -o)

./rx.py --nocrypt --args "rtl" --gains 'lna:36' -S 960000 -X -q 28 -o 28000 -v 1 -2 -V -U -T trunk.tsv 2> stderr.2


#########################OBSOLETE###################

cd ./op25/op25/gr-op25_repeater/apps
./rx.py --args 'rtl' -N 'LNA:47' -S 2500000 -x 2 -f 858.4875e6 -o 17e3 -q 2


maybe this 858463.900 (tuned in gqrx)
or 858983.100 (labeled as hardware freq) 


anthony@anthony-pi:~/Documents/op25/op25/gr-op25_repeater/apps$ ./op25.sh
./op25.sh: line 1:  2677 Aborted                 (core dumped) ./rx.py --nocrypt --args "rtl" --gains 'lna:36' -S 960000 -X -q 0 -v 1 -2 -V -U -T trunk.tsv 2> stderr.2


this is the new way
./setTrunkFreq.sh 858.4875									
./op25.sh
										


