# Build a quesiton answering solution
[Learning Path](https://learn.microsoft.com/en-us/training/paths/build-qna-solution/)

Learning achievements:
- Describe the question answering capabilities of the Azure AI Language service.
- Describe the differences between question answering and conversational language understanding.
- Create a knowledge base.
- Implement multi-turn conversation.
- Test and publish a knowledge base.
- Consume a published knowledge base.
- Implement active learning.
- Create a question answering bot.

**Understand QA**
knowledge base = question and answer pairs

**Compare question answering to Azure AI Language understanding**
table:
|Use Case|Question answering	|Language understanding|
--|--|--|
|Usage pattern|	User submits a question, expecting an answer	|User submits an utterance, expecting an appropriate response or action|
|Query processing|	Service uses natural language understanding to match the question to an answer in the knowledge base|	Service uses natural language understanding to interpret the utterance, match it to an intent, and identify entities|
|Response|	Response is a static answer to a known question|	Response indicates the most likely intent and referenced entities|
|Client logic|	Client application typically presents the answer to the user	|Client application is responsible for performing appropriate action based on the detected intent|

services are complimentary

**create a knowledge base**
- Ai language resource enable question answering feature
- create cognitive search resource to host knowledge base index
- select Language resource create customer question answering project
- name knowlege base
- add data sources (urls web page containing FAQ, file containing structured text where QA can be derived, pre-defined chit-chat datasets
- create knowledge base and edit question and answer pairs

  **implement multi-turn conversation**
  ask questions to user to get more info

  **est and publish a knowledge base**
  **Use a knowledge base**
  - request body is json with question
  - response: closest question match found in knowledge base with answer, confidence score, and other metadata

  **Improve question answering performance**
  - active learning
  - synonyms

  active learning:
  - implicit feedback: suggestions from user questions clustered as alternative phrases to similarly scored knowledge base phrases. suggestions page gives accept and reject function
  - explicit feedback: control possible matches json format: {'question':'','top':3}
 
  synonyms:
  - straight forward definition e.g. {'synonyms':[{'alterations':['reservation','booking',,]}]}
 
  **Create a question answering bot**
  bot = conversational application that enables users to interact using natural language through one or more channels
