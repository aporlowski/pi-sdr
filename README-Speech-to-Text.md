# Investigation of Speech-to-Text Services on P25 Radio Transmissions

## Introduction

P25 is digital radio standard commonly used by emergency services and public service personnel (such as poice, EMS, department of transportation, DNR, etc.) for two-way radio communication. In previous work, we built [a software defined radio (SDR)](https://github.com/aporlowski/pi-sdr) using inexpensive SDR dongles and a Raspberry Pi to record these radio trasnmissions. 

In this investigation we will investigate the ability of modern AI driven speech-to-text services, such as those provided by cloud providers like Amazon Web Services, Google Cloud Platform, and Microsoft Azure, to transcribe P25 radio transmissions. The automated recording and transcription of radio communication could be beneficial for many usings including:

- A rich feed of data for Geospatial Information System and Situational Awareness Applications (like [ATAK](https://en.wikipedia.org/wiki/Android_Team_Awareness_Kit)) when coupled with unit GPS tracking such as "[Blue Force Tracker](https://en.wikipedia.org/wiki/Blue_force_tracking)". 
- Access to P25 emergency communications for the hearing impaired
- A reviewable tranmission log for radio users to review past discussions, such as a dispatcher trying to remember and address or license plate number
- Investigation and review of emergency service personnel actions in incidients
- Real-time and customized alerting to police and EMS activity in a users area; useful for micro-alerting and monitoring journalists.
- A study of how police, EMS, and others use radio communiction to achieve thier goals

We will record P25 radio transcriptions using our SDR radio, and then run the recodings against AI services to investigate the accuracy of these services.

## Methodology

- We will setup [trunk-recorder software](https://github.com/robotastic/trunk-recorder) to automate the recoding of P25 radio communications
- Next, we demonstrate transcription by measuring Amazon Transcribe's transcription compared to human comprehension on ~10 minutes of trunk-recorder produced recordings
- Next, we anaylze the transcription results to make generalizations, recommendations, and identify areas for further investigation
- Finally, we propose a software technical demo capable of real-time P25 radio transcription to a computer console or file.

## Setting Up Trunk Recorder

In this section we setup the [trunk-recorder software](https://github.com/robotastic/trunk-recorder) software to automate the recording of real-time P25 radio transmissions of the Indiana SAFE-T network from Bloomington, IN.

### Design your SDR radio system

You may need multiple SDR dongles depending on the frequency ranges used in your local P25 radio system.

Bloomington uses these channels for it's P25 system:

851.250 Mhz, 851.9625, 852.400, 853.450a, 857.4875, and 858.4875c.

Bloomington's lowest frequency is 851.250 Mhz and the highest is 858.4875 (used as the control channel).

The difference is 7.2375 Mhz. The RTL-SDR dongles used in our prior work can reliably sample approximately 2.4 Mhz of bandwidth. So to cover the entire spectrum used by Bloomington's P25 system we need 2-3 SDR dongles. The first can cover the highest two channels (857.4875, and 858.4875c), and the second could theoretically cover the lowest four channels (851.250 Mhz, 851.9625, 852.400, 853.450a). However, SDRs do not work as well on channels at the edges of the sample range. As the difference in the lowest four sets is 2.2 Mhz, it may be more reliable to use the second for the lowest three channels (851.250 Mhz, 851.9625, 852.400) a third SDR specifically for the 853.450a channe.

In my case I only have two SDRs, so I will use one for the high two channels, and a second for the lower three channels, and not cover the 853.450a channel. In my experience this channel is infrequently used for voice comms, probably becuase it is the alternate control channel. However, in peak traffic I have seen it used for voice transmissions, but at a significatnly lower rate than the other channels.

### Set serial numbers on your SDR dongles for trunk-recorder to use

I first set the SNs on my SDR dongles so I can specify trunk recorder to use a specific dongle for a certain coverage. This is because my dongles are not unique. I will use my cheap dongle to monitor the control channel 858.4875c and 857.4875, and my purpose built SDR dongle to monitor the lower three channels (851.250 Mhz, 851.9625, 852.400)

I plug in only the cheap dongle, and I set the cheap dongle to be SN `00000001` with the following command:

```
rtl_eeprom -s 00000001
```

I plug in only the expensive dongle at set its serial number to be `0000002`

```
rtl_eeprom -s 00000002
```

### Create a `config.json` file for your SDR radio system

Now we create a config file as `~/trunk-recorder/config.json` with the following content:

```
{
    "ver": 2,
    "sources": [{
        "center": 858287500,
        "rate": 2048000,
        "ppm": 28,
        "gain": 36,
        "debugRecorders": 0,
        "digitalRecorders": 4,
        "driver": "osmosdr",
	"device": "rtl=00000001"
    }, {
        "center": 851825000,
        "rate": 2048000,
        "ppm": 0,
        "gain": 36,
        "debugRecorders": 0,
        "digitalRecorders": 4,
        "driver": "osmosdr",
	"device": "rtl=00000002"
    }],
    "systems": [{
        "control_channels": [858487500],
        "type": "p25",
        "shortName": "bloom",
        "modulation": "qpsk"
    }],
    "captureDir": "/home/anthony/trunk-recorder/"
}
```

The configuration contains two sources (dongles) and one system (the target P25 system).

#### Sources

For the first source I compute the center frequency (a frequency in the middle) by subtracting the target low channel from the target hight channel, dividing by two, and adding this value to the target low channel.

First source: monitors (851.250 Mhz, 851.9625, 852.400)

852.400 - 851.250 = 1.15 Mhz
1.15 / 2 = 0.575 Mhz
center frequency = 851.25- + 0.575 = 851.825 Mhz.

Notice this center frequency should not be too close to any targeted channel. 851.9625 is only .1375 Mhz away from the center frequency. That should be okay for this dongle.

Second source: monitors (857.4875, and 858.4875c).

I simly set the center frequency 200 kHz lower than the control channel at 858.2875, as I wish to get the best reception on the control channel and still reach the low channel. As long as 1/2 the rate can reach the lower channel, we should still recieve it. The separation from the center frequency to the low channel is 0.8 Mhz, and 1/2 the sample rate is 1.024 Mhz. So it appears it may be close to the edge, but we will test and see.

It is also important to ensure the sources do not overlap.

First source range =  850.801 - 852.849 Mhz

Second source range =  857.2635 - 859.31115

The `rate`, `ppm`, and `gain` are all values carried over from [this](https://github.com/aporlowski/pi-sdr) tutorial.

I am currently unsure on the effect of `debugRecorders`, `digitalRecorders`, and `driver` values, but these values work.

`digitalRecorders` - sets the number os simultaneous transmissios allowed to be recorded by this source.
`debugRecorders` - sets the number of raw input recorders for signal debugging
`driver` - sets the GNU radio block used.

The `device` value needs to be set to the dongle SNs set in the above section. This assigns that dongle as that source. For example, the cheap dongle `00000001` is supposed to monitor the control channel, so on the first source we assign `"rtl=00000001"` as the device (the `rtl=` is required).

> Note see [this page](https://github.com/robotastic/trunk-recorder#configure)for a full description of configuration options

#### system

This configures the system we plan to monitor. Set the `control_channels`, `type`, `shortName` (an nickname id for the system), and the `modulation`.

#### capture directory

The `captureDir` is the directory within the Docker container that trunk-recorder will place the recorded `wav` files, and the metadata files (`.json`).

### Setup trunk-recorder as a Docker container on Ubuntu

A docker container povides a quick and easy method to install the trunk-recorder software if you do not need to modify its source code. 

We assume your environment already has Docker installed. If not we suggest you look at [these installation instructions](https://docs.docker.com/engine/install/ubuntu/).

With our `config.json` already configured we are ready to launch the container.

> Note: There is also an optional `talkgroup.csv` file that you can setup, but we have skipped that for this demo.


## Software Technical Demo

He we outline the processing of real-time P25 transmissions for a simple transcription technical demonstration.

The technical demonstration will:

- Monitor trunk-recorder output directories for new audio `wav` files.
- Upload `wav` files to Amazon S3 bucket.
- Schedule Amazon Transcribe Job on the uploaded file.
- Fetch Amazon Transcribe Job and print result to console / output file inorder of transmission
