# Transcribe audio in Asterisk with Google Speech Recognition

This repository is an example of how to use Google Speech Recogntion in Asterisk to transcribe audio voice.

Tools that are used here :
- Asterisk EAGI (Extended Asterisk Gateway Interface), in bash
- Node.js Google Cloud Speech client
 
# Set up your Google Cloud Speech account and Node.js client
1.  Select or create a Cloud Platform project.

    [Go to the projects page][projects]

1.  Enable billing for your project.

    [Enable billing][billing]

1.  Enable the Google Cloud Speech API API.

    [Enable the API][enable_api]

1.  [Set up authentication with a service account][auth] so you can access the API from your local workstation.

Note : in `transcribeWithGoogle.eagi`, we assume that the generated Service Account `.JSON` file is stored in `/usr/local/node_programs/service_account_file.json`

[projects]: https://console.cloud.google.com/project
[billing]: https://support.google.com/cloud/answer/6293499#enable-billing
[enable_api]: https://console.cloud.google.com/flows/enableapi?apiid=speech.googleapis.com
[auth]: https://cloud.google.com/docs/authentication/getting-started

## Install the client library
    git clone https://github.com/googleapis/nodejs-speech.git
    cd nodejs-speech
    npm install --save @google-cloud/speech
    
Note : we assume that the `nodejs-speech` directory sits under `/usr/local/node_programs/`. Adjust to your configuration. 

# Set up Asterisk

Edit your `extensions.conf` according to the example given in this repository :

```
exten = 6500,1,verbose(1, "User ${CALLERID(num)} dialed extension 6500. Testing Google Speech Recognition")
 same = n,noop(CHANNEL(audioreadformat) : ${CHANNEL(audioreadformat)})
 same = n,answer
 same = n,execif($[ "${CHANNEL(audioreadformat)}" = "opus" ]?set(EAGI_AUDIO_FORMAT=slin48):noop(Won't set EAGI_AUDIO_FORMAT))
 ; Usage : eagi(transcribeWithGoogle.eagi,<language>,<timeout>)
 ; <language> : en-US, fr-FR, etc.
 ; <timeout>  : the time to record audio input, in seconds
 same = n,eagi(transcribeWithGoogle.eagi,fr-FR,2)
 same = n,noop(GOOGLE_TRANSCRIPTION_RESULT : ${GOOGLE_TRANSCRIPTION_RESULT})
 same = n,hangup()
```

Copy the `transcribeWithGoogle.eagi` of this repository to `/var/lib/asterisk/agi-bin/transcribeWithGoogle.eagi`.

Call extension 6500 and inspect your Asterisk CLI to get the transcription, the `EAGI` script assigns the resulting text to the `GOOGLE_TRANSCRIPTION_RESULT` variable.
