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

### Install trunk-recorder as a Docker container on Ubuntu

A docker container povides a quick and easy method to install the trunk-recorder software if you do not need to modify its source code. 

We assume your environment already has Docker installed. If not we suggest you look at [these installation instructions](https://docs.docker.com/engine/install/ubuntu/).

#### Create a `config.json` file for your SDR radio system

## Software Technical Demo

He we outline the processing of real-time P25 transmissions for a simple transcription technical demonstration.

The technical demonstration will:

- Monitor trunk-recorder output directories for new audio `wav` files.
- Upload `wav` files to Amazon S3 bucket.
- Schedule Amazon Transcribe Job on the uploaded file.
- Fetch Amazon Transcribe Job and print result to console / output file inorder of transmission
