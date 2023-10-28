# Create a language understanding solution

[Learning Path](https://learn.microsoft.com/en-us/training/paths/create-language-solution-azure-cognitive-services/)

## Build a converstaional language understanding model

[Learning Module](https://learn.microsoft.com/en-us/training/modules/build-language-understanding-model/)

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
pOST request to endpoing: e.g. {ENDPOINT}/language/authoring/analyze-conversations/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}?api-version={API-VERSION}
|Placeholder|	Value	Example|
---|---|
|{ENDPOINT}|	The endpoint of your Azure AI Language resource|	https://<your-subdomain>.cognitiveservices.azure.com|
|{PROJECT-NAME}|	The name for your project. This value is case-sensitive|	myProject|
|{DEPLOYMENT-NAME}|	The name for your deployment. This value is case-sensitive	|staging|
|{API-VERSION}|	The version of the API you're calling|	2022-05-01|

will receive 202 if successful with response header of operation-location wiht a URL to request status
{ENDPOINT}/language/authoring/analyze-conversations/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version={API-VERSION}

**get deployment status**
GET request to {ENDPOINT}/language/authoring/analyze-conversations/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version={API-VERSION}
|Placeholder	|Value|
---|---|
|{ENDPOINT}	|The endpoint for authenticating your API request|
|{PROJECT-NAME}	|The name for your project (case-sensitive)|
|{DEPLOYMENT-NAME}|	The name for your deployment (case-sensitive)|
|{JOB-ID}	|The ID for locating your model's training status, found in the header value detailed above in the deployment request|
|{API-VERSION}|	The version of the API you're calling|
