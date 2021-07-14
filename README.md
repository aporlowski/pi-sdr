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

### Setup, Update and Upgrade your OS

Setup the OS using the GUI guide to select a username, passworkd, etc.

Once you are logged into your desktop we first need to update the software and software repositories to ensure we have the latest software on our OS. Run the commands below to update and reboot.

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

First we will manually scan and tune FM nad NOAA Weather radio using GQRX to become familiar with SDR. We will use Bloomington, Indiana our example scanning location. 

1. Launch GQRX at the command line.

```
gqrx
```

2. Pick device RTL2838UHIDIR > OK

3. We will start with an easy signal to capture. FM radio, the same radio stations you listen to in your car. For our example we will use Bloomington's B96.7 WBWB found at 96.7 FM MHz. If you are not in Bloomgington you can substitute a channel from yourbroadcast area. Because FM is a widebandwidth transmission, it will be easier to find and tune correctly.  

4. GQRX takes all frequency inputs in kHz. So to get from 96.7 MHz to kHZ we need to multiply 96.7 by 1000 to get 96700 kHz. Input this number in the Frequency box under the Reciever tab. This is the target frequency the SDR will center its reception on.

5. Next Select `mode WFM (mono)` or `WFM (stereo)`. This mode selection that tells GQRX how to demondulate the radio signal. For example AM and FM are different modulation techniques and you won't hear hte correct audio output if you don't select the correct mode. You will notice that stereo provides two channels of sound whereas mono provides one channel. Depending on your reception quality one mode may sound better than the other.

> See [this page](https://gqrx.dk/doc/practical-tricks-and-tips#use) for more description of the various settings.

6. Push the `play` button in the top left corner. You should hear start to hear the radio station. You will see in the top half of hte screen a specturm analyzer, and in the bottom half a time plot of the signal strength for the sampled frequency range. The specturem analyzer measures the signal strength in dB. You will see a noise floor across the entire frequency range (probably between -70 to -100 depending on your reciever). Where there is a signal broadcast, you should see peaks of signals rise above the noise floor(probably between -80 and -20), these are broadcast radios signals.


**Picture**

In this plot time runs up and down along the y-axis, and frequency is the x-axis. The colors show the signal strehnth blue:low, yellow:medium, red:high. Yellow is typically suficient.

7. Next we will try a slightly more difficult signal to tune, NOAA Weatherradio. 

8. Push the `play` button again to stop the reception.

9. Change the frequency to your local NOAA Weather radio channel. In Bloomington, this is 162.450 MHz or 162450 kHz. You can find your local frequency from the [NOAA site](https://www.weather.gov/nwr/station_listing). 

10. Change the mode to `Narrow FM`. You will notice the grey bars on either side of the center frequency will shirnk. Narrow FM uses less bandwidth to broadcast thier signals. This allows more radio stations to share the spectrum, however, it decreases the quality of audio. This smaller bandwidth is still suitable for talking based audio, but would not perform well for music found on typical FM radio stations.

11. Push the `play` button. You should see a signal peak near your center frequency (red line). However, depending on your reciever, you may find it is not completely centered on the peak. This is due to slight inaccuracies that can exist in your reciever. As frequency increase, you will notice this inaccuracy will also become more sever. You can tune the frequency by hovering over the red bar until you have a bi-directional horizontal arrow and then drag the center frequency until you get a clear signal. You can also use the frequency input text box or arrows, or click the numbers in the black box under `Reciever Options`. Notice the audio quality of the NOAA radio is significantly less that the FM radio, this is due to the reduced bandwidth.
12. Notice much you have to move the frequency to get a clear signal. For example I have to move my frequency -3.5 kHz. This will not be a static number that works for all frequencies, as it will increaes as frequency does. Negative imples less (left) of the center frequency and a positive number imples greater (right).
13. Next, we will search for the P25 control channel for Bloomington.
14. Locate your P25 control channel by the [radioreference.com](https://www.radioreference.com/apps/db/?sid=8084) databse. The control channels are colored red. Search for bloomington and identify the control channel as `858.4875c` MHz. The channels in blue are alternate control channels, and hte other channels are voice channels used by the trunked radio stations.
15. Input the frequency as 858487.500 kHz
16. Keep the mode on `Narrow FM`
17. This channel is significantly harder to tune, as there is no voice audio to guid us. P25 is a digital broadcast, so the control channel is a consisting broadcast of what sounds like digital noise. See this [youtube video](https://www.youtube.com/watch?v=KtWhSuAL1_Q&t=5s) to get an idea of the sound we are looking for. Mine sounds similar to the video but with less of the screeching and a slower paced rhtymic thudding.
18. Also at this higher frequency your reciver error will be amplified. I have to tune -23.1 kHz to center on the true signal. This is quite far due to my cheap reciever, and in my case happens to be a slight less strong signal than the one that appears at the true frequency. Do not be tricked if you have a strong signal but not the correct noise. Investigatge all peaks nearby. 
19. Once you have identified the signal return the offset to zero and use the `ppm`found on the `Input` tab to align the correct signal on the center frequency. This will be a number we use later. In my case it is 28 ppm. Better SDR dongles should be significantly smaller. Tthe sound may no longer be the same digital noise, that is okay. Jot down this number for use later. 

### Configure and Launch OP25 Software to Listen to P25 Commuincation

1. Change to the op25 reciver application directory

```
$ cd Documents/op25/op25/gr-op25_repeater/apps/
```

2. Set the control channel frequency

```
./setTrunkFreq.sh 858.4875    (this modiefies op25.sh, but we may need to modify further for -q and -o)
```

3. Launch OP25 using the command below. Substitue your correct `ppm` offset after the `-q` option. In my example it is 28. Also replace the `-o` option with the ppm x 1000 as seen below.

```
./rx.py --nocrypt --args "rtl" --gains 'lna:36' -S 960000 -X -q 28 -o 28000 -v 1 -2 -V -U -T trunk.tsv 2> stderr.2
```

**EXPLAIN OPTIONS HERER**
							


