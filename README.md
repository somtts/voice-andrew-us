# voice-andrew-us

| Voicebuilding for Text-to-speech Synthesis (WS 2016/17) |
| ------------------------------------------------------- |
| Group 1                                                 |
| Christophe Biwer, Dana Ruiter, Andrew Johnson           |

## Report
Notes for the group report can be found [here](https://hackmd.io/OwMxGMCMCYDYFMC0AOAjMALIjAGewUBWATgJGmGkNWhwGYdl4g==) (registration required to edit).

## Dependencies
In order to work with this voice, the following command must be executed (on ubuntu) to get the desired dependencies:
```
sudo apt sox praat speech-tools
```
There may be other requirements which need to be obtained via `apt`.

## Installation of the unit selection based voice
Run 
```
./gradlew legacyInit
```
followed by 
```
./gradlew build
```

To actually test the voice, enter
```
./gradlew run
```
and go to [localhost:59125](http://localhost:59125).

## Installation of the HMM based voice
### Install docker
... as explained [here](https://docs.docker.com/engine/installation/)

### Install the required container / image
Execute the dockerfile contained in the root folder of the projet (which originates from [https://github.com/psibre/marytts-dockerfiles](https://github.com/psibre/marytts-dockerfiles)) and run it with
```
sudo docker build --build-arg HTKUSER=***** --build-arg HTKPASSWORD=***** -t marytts-builder-hsmm .
```
replace `HTKUSER` and `HTKPASSWORD` with the desired credentials ([Registration](http://htk.eng.cam.ac.uk/register.shtml) needed).

*I guess the unit selection based voice must already be installed to continue!*

### Meanwhile, because downloading the docker files may take some time, you can do the following:

Download the [MaryTTS Builder](https://github.com/marytts/marytts/releases/download/v5.2/marytts-builder-5.2.zip) and unpack it to *some location*

Go to the build directory of this project and run *some location*`/bin/voiceimport.sh`. Click on `Settings` and set `db.marybase` to `/marytts`. Don't forget to **save**.

Run the `HMMVoiceFeatureSelection` component.

### After docker has been installed:

Go to the build folder and start the docker container. By using the following command, you automatically execute the needed tasks:
```
sudo docker run -v `pwd`:`pwd` -w `pwd` -it marytts-builder-hsmm /bin/bash 'dockerScript.sh'
```
(the `dockerScript.sh` contained in the root folder of the project is the one that is tracked by git. Running `./gradlew legacyInit` copies it to the build folder)

Alternatively you cant run the following command:
```
sudo docker run -v $PWD:$PWD -t marytts-builder-hsmm bash -c "cd $PWD; /marytts/target/marytts-builder-5.2/bin/voiceimport.sh HMMVoiceDataPreparation HMMVoiceConfigure HMMVoiceMakeData HMMVoiceMakeVoice" 
```

You can track the process by running the following in another terminal window:
```
tail -f hts/log-XXX
```

### Compile the voice:
```
docker run -v $PWD:$PWD -t marytts-builder-hsmm bash -c "cd $PWD; /marytts/target/marytts-builder-5.2/bin/voiceimport.sh HMMVoiceCompiler"
```
It won't succeed but don't worry, that's perfectly fine.

### Run the voice
**You may now have to change permissions because the created folders belong to root**
```
chown christophe:christophe -R build/mary/voice-XXX
```

Run the gradle script in the folder `build/mary/voice-XXX` with `gradle run` or `gradle build`.
and go to [localhost:59125](http://localhost:59125).
