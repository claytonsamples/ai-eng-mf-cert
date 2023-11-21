# Decision support solutions
Decision support helps automate decision making by providing recommendations to users\

## classify and moderate text with content moderator
learning achievements:
- learn text moderator
- identify key features
- test moderation of text with api

**overview**
block, approve, or review content based on policies and thresholds\
implemnted in places like:
- chatbots
- discusson boards
- chat rooms
- e-commerce catalogs
- documents

text moderation response:
- list of potentially unwanted words found in text
- type of potentially unwanted words
- possible personal data

***profanity***\
profane item located in "item" and you can use custom term lists\
"Terms": [
{
    "Index": 118,
    "OriginalIndex": 118,
    "ListId": 0,
    "Term": "crap"
}

***classification***\
category 1: presence of language that might be considered sexually explicity or adult in certain situations\
category 2: presence of language that might be considered sexually suggestive or mature in certain situations\
category 3: presence of language that might be considered offensive

json response returns each category with prediction and ReviewRecommended: true should be reviewed manually\

***personal data***
Email addresses\
US mailing addresses\
IP addresses\
US phone numbers\
UK phone numbers\
Social Security numbers\

sample response:
"PII": {
    "Email": [{
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 32
        }],
    "IPA": [{
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 72
        }],
    "Phone": [{
        "CountryCode": "US",
        "Text": "5557789887",
        "Index": 56
        }, {
        "CountryCode": "UK",
        "Text": "+44 123 456 7890",
        "Index": 208
        }],
    "Address": [{
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 89
        }],
    "SSN": [{
        "Text": "999-99-9999",
        "Index": 267
        }]
    }

**Create and subscribe to a Content Moderator resource**
create resource and get subscription key for accessing resource\

## Make recommendations with AI personalizer
learning achievements:
- create azure ai personalizer resource
- config learning loop and learning behavior
- import export learning and model settings
- use inference explainability and run evaluations

**Create Azure AI Personalizer resource**
uses reinforcement learning: best action given specific context\
Context: total information that represents the state of the app, scenario, or user\
action: sets of items that can be chose along with attributes that describe item\
reward: 0 (bad) to 1 (good)\

personalizer run through api\
app calls Rank API whenever decision is needed (called an event)\
call in json sends, description of set of actions, description of each action, and description of set of context\
then it calls the reward api to determine if action id returned was valuable\

create a azure ai personalizer learning loop for each content area or subject domain

***cli to create resource***\
all in bash\

sign in:
az login

create resource group:
az group create \
    --name my-personalizer-resource-group \
    --location westus2


create resource:
az cognitiveservices account create \
    --name my-personalizer-learning-loop \
    --resource-group my-personalizer-resource-group \
    --kind Personalizer \
    --sku F0 \
    --location westus2 \
    --yes

get key:
az cognitiveservices account keys list \
    --name my-personalizer-learning-loop \
    --resource-group my-personalizer-resource-group

**Configure a learning loop**
- reward wait time: how long personalizer should collect reward values for rank call
- default reward: typicall 0
- reward aggregation: sum or earliest when multiple rwards received

***configure exploration8**
change value will reset model instance and maintain last two days of data\

***configure model update frequency***
minutes, hours available based on need\

**Configure learning behavior**
apprentice mode: takes choices made by current logic and mimics decisions\

once apprentice mode reaches 75-85% efficacy then switch to online mode\

***configure app to call rank and reward***\
In your app's existing logic, you'll add a call to the Rank API where you currently determine a list of actions and features. Ensure that the chosen action by your app's existing logic is the first item in this list.\

You change your app's code to show the action associated with the Reward Action ID that is returned in the Rank API response.\

Next, you'll use your app's existing business logic to calculate the reward for the selected action. A valid reward value ranges between 0 to 1.\

You then send the reward to the Reward API. You need to send the reward within the Reward wait time period you've configured earlier, otherwise the default reward will be logged by AI Personalizer.\

***Evaluate Apprentice mode then switch to Online mode***\
Baseline – average reward: Average reward of the application’s default business logic (baseline).\
Personalizer – average reward: Average of total reward your AI Personalizer could potentially have reached.\
Reward achievement ratio over most recent 1000 events: A ratio that represents the Baseline and Personalizer reward.\

**Import and export model and learning settings**
import/export tab export model config\
learning settings describe hyperparams\

***clear data for learning loop***\
navigate to configuration tab, select clear data\
Logged personalization and reward data: This is log data that is used for offline evaluations. You select this option to remove that data.\
Reset the Personalizer model: You select this to reset the underlying model.\
Set the learning policy to default: If you've ever changed your learning policy, this will help you reset it to the original version.\

**use inference explainability**
helps explain what specific features have most or least influence\
adding feature scores for different features\

***enable inference explainability***
update service config by PUT request to {Your-Endpoint}/personalizer/v1.1-preview.1/configurations/service
{
  "rewardWaitTime": "PT10M",
  "defaultReward": 0,
  "rewardAggregation": "earliest",
  "explorationPercentage": 0.2,
  "modelExportFrequency": "PT5M",
  "logRetentionDays": 7,
  "isInferenceExplainabilityEnabled": true
}

|option|	Description|
--|--|
|defaultReward	|The reward to assign if a reward isn't received within the specified time.|
|rewardWaitTime|	The time span to wait until a request is marked with the default reward and should be between five seconds and two days. For example, PT10M (10 mins).|
|rewardAggregation|	The function to use to process rewards, in case multiple reward scores are received before rewardWaitTime has ended.|
|explorationPercentage|	The percentage of rank responses that should use exploration.|
|logRetentionDays	|Number of days to keep historical log data. You can use -1 to ensure the logs won't ever be deleted.|
|isInferenceExplainabilityEnabled|	Set to true to enable inference explainability and false to disable it.|

[service config docs](https://learn.microsoft.com/en-us/rest/api/personalizer/1.1preview1/service-configuration/update?tabs=HTTP)























