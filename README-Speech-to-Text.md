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

#### Launch the Docker container

With the following command. Notive the path to the location of the `config.json` file (`/home/anthony/trunk-recorder`). Substitute as necessary.

```
sudo dockerun -it --privileged -v /home/anthony/trunk-recorder:/app -v /var/run/dbus:/var/run/dbus -v /var/run/avahi-daemon/socket:/var/run/avahi-daemon/socket robotastic/trunk-recorder:latest
```

Buried in the output you should find a line like hte one below that confirms you have tuned into the control channel:

```
[2021-07-27 15:23:09.061207] (info)   [bloom]	Decoding System ID 6BD WACN: BEE00 NAC: 6B1
```

When recording you will see output like the following:

```
[2021-07-27 15:23:10.853990] (info)   [bloom]	TG:      21519	Freq: 8.519625e+08	TG not in Talkgroup File 
[2021-07-27 15:23:10.854071] (info)   	- Starting P25 Recorder Num [4]	TG:      21519	Freq: 8.519625e+08 	TDMA: false	Slot: 0	Mod: true
[2021-07-27 15:23:10.854175] (info)   [bloom]	TG:      21519	Freq: 8.519625e+08	Starting Recorder on Src: rtl=00000002
[2021-07-27 15:23:25.262988] (trace)   [bloom]	TG:      21519	Freq: 8.524000e+08	Update Retuning - New Freq: 8.524000e+08	Elapsed: 15s 	Since update: 2s
[2021-07-27 15:23:39.010747] (trace)   [bloom]	TG:      21519	Freq: 8.524000e+08	Ending Recorded Call - Last Update: 4s	Call Elapsed: 29
[2021-07-27 15:23:39.010963] (info)   	- Stopping P25 Recorder Num [4]	TG:      21519	Freq: 8.524000e+08 	TDMA: false	Slot: 0
[2021-07-27 15:23:39.010985] (error)   wav error closing file
[2021-07-27 15:23:39.010995] (info)   [bloom]	Deleting this call as it has a duration less than minimum duration of 0	TG:      21519	Freq: 8.524000e+08	Call Duration: 0s
[2021-07-27 15:23:39.011025] (error)   Could not delete file /home/anthony/trunk-recorder/bloom/2021/7/27/21519-1627399390_851962500.wav
[2021-07-27 15:23:39.456282] (info)   [bloom]	TG:      21519	Freq: 8.519625e+08	TG not in Talkgroup File 
[2021-07-27 15:23:39.456407] (info)   	- Starting P25 Recorder Num [4]	TG:      21519	Freq: 8.519625e+08 	TDMA: false	Slot: 0	Mod: true
[2021-07-27 15:23:39.456637] (info)   [bloom]	TG:      21519	Freq: 8.519625e+08	Starting Recorder on Src: rtl=00000002
[2021-07-27 15:23:51.006459] (trace)   [bloom]	TG:      21519	Freq: 8.519625e+08	Ending Recorded Call - Last Update: 4s	Call Elapsed: 12
[2021-07-27 15:23:51.006625] (info)   	- Stopping P25 Recorder Num [4]	TG:      21519	Freq: 8.519625e+08 	TDMA: false	Slot: 0
[2021-07-27 15:23:51.006655] (error)   wav error closing file
[2021-07-27 15:23:51.006666] (info)   [bloom]	Deleting this call as it has a duration less than minimum duration of 0	TG:      21519	Freq: 8.519625e+08	Call Duration: 0s
[2021-07-27 15:23:51.006698] (error)   Could not delete file /home/anthony/trunk-recorder/bloom/2021/7/27/21519-1627399419_851962500.wav
[2021-07-27 15:23:51.877223] (info)   [bloom]	TG:      21526	Freq: 8.524000e+08	TG not in Talkgroup File 
[2021-07-27 15:23:51.877260] (info)   	- Starting P25 Recorder Num [4]	TG:      21526	Freq: 8.524000e+08 	TDMA: false	Slot: 0	Mod: true
[2021-07-27 15:23:51.877314] (info)   [bloom]	TG:      21526	Freq: 8.524000e+08	Starting Recorder on Src: rtl=00000002
[2021-07-27 15:23:58.729863] (info)   [bloom]	TG:      21519	Freq: 8.519625e+08	TG not in Talkgroup File 
[2021-07-27 15:23:58.729985] (info)   	- Starting P25 Recorder Num [5]	TG:      21519	Freq: 8.519625e+08 	TDMA: false	Slot: 0	Mod: true
[2021-07-27 15:23:58.730133] (info)   [bloom]	TG:      21519	Freq: 8.519625e+08	Starting Recorder on Src: rtl=00000002
[2021-07-27 15:24:00.012538] (trace)   [bloom]	TG:      21526	Freq: 8.524000e+08	Ending Recorded Call - Last Update: 4s	Call Elapsed: 9
[2021-07-27 15:24:00.012876] (info)   	- Stopping P25 Recorder Num [4]	TG:      21526	Freq: 8.524000e+08 	TDMA: false	Slot: 0
```

When the contol channel is lost you will see the following message:

```
[2021-07-27 15:28:31.009971] (info)   	 - System Source 0 - Min Freq: 8.573275e+08 Max Freq: 8.592475e+08
[2021-07-27 15:28:31.010085] (error)   [bloom]	 Control Channel Message Decode Rate: 1/sec, count:  3
[2021-07-27 15:28:31.242947] (error)   [bloom]	 process_data_unit timeout
[2021-07-27 15:28:32.265007] (error)   [bloom]	 process_data_unit timeout
[2021-07-27 15:28:33.287592] (error)   [bloom]	 process_data_unit timeout
[2021-07-27 15:28:34.007134] (error)   [bloom] Retuning to Control Channel: 8.574875e+08
```

With my cheap dongle, the channel can sometimes come and go.


### Operate trunk-recorder as a Docker container.

The following list of commands demonstrates how to operate your trunk-recorder container.
- **get container ID** `sudo docker ps`
- **second shell to running container** `sudo docker exec -it CONTAINERID bash`
- **see recordings in container** `ls -R /home/anthony/trunk-recorder/*`
- **copy file from container to host working directory** `sudo docker cp CONTAINERID:/home/anthony/trunk-recorder/bloom/2021/7/25/21256-1627238771_851962500.wav ./`
- **copy all files from container to host working directory** `sudo docker cp -r CONTAINERID:/home/anthony/trunk-recorder/ ./`
- **commit and save container state as new image** `sudo docker commit CONTAINERID trunk-recorder-sample`
> Note if you close a container without commiting, that state is lost if not previoulsy copied.
- **list available images** `sudo docker images`
- **launch new state image** `sudo docker run -it   --privileged   -v /home/anthony/trunk-recorder:/app   -v /var/run/dbus:/var/run/dbus   -v /var/run/avahi-daemon/socket:/var/run/avahi-daemon/socket   trunk-recorder-sample`
- **kill a specific container** `sudo docker kill CONTAINERID`

### Create an AWS S3 bucket
We assume you have an AWS account.

We need to upload the `wav` files to to an AWS S3 bucket so that Amazon Transcribe can run a Transcription Job.

![Create bucket](/images/create_bucket.png)

### Upload `wav` files to S3 bucket

Copy the `wav` files from the trunk-recorder container, using `docker cp` (demoed above), and the upload them to your AWS S3 bucket.

![Upload Files](/images/upload_files.png)

### Schedule Amazon Transcribe Job

Schedule a Amazon Transcribe job on those `wav` files. For this experiment I used all default optoins.

![Schedule Job](/images/schedule_job.png)

### View the Job Result

View the results.

![View Job](/images/view_job.png)

## Analysis of transcription demo

Here we describe the results of our first experiment of using AI services to transcribe P25 radio communications.

### Description of experiment

In this experiement we recorded and analyzed 50 messages of P25 radio transmissions using trunk-recorder as configured above. The caputre time was approximately 1 hour on 27 July 2021 between 11:30 and 12:30 EST. The raw `wav` files can be found [here](/data).

We measured the word count and length(s) of the messages. We first transcribed the messages by ear, allowing unlimited replays. Next, we used Amazon Transcribe, an AI speech-to-text service, to transcribe the messages. We used all default settings on the Amazon Transcribe jobs.

Finally, we measured how many words from the Amazon Transcribe matched the human transcription which is computed as the percentage that we refer to as accuracy.

### Rules for Comparing Human and AI Transcription

We used a pretty liberal ruleset while determining if the words in the transcriptions matched. The thought process behind this ruleset is that no meaning is lost by the rule, or that context-aware post-processing could correct the errors with no loss to the message meaning. The resulting measure is a `% match to human transcription`. Note, this does not always correspond to `% meaning retained`.

1. Punctuation and capitalization were not considered 
2. Mispellings are considered the same
3. A number spelled out vs represented as a numeral is considered the same
4. Contractions vs spelled out contractions are considered the same
5. Colloquial speech vs regular grammar, for example "gonna" vs "going to", are considered the same.
6. If the human transcription labeled the word GARBLED or INDISCERNABLE, that word did not count.
7. An unplaceable fragment from the AI transcription was not counted
8. Gaps in numbers were considered okay as long as no other added characters within or between.
9. Conjoined numbers that should have had gaps were okay as long as there were no other added characters within or between.

**INDISCERNABLE** Words are words that cannot be comprehended given all context cluse. For example, police jargon unfamiliar to the transcriber.

**GARBLED** Words are unclear if a word was transmitted or some other radio noise.

### General Statistics

- Messages: 50
    - No Content: 8 (16%)
        - SHORT, GARBLED: 5
        - SHORT, SILENT: 2
        - COMPLETELY INDISCERNABLE: 1
    - With Content: 42 (84%)
- Message Length
    - Transcribed Seconds: 500 (~8.33 min)
    - Average Length (s): 11.9
    - Min Length (s): 1
    - Max Length (s): 51
- Message Word Count
    - Bottom 25 percentile: <= 10
    - 25 - 50 percentile: 11 to 19
    - 50 - 75 percentile: 20 to 32
    - 75 - 100 percentile: 33 to 144 
- Total Words Transcribed
    - human transcribed: 1046
    - AWS transcribed: 878 (~84 % of human value)
- **Average Accuracy: 59.10 %**
- Accuracy STD: 27.2

The accuracy of the transcription (at an average of 59.1%, likely much lower for meaning retention) seems to be at a point where it is not immediately useful for real-world application. However, it shows enough promise that model improvments, such as custom vocabularies and custom training data should be considered.

Below we present a scatter plot that shows message word count vs transcription accuracy. There is a correlatoin between message length in word count to the accuracy of the transcription.

![Scatter Plot](/images/scatter_plot.PNG)

Below we present a bar plot of the message length by word count in quartiles vs accuracy. We show that accuracy tends to increase as word count increases, however, messages in the top quartile (longer than 33 words) were more likely for large segments of the message to be missed by the AI transcription.

![Bar Plot](/images/bar_plot.png)

### Analysis

Here we present a list of thoughts and findings identified by this experiment that will need to be considered when desigining a future system for P25 radio transcription; in particular, within the context of the emergency services and public services sectors.

#### Alphabet and Numeric Words

- Great care needs to be given to numbers and whether they are spelled out or presented in number form. Different contexts, such as call signs, street numbers, house numbers, regular speech require different care. Numeric numbers are very common in the emergency service sector.
- Letters such as "B" and "D", and numbers suchs as "15" and "16" often sound similar. Speaker protocol should be used to use word substitution of letters, and numbers should be repeated. The numbers should be repeated as well. Similarly, numbers and words can be confused such as "04" and "Oh for", the model should be context aware to account for the right usage.

#### Message Length

- The AI transcription seems to work better on longer messages, up until a point around 33 words. Shorter messages contain more numeric words, such as station identifiers, that appear to be incorrect more often than regular words. Also, as the message gets longer, the effect of poor beginning of message and ending of message transcription are better amoritized over the length of the message. Longer messages may also be more likely to contain more rich data for automated SA, while shorter messages tend to be radio calling and acknowledgement, so while performance is not great overall, perhaps only good performance on longer messages is required for a useful input into a SA use case.
- Missing words seemed to be more common than inaccurate words. This needs to be formally measured.

#### Emergency Service Context

- Often times emergency personnel use word substutions, such as when describing a license plage number. For example, "900 robert King George” could be replaced with “900RKG”to make the output transcriptoin more usable in an automated system.
- The AI model needs to account for regional accents.
- To be useful in a situational awareness use case, addresses are super important to transcribe correctly. The AI model may need to be influenced to correctly identify local street names and businesses over more common english words.
- Emergency frequently use radio codes, such as "10-4" being an acknowledgement of a transmission. These radio codes, as they are numeric based, are suspect to higher inaccuracy. They will also reduce the availability of the transcribed data to be use for automated SA. For example, if a police officer calls in an accident using a radio code, it will more likely be mistranscribed and not be plotable on a GIS SA map. ICS recommends the use of plain language radio speak, and this recommendation may be best for the purpose of improving automated radio transcription as well. Analternative may be trying to use word based radio codes such as "Oscar Mike".

#### Human vs AI Transcription Influences
- Human transcribers are greatly benefited by context cluses, such as mishearing a phrase first, but then corrected it after hearing the same pharase used again later on in the message. The AI transcription model should have a similar correction mechanism.
- Colloquial speech needs to be allowed by the AI transcription model. For example, "Gonna" instead of "Going to".
- Human transcribers may have a bias to susbstitue correct grammar or language. When comparing AI models to human transcription, lenancy should be permitted as long as the message meaning is not lost.

#### Experiment Design
- Human transcription is very time consuming and suspect to thier own bias (such as misspellings, and knowledge of the area, for example, road names).
- Having a high accuracy (meaning close match to human transcription) does not always translate to the meaning of the message being retained. Some words are more important to transcribe correctly to maintain the meaning of the message, or prevent the corruption of the meaning of the message. This is one flaw in this experiment. We need to identify how to measure meaning retainment.
- Amazon presents a confidence report for each word that was not considered in this experiement. In some cases it is likely better to have a INDISCERNABLE than a wrong word. We could replace a low confidence word with INDISCERNABLE to prevent message corruption (a changing of the meaning).
- Manual transcription is a slow process, to continue future experiments we need to crowd source or incentivize multiple people to create a labeled dataset.


#### P25 and Recording Software

- It seems the beginning of recordings are most likely to be GARBLED. This is likely a result of the radio protocol design or user error (such as not waiting long enough after keying the radio to begin speaking). P25 may need to include future additions to address this.
- Improvements in P25 may need to be sought out to improve the clarity of the beginning and end of transmissions. It is also possible that these are created by the recording software and can be fixed at that level.
- GARBLED, SHORT transmission might have been an attempt at a station identification, such as when responding to a caller. These can likely be ignored.
- SILENT, SHORT transmissions might be explained by radio miskeys, or filtered encrypted communications. These can likely be ignored.
- There seems to have been one spurious retransmission in messages 19 and 36. We need to investigate if this is possibly caused by the P25 system or the trunk-recording software.
- The recorded messages contain mixed conversations. This is because of limitations in the design of P25 and the recording software. P25 does not include a message ending signal and the same frequency is used across multiple talkgroups, so the recording software has to record 5 seconds after the last transmitted word at which point a new conversation may have been introduced. Perhaps using talkgroup identifiers (TGIDS) and AI speaker detection, we can create a better transcription of distict speakers and conversations.
- We identified many transmiters that were consistently clearer than others. For example, most animal control related messages in this experiment showed high accuracy. This may be becuase the transmitters are vehicular or base station transmission instead of personal mobile transmission used by a emergency service worker.


### Future Opportunities

Here we present some thoughts on future opportunities.

- This experiement needs to be reconducted on a larger dataset.
- We should look into running this experiment with a better radio system (base station with good antenna location) to maximize reception. This may improve the clarity and correctability of some radio transmissions.
- AWS Transcribe was used manually, via the web GUI, during this experiment. In the future a simple sofware component could be easily implemented to handle this automtically using the AWS API.
- An algorithm needs to be implemented to conduct the word comparision as described in the rules above. It was done by-hand in this experiement.
- There may be an increase in accuracy if we run the sample multiple times through the same service, or make a composite of the sample as run through multiple AI speech-to-text services
- need to write alogrithm to compare human vs AI with these rules
run AWS transcribe multiple times and average?
base station setup for reception?
longer experiment
custom model

measure number of consisten words before mistake (fragmentation) lot's of missing words

## Software Technical Demo

He we outline the processing of real-time P25 transmissions for a simple transcription technical demonstration.

The technical demonstration will:

- Monitor trunk-recorder output directories for new audio `wav` files.
- Upload `wav` files to Amazon S3 bucket.
- Schedule Amazon Transcribe Job on the uploaded file.
- Fetch Amazon Transcribe Job and print result to console / output file inorder of transmission
