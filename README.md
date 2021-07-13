# pi-sdr

Turn your Raspberry Pi into a portable radio reciever using software defined radio (SDR). We demonstrate its use to listen to AM, FM, Project 25 (P25) emergency-use radio channels and more.

This tutorial will be a comprehensive introduction to Raspberry Pi single-board computers (SBC), software defined radio, and P25 radio.

Teachers and students can use this tutorial to learn about these concepts through an hands-on project. Concerned citizens, journalists, and Virtual Operations Support Team (VOST) members can use this tutorial to setup a radio to monitor emergency radio traffic in their area.

## Components

P25 recievers on the market, such as the Bluetail Technolgies P25rx, typically cost $250 or more. This can be prohibitively expensive for individuals 

### Portable Model

These components provide a portable Rasbperry Pi based radio including a case, battery, built-in speaker, small utility screen, SDR dongle, and antenna. Bluetooth and audio jacks from the Pi provide audio output, and wifi allows configuration via secure shell (ssh) from any computer.

- [Raspberry Pi 4 Model B Starter Kit 8GB](https://www.amazon.com/dp/B08DJ9MLHV?psc=1&smid=A30ZYR2W3VAJ0A&ref_=chk_typ_imgToDp) $94.99 (cheaper, smaller RAM models should work as well)
- [Samsung EVO 32 GB micro SD card](https://www.amazon.com/dp/B06XWN9Q99?psc=1&smid=ATVPDKIKX0DER&ref_=chk_typ_imgToDp) $7.49
- [PI-TOP [4] DIY Edition Case](https://www.amazon.com/dp/B08N6B8M1H) $99.95
- [RTL-SDR Blog V3 Dongle and Antenna Kit](https://www.amazon.com/RTL-SDR-Blog-RTL2832U-Software-Defined/dp/B011HVUEME/) $39.95 (in the tutorial below [this](https://www.amazon.com/gp/product/B00UAB79WG/) cheaper $ 19.99 SDR dongle was used instead. The reccommended dongle should provide better radio reception, and require no deviation from the tutorial)
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


