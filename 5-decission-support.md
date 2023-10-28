# Create a language understanding solution

[Learning Path](https://learn.microsoft.com/en-us/training/paths/create-language-solution-azure-cognitive-services/)

### Build a converstaional language understanding model

[Learning Module](https://learn.microsoft.com/en-us/training/modules/build-language-understanding-model/)
[Example](https://learn.microsoft.com/en-us/azure/ai-services/language-service/conversational-language-understanding/quickstart?pivots=rest-api#create-a-clu-project)

Learning Achievements:
- provision
- define intents, utterances, and entities
- patterns to differentiate similar utterances
- pre-build entity components
- train,test, publish, and review 

**Understand resources for building a conversational language understanding model**
provision language understanding service

**build model**
- build, train, and deploy

**Rest API**
steps are asynch:
- create project
- import data
- train
- deploy
- use model
will need to submit a request to URI for each step and another request to get status of the job

**Authentication**
| Key	| Value |
---|---|
| Ocp-Apim-Subscription-Key	| The key to your resource |

**request deployment**
pOST request to endpoing
- {ENDPOINT}/language/authoring/analyze-conversations/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}?api-version={API-VERSION}

|Placeholder|	Value	Example|
---|---|
|{ENDPOINT}|	The endpoint of your Azure AI Language resource|	https://<your-subdomain>.cognitiveservices.azure.com|
|{PROJECT-NAME}|	The name for your project. This value is case-sensitive|	myProject|
|{DEPLOYMENT-NAME}|	The name for your deployment. This value is case-sensitive	|staging|
|{API-VERSION}|	The version of the API you're calling|	2022-05-01|

will receive 202 if successful with response header of operation-location wiht a URL to request status
- {ENDPOINT}/language/authoring/analyze-conversations/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version={API-VERSION}

**get deployment status**
GET request to 
- {ENDPOINT}/language/authoring/analyze-conversations/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version={API-VERSION}

|Placeholder	|Value|
---|---|
|{ENDPOINT}	|The endpoint for authenticating your API request|
|{PROJECT-NAME}	|The name for your project (case-sensitive)|
|{DEPLOYMENT-NAME}|	The name for your deployment (case-sensitive)|
|{JOB-ID}	|The ID for locating your model's training status, found in the header value detailed above in the deployment request|
|{API-VERSION}|	The version of the API you're calling|

**query model**
POST request to URL
built in features such as language detection and sentiment analysis query analyze-text
- {ENDPOINT}/language/:analyze-text?api-version={API-VERSION}

  |Placeholder|	Value|
  ---|---|
|{ENDPOINT}	|The endpoint for authenticating your API request|
|{API-VERSION}	|The version of the API you're calling|

### Define intents, utterances, and entities
- utterances phrases a user might enter
- intent is task or action user wants to perform (meaning of utterance)
examples: GetTime (intent) ; What time is it? (utterance)
- entities add context to intents

process:
- consider domain and intents model needs
- None intent for all models is for no specific action (conversational greetings like 'hello')

guidelines for utterance examples:
- Capture multiple different examples, or alternative ways of saying the same thing
- Vary the length of the utterances from short, to medium, to long
- Vary the location of the noun or subject of the utterance. Place it at the beginning, the end, or somewhere in between
- Use correct grammar and incorrect grammar in different utterances to offer good training data examples
- The precision, consistency and completeness of your labeled data are key factors to determining model performance.
- Label precisely: Label each entity to its right type always. Only include what you want extracted, avoid unnecessary data in your labels.
- Label consistently: The same entity should have the same label across all the utterances.
- Label completely: Label all the instances of the entity in all your utterances.

entity examples:
|Utterance|	Intent|	Entities|
  ---|---|---|
|What is the time?|	GetTime	|
|What time is it in London?|	GetTime	|Location (London)|
|What's the weather forecast for Paris?|GetWeather	|Location (Paris)|
|Will I need an umbrella tonight?	|GetWeather|	Time (tonight)|
|What's the forecast for Seattle tomorrow?	|GetWeather|	Location (Seattle), Time (tomorrow)|
|Turn the light on.	|TurnOnDevice|	Device (light)|
|Switch on the fan.	|TurnOnDevice	|Device (fan)|

split entities into components
- Learned entities: most flexible define with suitable name and then associate words or phrases in training utterances
- list entities: useful for set possible values e.g. days of week
- [prebuilt entities](https://learn.microsoft.com/en-us/azure/ai-services/language-service/conversational-language-understanding/prebuilt-component-reference)

**Use patterns to differentiate similar utterances**
use case to try and separate multiple intents for single utterance
examples
TurnOnDevice:
- "Turn on the {DeviceName}"
- "Switch the {DeviceName} on"
- "Turn on the {DeviceName}"
GetDeviceStatus:
- "Is the {DeviceName} on[?]"
TurnOffDevice:
- "Turn the {DeviceName} off"
- "Switch off the {DeviceName}"
- "Turn off the {DeviceName}"
When you teach your model with each different type of utterance, the Azure AI Language service can learn how to categorize intents correctly based off format and punctuation.

**Use pre-built entity components**
see link above in entitiy example section

**Train, test, publish, and review a conversational language understanding model**
- Train a model to learn intents and entities from sample utterances.
- Test the model interactively or using a testing dataset with known labels
- Deploy a trained model to a public endpoint so client apps can use it
- Review predictions and iterate on utterances to train your model
