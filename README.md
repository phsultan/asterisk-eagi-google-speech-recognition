# Transcribe audio in Asterisk with Google Speech Recognition

This repository is an example of how to use Google Speech Recogntion in Asterisk to transcribe audio voice. The EAGI (Extended Asterisk Gateway Interface) program in this repository basically gets the audio input stream as a file descriptor, passes it to a Node.js Google Cloud Speech client and sets an Asterisk dialplan variable to return the transcription result to the dialplan.

## Requirements

- set up your Google Cloud Speech project along with a Service Account key at https://cloud.google.com/speech/
- install Node.js Google Cloud Speech client at https://github.com/googleapis/nodejs-speech

## How to use this repository

### Asterisk

Edit your `extensions.conf` according to the example given in this repository :

```
exten = 6500,1,verbose(1, "User ${CALLERID(num)} dialed extension 6500. Testing Google Speech Recognition")
 same = n,noop(CHANNEL(audioreadformat) : ${CHANNEL(audioreadformat)})
 same = n,answer
 same = n,execif($[ "${CHANNEL(audioreadformat)}" = "opus" ]?set(EAGI_AUDIO_FORMAT=slin48):noop(Won't set EAGI_AUDIO_FORMAT))
 same = n,eagi(transcribeWithGoogle.eagi,fr-FR,2)
 same = n,noop(GOOGLE_TRANSCRIPTION_RESULT : ${GOOGLE_TRANSCRIPTION_RESULT})
 same = n,hangup()
```

Copy the `transcribeWithGoogle.eagi` of this repository to `/var/lib/asterisk/agi-bin/transcribeWithGoogle.eagi`.

Call extension 6500 and inspect your Asterisk CLI to get the transcription, the `EAGI` script assigns the transcriptio result to the `GOOGLE_TRANSCRIPTION_RESULT` variable.
