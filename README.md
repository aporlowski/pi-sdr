# pi-sdr

Turn your Raspberry Pi into a portable radio reciever using software defined radio (SDR). We demonstrate its use to listen to FM radio, NOAA Weather Radio, and Project 25 (P25) emergency services communications.

## Introduction

This tutorial will be a introduction to Raspberry Pi single-board computers, software defined radio, and P25 radio.

Teachers and students can use this tutorial to learn about these concepts through an hands-on project. Concerned citizens, journalists, and Virtual Operations Support Team (VOST) members can use this tutorial to setup a radio to monitor emergency radio traffic, NOAA weather radio, or other FM radio in their area.

P25 recievers on the market, such as the [Bluetail Technolgies P25rx](https://bluetailtechnologies.com/products/p25rx-digital-police-receiver), typically cost $250 or more and are relatively single use when compared to the Raspberry Pi which provides a full computer and can be repurposed for multiple projects. The initial cost of recievers can be prohibitively expensive for individuals interested in monitoring emergency radio communication. Some webistes, such as [openmhz.com](https://openmhz.com/systems) provide real-time and recorded P25 communications traffic, however coverage for your area is not guaranteed. With a cheap SDR dongle and a computer such as the Raspberry Pi, you can set up your own P25 scanner. By presenting a gentle introduction to the field of SDR, this tutorial can enable and encourage more people to implement P25 trunk recording, such as that found on openmhz.com, to make emergency communications more acessbile to the public. Building your own P25 scanner is a fun hands-on project to learn about P25, SDR, and single-board computers.

> See this [site](https://wiki.radioreference.com/index.php/APCO_Project_25#Scanner_Support_FDMA_and_TDMA) for a list of P25 scanners and thier supported technology (phase I, II, etc) 

## Components

In the sections below we provide components recommended to build a Raspberry Pi based radio with different use-cases in mind.

### Portable Model

These components provide a portable Rasbperry Pi based radio including a case, battery, built-in speaker, small utility screen, SDR dongle, and antenna. Bluetooth and audio jacks from the Pi provide audio output, and wifi allows configuration via secure shell (ssh) from any computer. Alternatively you can attach a monitor, keyboard, and mouse to make configuration changes.

- [Raspberry Pi 4 Model B Starter Kit 8GB](https://www.amazon.com/dp/B08DJ9MLHV?psc=1&smid=A30ZYR2W3VAJ0A&ref_=chk_typ_imgToDp) $94.99 (cheaper, smaller RAM models should work as well)
- [Samsung EVO 32 GB micro SD card](https://www.amazon.com/dp/B06XWN9Q99?psc=1&smid=ATVPDKIKX0DER&ref_=chk_typ_imgToDp) $7.49
- [PI-TOP [4] DIY Edition Case](https://www.amazon.com/dp/B08N6B8M1H) $99.95
- [USB-C PD power supply](https://www.amazon.com/ZMI-zPower-Turbo-Power-Adapter/dp/B07D64QLQ1/) $19.99 capable of 12v or 15v 3A output. 
- [Nooelec NESDR Smart v4 Bundle](https://www.amazon.com/gp/product/B01GDN1T4S/) $41.95 ([this](https://www.amazon.com/gp/product/B00UAB79WG/) cheaper $19.99 SDR dongle was validated with the tutorial below (althout it is significantly more difficult to tune; see notes in tutorial . The reccommended dongle should provide better radio reception with less manual frequency corerection and less frequency drift compared to the cheaper dongle.)
- [LM YN LTE 4G Omnidirectional Antenna](https://www.amazon.com/gp/product/B01N6GO584/). $6.99 This provides a more portable form factor antenna for 800 Mhz P25 scanning demonstrated below, but is not required since the Nooelec comes with antenns.
- **Total $271.36 not including tax, or $168.86 with existing Pi and SD card** (You can redudece the price by $40 or more by using cheaper Raspberry Pi's and SDR dongles)

Wihle it is near the top-end of the price range for Pi cases, the pi-top[4] is a good fit for this project becuase it includes a battery, speaker, OS soft-shutdown via button press (to prevent SD card corruption without needing keyboard, monitor, or mouse to shutdown), and a utility screen that provides battery level and CPU monitoring. The pi-top[4] case is a well designed case for use in education or project settings, such as building robots, sensors, and more. It has the added benefit of exposing all GPIO pins and ports of the raspberry pi, so you can extend your pi to do multiple other projects if desired. It also has an attachable touch screen and keyboard that you can add to make your pi-radio fully configurable in a stand-alone manner. 

> You can buy a pi-top[4] with a 4 GB Raspberry Pi 4 pre-installed from thier website. See the `pi-top[4] complete` kit. The `pi-top[4] diy` case was reccommended becuase it is assumed many users will already have a Raspberry Pi to begin with, it allows you the option to use an 8GB RAM model, and it is cheaper. Note the DIY edition does not come with the required USB-C PD 12v or 15v power supply. These are the type used for a modern laptop charger. Your 5v phone charger or Raspberry Pi charger will not work. I found a Nintendo Switch power supply works as well.

### Car Plug in Model

If you simply want to plug your Pi radio into a car (or other USB power source and bluetooth accessible speaker), then you will only need your Pi, the SDR dongle and antenna kit, and an inexpensive case.

- [Raspberry Pi 4 Model B Starter Kit 8GB](https://www.amazon.com/dp/B08DJ9MLHV?psc=1&smid=A30ZYR2W3VAJ0A&ref_=chk_typ_imgToDp) $94.99 (cheaper, smaller RAM models should work as well)
- [Samsung EVO 32 GB micro SD card](https://www.amazon.com/dp/B06XWN9Q99?psc=1&smid=ATVPDKIKX0DER&ref_=chk_typ_imgToDp) $7.49
- [Raspberry Pi 4 Case iUniker](https://www.amazon.com/iUniker-Raspberry-Aluminium-Heatsink-Supply/dp/B07D3S4KBK/) $10.95
- [Nooelec NESDR Smart v4 Bundle](https://www.amazon.com/gp/product/B01GDN1T4S/) $41.95 ([this](https://www.amazon.com/gp/product/B00UAB79WG/) cheaper $19.99 SDR dongle was validated with the tutorial below (althout it is significantly more difficult to tune; see notes in tutorial . The reccommended dongle should provide better radio reception with less manual frequency corerection and less frequency drift compared to the cheaper dongle.)
- **Total $155.38 not including tax, $52.90 with existing Pi and SD card**


### Minimum Required Hardware

With an existing computer running Linux you simply need an SDR dongle and antenna to begin monitoring P25 radio communications. 

- [Nooelec NESDR Smart v4 Bundle](https://www.amazon.com/gp/product/B01GDN1T4S/) $41.95 ([this](https://www.amazon.com/gp/product/B00UAB79WG/) cheaper $19.99 SDR dongle was validated with the tutorial below (althout it is significantly more difficult to tune; see notes in tutorial . The reccommended dongle should provide better radio reception with less manual frequency corerection and less frequency drift compared to the cheaper dongle.)
- **Total $19.99-39.99 not including tax**

### Antenna Purposes

Antennas vary in thier phyical properties, such as length and shape, to optimize reception of a specific section of radio spectrum. Length typically decreaes as frequency increases. 

The Nooelec bundle comes with three antennas, and we reccommend LM YN LTE 4G Omnidirectional Antenna for more portable 800 Mhz reception when compared to the cable and base setup that comes with the Nooelec.

The shorter fixed length antenna (~12cm) is for generic UHF, 300 MHz to 3 GHz.

The longer fixed length antenna is optimized specifically for 433 Mhz which includes the ISM radio band.

The variable length antenna can be tuned for a range of frequencies by varying its extended length. You can use various [calculators](http://www.csgnetwork.com/antennagenericfreqlencalc.html) do determine this. For example, to get a 1/4 wavelength antenna for 162.45 Mhz NOAA Weather Radio that we demonstrate later on, we determine we need to extend the antenna to ~17.29 inches. Whereas an 1/2 wavelenth for 858 Mhz P25 radio would be ~6.5 inches.

Assuming reception is strong enough, you can likely use any of the three antennas for all instructions below, however, if you are having trouble getting a strong reception, then you can use this information to choose the correct antenna for your task.


## Setup

### Choose your OS

If you are building the radio with the `pi-top[4]` case we recommend you use the pi-topOS so all functions of the case (screen, buttons, fan, etc.) have the required software to work correctly without any additional software installation. 

If not using the pi-top[4] case we recommend you install Ubuntu Desktop 21.04 on your Pi. 

#### Option 1: Install pi-topOS on Raspberry Pi when using pi-top[4] case

1. First you need to install your Pi into your pi-top[4] case if not already done so. See this [youtube video](https://www.youtube.com/watch?v=38BCTsGrdiU) for instructions. 
2. Now we can install the OS. Download the pi-topOS from [here](https://www.pi-top.com/products/os)
3. Extract the image from the zip file.
3. Download the Raspberyy Pi Imager [software](https://www.raspberrypi.org/software/)
4. Insert your micro SD card into an SD card reader and plug it into your laptop
5. Launch the Pi Imager software
6. `Choose OS` > `Use Custom` > `Use the .img file you extracted e.g. XXXX-pi-topOS-sirius.img`
7. `Choose Storage` > select your SD card
8. `Write`

You know have Pi-topOSinstalled on your Pi. Insert the SD card into the Pi and connect a keyboard, mouse, monitor, and finally the power supply.

#### Option 2: Install Ubuntu Desktop 21.04 on Raspberry Pi when not using pi-top[4] case

1. Download the Raspberyy Pi Imager [software](https://www.raspberrypi.org/software/)
2. Insert your micro SD card into an SD card reader and plug into your laptop
3. Launch the Pi Imager software
4. `Choose OS` > `Other General Purpose OS` > `Ubuntu` > `Ubuntu Desktop 21.04 (RPI 4/400)`
5. `Choose Storage` > select your SD card
6. `Write`

You know have Ubuntu Desktop installed on your Pi. Insert the SD card into the Pi and connect a keyboard, mouse, monitor, and finally the power supply.

### Setup, Update and Upgrade your OS

As the Pi is booting your will be presented a GUI setup menu. Setup the OS using the GUI guide to setup the computer. If using Ubuntu, make sure you enable automatic login, as we intend to boot the radio in the future without requireing a screen, monitor, and keybaord. On pi-topOS, this is enabled by default.

Once you are at your desktop we first need to update the software and software repositories to ensure we have the latest software on our OS. Run the commands below to update and reboot. If you are using `pi-topOS` this was completed in the GUI and you can skip to the next section.

```sudo apt update```

`sudo apt full-upgrade`

`sudo reboot`

### Install the [GQRX](https://gqrx.dk/) Open Source SDR Reciever Software

Now we can install our first SDR radio application, GQRX.

1. Follow the GQRX [installation instructions](https://gqrx.dk/download/install-ubuntu) repeated below
2. First we need to remove existing installations of the software. There should be none so you can skip these commands or answer `n` to these commands if using a new OS installation.

```
sudo apt-get purge --auto-remove gqrx      
sudo apt-get purge --auto-remove gqrx-sdr    
sudo apt-get purge --auto-remove libgnuradio*   
```

3. **Ubunt only, pi-topOS skip:** Next we need to add multiple Personal Package Archive to install the software. GQRX and its required components are distributed by its authors in PPA instead of using standard built-in Ubuntu repositories. This gives the authors more control over the software repository, but requires we add the repositories to our OS so the software can be located. A verstion of GQRX is available in the default Raspbian repositories, so these instructions are not needed.

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
sudo apt-get install libvolk2-bin   (Ubuntu only, if using older ubuntu use libvolk1-bin)
volk_profile (ubuntu and pi-topOS)
```

Running this program creates a congiuration file in `$HOME/.volk/volk_config` that contains the best architecture and function calls to process radio signals on your computer. 

> If running an older OS only libvolk1-bin may be available for installation from standard repositories. Use this instead.

### Install OP25 Software

Now we can install the OP25 software that we will use to demodulate the digital P25 signals.

1. First we need to install Git so we can download the software.

```
sudo apt install git
```

2. Next we clone the [boatbod fork](https://github.com/boatbod/op25.git) of the [OP25 software](http://osmocom.org/projects/op25/wiki)

```
cd Documents
git clone https://github.com/boatbod/op25.git
```

This will download the software into a the directory `~/Documents/op25`

3. Now we install the software using the provided script

```
cd op25
./install.sh
sudo reboot
```

### Scan for Signals using GQRX

Now we can begin to use our SDR radio. Connect the SDR dongle and antenna. We will first manually scan and tune FM and NOAA Weather radio using GQRX to become familiar with SDR. We will use Bloomington, Indiana as our example location. Next, we will find the P25 control channel used by emergency services personnel in GQRX, and finally setup the OP25 software to demodulate the P25 radio communication.

#### FM Radio

1. Launch GQRX at the command line.

```
gqrx
```

2. Pick device RTL2838UHIDIR > OK

3. We will start with an easy signal to capture, FM radio; the same radio stations you listen to in a car. For our example we will use Bloomington's B96.7 WBWB found at 96.7 MHz. If you are not in Bloomgington you can substitute a channel from your broadcast area. Because FM is a wide bandwidth transmission, it will be easier to find and tune correctly.  

4. GQRX takes all frequency inputs in kHz. So to get from 96.7 MHz to kHZ we need to multiply 96.7 MHz by 1000 to get 96700 kHz. Input this number in the Frequency box under the Reciever tab. This is the target frequency the SDR will center its reception on.

5. Next Select `mode WFM (mono)` or `WFM (stereo)`. This mode selection that tells GQRX how to demondulate the radio signal. For example, AM and FM are different modulation techniques and you won't hear the correct audio output if you do not select the correct mode. You will notice that the stereo demodulation provides two channels of sound whereas the mono demodulation provides only one channel. Depending on your reception quality one mode may sound better than the other.

> See [this page](https://gqrx.dk/doc/practical-tricks-and-tips#use) for more description of the various settings.

6. Push the `play` button in the top left corner. You should hear start to hear the radio station. You will see in the top half of the screen a specturm analyzer, and in the bottom half a time plot of the signal strength for the sampled frequency range. The specturem analyzer measures the signal strength in dB. You will see a noise floor across the entire frequency range (probably between -60 to -100 dB depending on your reciever and GQRX settings). Where there is a signal broadcast, you should see peaks of signals rise above the noise floor(probably between -80 and -20 dB), these are broadcast radios signals. In the lower plot, time runs up and down along the y-axis, and frequency is the x-axis. The colors show the signal strength blue:low, yellow:medium, red:high. Yellow is typically suficient.

If your signal is not well define from the noise floor you may need to adjust your LNA `input tab` > `LNA`> I have mine set at 3.6 dB. You may also adjust your gain `reciever tab` > `Gain` > I have mine at -3.7 dB. The  `LNA` is a software low noise amplifier that attempts to amplify the power of the signal while not affecting the signal-to-noise (SNR) ratio. The `gain` will increase the power (read as volume) of the signal. You will notice as you increase the `gain` the volume output increases, but as you increase the `LNA` the signal may become more clear. Increasing the `LNA` will increase the power consumption, so you ideally want to use as little as possible while still recieving a clear signal.

 ![FM Tuning](/images/fm-tuning.png)

7. Push the `play` button again to stop the reception after you finish exploring the software. Next, we will try a slightly more difficult signal to tune, NOAA Weather radio.

#### NOAA Weather Radio

1. Change the frequency to your local NOAA Weather radio channel. In Bloomington, this is currently 162.50 MHz or 162500 kHz. You can find your local frequency from the [NOAA site](https://www.weather.gov/nwr/station_listing). 

2. Change the mode to `Narrow FM`. You will notice the grey bars on either side of the center frequency will shirnk. Narrow FM uses less bandwidth to broadcast the signals. This allows more radio stations to fit into the specturm, however, it decreases the quality of audio. This smaller bandwidth is still suitable for talking based audio like two-way radio and news, but it would not perform well for music found on typical FM radio stations.

3. Push the `play` button.

4. For the NOAA Weather Radio I had to increase the `LNA` from 3.6 dB to 36 dB to hear the voice transmission.   

5. You should see a signal peak near your center frequency (red line). However, depending on your reciever, you may find it is not completely centered on the peak. This is due to slight inaccuracies that can exist in your reciever. As frequency increase, you will notice this inaccuracy will also become more severe. You can tune the frequency by hovering over the red bar until you have a bi-directional horizontal arrow and then drag the center frequency until you get a clear signal. You can also use the frequency input text box or arrows, or click the numbers in the black box under `Reciever Options`. Notice the audio quality of the NOAA radio is significantly less that the FM radio, this is due to the reduced bandwidth.

6. Notice how much you have to move the frequency to get a clear signal. For example, using a cheaper dongle, I have to move my frequency -3.5 kHz. On my purpose-built dongle, I do not need to adjust. This will not be a static number that works for all frequencies, as it will increaes as frequency does. Negative imples less (left) of the center frequency and a positive number imples greater (right). Fortunately there is a correction method that does scales with the frequency called parts-per-million `ppm`. We will discuss how to find this correction number below.

7.  With this narrower signal you may run into a form of interference known as DC bias. This affects the reciever only at the center frequency (e.g. the frequency you input into GQRX). If your reception is poor, you may find that the DC bias drowns out the signal. If you are having trouble getting clear reception, try offsetting the center frequency 20 kHz or so, and then tuning to the channel by dragging the red bar back onto the signal. Also, you can investigate the `DC remove` option on the `input tab`. 

 ![NOAA Tuning](/images/noaa-tuning.png)

#### P25 control channel

Next, we will search for the P25 control channel for Bloomington.

1. Locate your P25 control channel by using the [radioreference.com](https://www.radioreference.com/apps/db/?sid=8084) databse. The control channels are colored red. Search for bloomington and identify the control channel as `858.4875c` MHz. The channels in blue are alternate control channels, and the other channels are voice channels used by the trunked radio stations. This is a user updated database, so it may not be accurate. If the control channel does not work after following all instructions below, try the alternate control channels, or even the voice channels. I found that the Merer, WI P25 system was using the alternate control channel that was listed in the database.

 ![Radio Reference](/images/radio-reference.png)


2. Input the frequency as 858487.500 kHz
3. Keep the mode on `Narrow FM`
4. This channel is significantly harder to tune, as there is no voice audio to guide us. P25 is a digital broadcast, so the control channel is a consistent peak of what sounds like digital noise. See this [youtube video](https://www.youtube.com/watch?v=KtWhSuAL1_Q&t=5s) to get an idea of the sound we are looking for. Mine sounds similar to the video but with less of the screeching, chirping, and a slower paced rhtymic thudding, so do not be concerned if it only sounds similar, but not exact to the video.
5. Also, at this higher frequency your reciver error will be amplified. I have to tune my cheap dongle approximately -23.1 kHz to center on the signal (corrected signal). This is quite far due, and in my case the corrected peak happens to be a slightly lower than the peak that appears at the center frequency. Do not be tricked if you have a strong signal at the center frequency but not the correct noise. Investigatge all peaks nearby. The peak at the center frequency can be caused by DC bias. Audio sounds better the closer it is to the center frequency, and conversely SDR do not handle audio at the edge of the sample range as well. However, there is some DC bias introduced at the center frequency, so it is best for us to tune to a center frequency that is close to, but not exactl the target frequency. We will handle this with an tuning offset later on. 
6. Once you have identified the corrected signal return the offset to zero, thus returning to the center frequency, and use the `ppm`found on the `Input` tab to align the corrected signal onto the center frequency. This will be a number we use later. With my cheap dongle it is 28 ppm, and with my purpose-built dongle it is 0 ppm.  Jot down this number for use later. The sound may no longer be the same digital noise, that is okay, it is getting washed out by the DC bias at the center frequency.

 ![P25 Tuning](/images/p25-tuning.png)

### Configure and Launch OP25 Software to Listen to P25 Commuincation

Now we will use the OP25 software to demodulate the digital P25 voice communications.

1. Change to the op25 reciver application directory

```
$ cd Documents/op25/op25/gr-op25_repeater/apps/
```

2. Set the control channel frequency to tat identified above.

```
./setTrunkFreq.sh 858.4875    (this modiefies op25.sh, but we may need to modify further for -q and -o)
```

3. Launch OP25 using the command below. Substitue your correct `ppm` offset after the `-q` option. With my purpose-built dongle it is 0. For my cheap dongle it is 28.

```
./rx.py --nocrypt --args "rtl" --gains 'lna:36' -S 960000 -X -q 0 -o 28000 -v 1 -2 -V -U -T trunk.tsv 2> stderr.2
```

There are many options include to the `rx.py` program, that I will desribe below.

`--nocrypt`: some P25 signals are encrypted, this will filter out encrypted communication as they will only come across sounding like noise.

`--args "rtl`: this tells the OP25 software that you are using an rtl-sdr type dongle

`--gains 'lna:36` this controls the low noise amplifier to increase the strenth of the signal (and also some of the noise) I believe this value is 36 dB, similar to the LNA option in the `input tab` of GQRX.

`-S 960000` this is the sample range. Our SDR will monitor 960,000 kHz of bandwidth. 

> The dongle is likely capable of more, however, I found with a value closer to the dongles reported capability (2,400,000) I was recieving many `OOOOOOOOO` errors in the stderr.2 file. This is indicative that the SDR is overworked, or the computer can not keep up with the sample size.

`-X` The OP25 software will attempt to auto-tune to the frequency. It only works if you are close. You will see a number such as `Frequency 858.487500(-266)` at the bottom of the terminal. This shows it has autotuned -266 hz to center on the signal.

`-q 28` This is the frequency correction in `ppm`. Better SDR dongles should be significantly lower (<= +- 2). 

`-o 28000` The is the offset from the center frequency to avoid DC bias. I found 28000 Hz to work well for both of my dongles, however, this may be higher than is actually needed. This is not related to the `-q` option. 

`-v 1` Sets the verbosity level of the info and errors logged to stderr.2. Increase this for more information.

`-2` Enables phase 2 TDMA decoding. Indiana SAFE-T network is reportedly all phase 1 according to radioreference.com, however it does not appear hurt to enable this.

`-V`  Enables the voice codec. I've notived this appears to improve the sound of voice transmissions.

`-U` Enables the built in UDP audio player. This allows you to hear the audio from your speakers, and capture it on a specific UDP port.

`-T trunk.tsv` Tells rx.py which file to use for the trunk configuration. This was modified with the `./setTrunkFrequency` command earlier.

`2> stderr.2` This redirects error ouput to a file named `stderr.2` This prevents the text console display from getting messed up. If things are not working, it is worth taking a look at the file `cat stderr.2` to read the message log.


4. If the configuration options are correct (the frequency as set by the ./setTrunkFreq.sh program, the offset set by the -o, and the frequency correction as set by the -q) and the reception is acceptable then you should see a line similar to the following:

```
NAC 0x6b1 WACN 0xbee00 SYSID 0x6bd 858.487500/813.487500 tsbks 702
```

You can use this line at the top of the terminal to verify that you are recieving and correctly decoding data from the control channel. For the P25 system, the NAC is the network access code, the WACN is the wide area communications network ID and the SYSID is the system ID withing the WACN. If the WACN and SYSID numbers match those found in the [radioreference.com](https://www.radioreference.com/apps/db/?sid=8084) databse then you know you have tuned into the correct P25 control channel.

If your reception or options are incorrect then you will see a line similar to the following:

```
NAC 0x00 WACN 0x-1 SYSID 0x-1 0.000000/0.000000 tsbks 0
```

Continue trobleshooting by changing your input options and using hte steps below.

5. Verify the center frequency is on top of the control channel peak. `Press the number 1` to bring up the `fft plot`. This plot will show you the sampled specturm and the power of the signals recieved. You should see the peak of the control channel frequency line up with the black bar indicating the tuned frequey.

![FFT Plot](/images/fft-plot)

If this number is off, then you will need to change you ppm `-q` option until the peak is aligned on the black bar. Increasing the ppm and offset will shift the black bar to the left (or from the other perspective, the signal to right relative to the black bar). Good SDRs should be in the range of -2 to 2 ppm. Cheap dongles can be much greater, such as the 28 ppm observed on the cheap dongle. This is the most difficult part of tuning in the P25 signal, but that is why we first started in GQRX, to get a good starting point. Fortunately once you find the correct `ppmn` number, it will hopefully not deviate much. It should deviate less with higher quality dongles. Cheap dongles may increase in temperature overtime and cause additional drift. Interference from nearby electronics or USB devices can also cause signal loss due to increased noise. DC bias can also affect reception if tuning directly on the cneter frequency (i.e. you set `-o` to 0)

6. Verify the constellation plot is showing good decoding of the `CQPSK` signal. `Press the number 2` to bring up the `constellation plot`. `CQPSK` or Compatible Differential Offset Quadrature Phase Shift Keying is the modulation technique used by P25 Phase II FDMA systems. You should see 4 distinct circles in your graph that represent the 4 possible digital symbol values transmitted by the signal. If you see a ring or one large circle, then OP25 is not able to demodulate the `CQPSK` signal correctly and you will need to improve your reception or confirm your settings.

![Constellation Plot](/images/constellation-plot-good)

7. Verify the Mixer:balance plot is showing an even mix of the signal. `Press the number 5` to bring up the `Mixer:balance plot`.  If your signal is not dead on center then you may see one side of hte signal peak higher than the other. Ideally this plot is showing a low number (less than 10).  As long as you are close enough the `-X` option should autotune to achieve a good balance. For example, my auto tune is showing  XXX hertz correction in the below image. You can tune manyually using the `<` `>` (1200 hz) and `,` `.` (100 hz) keys

![Balance Plot](/images/balance-plot.png)

8. When there is a voice communication you will see the frequency, active talk-group ID (tgids) , a last seen timer, and a transmission count in the terminal.

Notice in the image below that in periods of high talking traffic, you will see many voice frequencies being used. Even the alternate control channel for Bloomington was used at some point for a voice transmission. If simultaneous transmissions are executed on frequences separated by more bandwidth than the sample rate (in our case 960,000 kHz) you may not recieve one of those communications. You can setup multiple SDR dongles to ensure you have enough bandwidth and recorders to recieve all traffic. (outside the scope of this tutorial)

![Voice-6-Freq](/images/voice-6-freq.png)


9. Press `q` to quit the program


10. We can now setup a file to automatically translate the talk-group ids (tgids) into a named entity such as ` Bloomington Police Dispatch`. This allows you to get a better idea of who is communicating. Open the file `Documents/op25/op25/gr-op25_repeater/apps/tompkins.tsv` using a spreasheet program. We need to create a tsv with the tgids in column 1 and the nomenclature in column 2. You can find these back on [radioreference.com](https://www.radioreference.com/apps/db/?sid=8084). Search for all Bloomington related tgids and input in the columns. Save as `bloomington.tsv` when you complete. An example tsv is includd in our git repository.

Ensure you save as a tab delimited file using [these instructions repeated below](https://ask.libreoffice.org/en/question/57184/how-to-generate-calc-tab-delimited-output/)

`Use File > Save a Copy.`

`Goto the place, where you want to save the file.q`

`Choose the file type Text CSV (.csv) form the drop down list.q`

`CheckEdit filter settings. Click Save. Now you get a dialog for the settings.`

`In the drop-down list field delimiter select the item {Tab}.`

`After saving change .csv to .tsv`

![RR TGIDS](/images/rr-tgids.png)

Now edit the trunk.tsv file in the Libre calc app. Under `tgid_tags_file` input `bloomington.tsv`. This tells the software to youse `bloomington.tsv` to translate the tgids. When saving use hte `Use Text CSV format` to keep the file as a tabbed separated file.

11. Relaunch the program with the same command as in `3.` You will now see the tags at the bottom of the terminal during voice transmissions. In this case `DOT-52-SUB`. You can yous the descriptions if you prefer.

![Tagged Voice](/images/tagged-voice.png)

### Launch the OP25 software automatically on boot

We will now setup the radio to automatically launch the op25 software on boot, so it can be used without a keyboard, screen, and mouse.

We will setup a systemd service to do this.

`sudo gedit`

Write this file being sure to double check the username and directories (this example should work unchanged for pi-topOS).

```
[Unit]
Description=Autostart the OP25 Radio Scanner
After=multi-user.target

[Service]
User=pi
Group=pi
WorkingDirectory=/home/pi/Documents/op25/op25/gr-op25_repeater/apps/
ExecStart=/home/pi/Documents/op25/op25/gr-op25_repeater/apps/rx.py  --nocrypt --args "rtl" --gains 'lna:36' -S 960000 -X -q 0 -o 28000 -v 1 -2 -V -U -T trunk.tsv 2> stderr.2

[Install]
WantedBy=multi-user.target
```

Save as `/etc/systemd/system/radio-start.service`

To get the service to start and be enabled on system restart we need to run the following commands.

```
sudo systemctl daemon-reload
sudo systemctl start radio-start.service
sudo systemctl enable autossh-red001.service
```

save command in radio-start.sh
chmod 777 radio-start.sh
./radio-start.sh to test that it works
crontab-e
select editor


### Video Demo

## Background

The world of SDR and radio waves is vast and complex. In this background section we provide a glossary of terms used in this tutorial that may be new to you. We explain them by describing where in the tutorial the term applies. We strive to provide the basics needed for you to understand the linked materials and bootstrap your knowledge as you explore the field.

### Radio


#### Frequency:

#### Spectrum:

#### Hertz:

#### Decibel (dB):

#### Antenna:

#### NOAA Weather Radio





### SDR


#### Dongle:

#### Sample Rate:

#### Recieve Range:

#### Temperature Drift:

#### Center Frequency

#### DC Bias

#### PPM:

#### Frequency Offset:

#### SDR Reciever Software (GQRX)

#### Filter

#### Modulation / Demodulation / Mode

##### FM

##### AM

#### OP25 Software

#### Interference
usb


### P25

#### Digital Radio:

#### Trunking:

#### Indiana SAFE-T

#### NAC:

#### WACN:

#### Control Channel:

#### Voice Channel:

#### TalkGroup:

#### Phase 1: 

#### Phase 2:	

## Acknoledgements

I would like to ackknowledge John for his useful OP25 [tutorial](https://www.hagensieker.com/wordpress/2018/07/17/op25-for-dummies/) that this tutorial extends and updates. 
