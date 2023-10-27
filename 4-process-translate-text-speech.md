# Process and Translate Speech with Azure AI Speech Services

[Learning Path](https://learn.microsoft.com/en-us/training/paths/process-translate-speech-azure-cognitive-speech-services/)

Develop speech enabled applications

## Create Speech Enabled Apps

AI speech provides tools for building speech-enabled apps such as:
- speech to text: API for speech recognition to turn speech into text
- text to speech: API for speech synthesis for app to provide spoken output
- speech translation: api translating spoken input into multiple languages
- speaker recognition: api to recognize speakers based on voice
- intent recognition: api with conversational language understanding to determine semantic meaning of input

Learning Achievements:
- provision speech service
- implement speech to text api
- implement text to speech api
- config audio format and voices
- use speech synthesis markup language

**Provision an Azure resource for speech**
provision and get keys

**Use the Azure AI Speech to text API**
- speech to text api primary way for longer audio
- speech to text short audio api for short audio (<= 60 seconds)
[Speech to text docs](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/rest-speech-to-text)

### process to use the api
1. speechconfig object to connect to speech resource (key and location)
2. optional, audioconfig to define input source (microphone is default)
3. use step 1 and 2 objects to create a speechrecognizer object (proxy client for speech to text)
4. methods to call underlying api
5. process the response (review docs above for structure)
6. if positive, Reason property has enumerated value recognizedspeech, and text property contains transcription. other results for reason; no match: parsed successfully speech not recognized; canceled: indicates error

**Use the text to speech API**
- text to speech api primary way to perform task
- batch synthesis designed for batch large volume of text (create audio book)
[Text to speech docs](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/batch-synthesis)

### process to use the text to speech api
1. speechconfig object to connect to speech resource (key and location)
2. optional, audioconfig to define input source (microphone is default; null value processes audio stream)
3. use step 1 and 2 objects to create a SpeechSynthesizer object (proxy client)
4. methods to call underlying api
5. process the response (review docs above for structure)
6. if positive, Reason property has enumerated value recognizedspeech, and text property contains transcription. other results for reason; no match: parsed successfully speech not recognized; canceled: indicates error

**Configure audio format and voices**
Speech service
speechconfig to customize audio returned by service
types:
- audio file
- sample-rate
- bit-depth
[Formats-supported and calls](https://learn.microsoft.com/en-us/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)

**Voices**
also speech service
two kinds of voices:
- standard: synthetic voices created from audio samples
- neural: more natural sounding creating using deep learning networks
Voices can be set using speechconfig property speechsynthesisvoicename
see link above for docs on how to call specific voices

**Use Speech Synthesis Markup Language**
a markup language that enables an AI engineer control over language characteristics of voices such as timbre, timing, phonetics, speaking style and more
SSML example:
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
                     xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US"> 
    <voice name="en-US-AriaNeural"> 
        <mstts:express-as style="cheerful"> 
          I say tomato 
        </mstts:express-as> 
    </voice> 
    <voice name="en-US-GuyNeural"> 
        I say <phoneme alphabet="sapi" ph="t ao m ae t ow"> tomato </phoneme>. 
        <break strength="weak"/>Lets call the whole thing off! 
    </voice> 
</speak>

## Translate speech
***process to use the text to speech api***
1. SpeechTranslationConfig  object to connect to speech resource (key and location). Also used to identify language of speech being spoken and target translation language
2. optional, audioconfig to define input source (microphone is default; null value processes audio stream)
3. use step 1 and 2 objects to create a TranslationRecognizer  object (proxy client)
4. methods to call underlying api
5. process the response (review docs above for structure)
6. If the operation was successful, the Reason property has the enumerated value RecognizedSpeech, the Text property contains the transcription in the original language. You can also access a Translations property which contains a dictionary of the translations (using the two-character ISO language code, such as "en" for English, as a key).

**synthesize translations**
translationrecognizer: translates audible speech to text
can accomplish speech-to-speech by:
1. Event-based synthesis: a 1-to-1 translation only (spoken language to a single target language)
   how: specify desired voice in translationconfig; create event handler for translationrecognizer object synthesizing event with GetAudio() of Result parameter
2. manual synthesis: doesn't require event handler. can do 1:N languages
