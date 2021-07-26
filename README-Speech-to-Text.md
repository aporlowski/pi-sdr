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

### Setup trunk-recorder as a Docker container on Ubuntu

A docker container povides a quick and easy method to install the trunk-recorder software if you do not need to modify its source code. 

We assume your environment already has Docker installed. If not we suggest you look at [these installation instructions](https://docs.docker.com/engine/install/ubuntu/).

### Design your SDR radio system

You may need multiple SDR dongles depending on the frequency ranges used in your local P25 radio system.

Bloomington uses these channels for it's P25 system:

851.250 Mhz, 851.9625, 852.400, 853.450a, 857.4875, and 858.4875c.

Bloomington's lowest frequency is 851.250 Mhz and the highest is 858.4875 (used as the control channel).

The difference is 7.2375 Mhz. The RTL-SDR dongles used in our prior work can reliably sample approximately 2.4 Mhz of bandwidth. So to cover the entire spectrum used by Bloomington's P25 system we need 2-3 SDR dongles. The first can cover the highest two channels (857.4875, and 858.4875c), and the second could theoretically cover teh lowest four channels (851.250 Mhz, 851.9625, 852.400, 853.450a). However, SDRs do not work as well on channels at the edges of the sampled bandwith. As the difference in the lowest four sets is 2.2 Mhz, it may be more reliable to use the second for the lowest three channels () a third SDR specifically for the 853.450a channel, and 

#### Create a `config.json` file for your SDR radio system

## Software Technical Demo

He we outline the processing of real-time P25 transmissions for a simple transcription technical demonstration.

The technical demonstration will:

- Monitor trunk-recorder output directories for new audio `wav` files.
- Upload `wav` files to Amazon S3 bucket.
- Schedule Amazon Transcribe Job on the uploaded file.
- Fetch Amazon Transcribe Job and print result to console / output file inorder of transmission
