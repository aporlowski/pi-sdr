# Notes


## TODO

1. ~~Add references section to radio tutorial~~
    - STATUS: COMPLETED. Also did general spell check and editing.
2. Make project poster (Are there details on this requirement, or is my tutorial link sufficient?)
3. Demonstrate transcription by measuring Amazon Transcribe transcription compared to my human comprehension on ~10 minutes of radio broadcasts.
4. Write software to automatically transcribe trunk-recorder recording outputs.
    - Monitor trunk-recorder outputdirectoriesfor new wav filesb.
    - Upload wav files to Amazon S3 bucket.
    - Schedule Amazon TranscribeJobd.
    - Fetch Amazon Transcribe Joband print result to console / output file inorder oftransmission
5. Make Docker image or file to launch trunk-recorder, install and run my software
6. Document results of speech-to-text investigation.
7. Complete project submission



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

## Analysis
run AWS transcribe multiple times and average?

measure number of consisten words before mistake (fragmentation)
lot's of missing words


gaps in numbers okay as long as no other additives really need alpha unit identifiers
same with conjoined numbers that should have gaps

if reception or error can be measured then those messages can be skipped

beginnings and ends commonly wrong or missing

sometimse having a high % match still losses too much meaning if wrong words are missing or wrong

seems to have gotten a spurious retransmission is that P25 or the software?

consistent good results with specific units (animal control), perhaps need to repeat this with a base station setup for reception?

addresses are super important to get right

regional accents

future work: build custom model

604.... Oh for.


