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
- Next we demonstrate transcription by measuring Amazon Transcribe transcription compared to human comprehension on ~10 minutes of radio broadcasts
- Next we anaylze the transcription results to make generalizations, recommendations, and identify areas of further investigation
- Finally we propose a software technical demo capable of real-time P25 radio transcription to acomputer console or file.

## Software Technical Demo

He we outline the processing of real-time P25 transcription for a simple technical demonstration.

Write software to automatically transcribe trunk-recorder recording outputs.

- Monitor trunk-recorder outputdirectoriesfor new wav filesb.
- Upload wav files to Amazon S3 bucket.
- Schedule Amazon TranscribeJobd.
- Fetch Amazon Transcribe Joband print result to console / output file inorder oftransmission 
