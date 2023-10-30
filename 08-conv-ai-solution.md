# Create conversational AI solution
## create a bot with Bot Framework SDK
activities are events e.g. join a convo or send message
flow of activities is a dialog, manage a multi-turn convo

**factors influencing bot's success**
great user experience
- is it discoverable?
- is it intuitive and easy to use?
- is it avaiable on devices and platforms users care about?
- can users solve their problems with minimual use & interaction?
- does the bot solve the user issues better than alternative experiences?

**factors that do not guarantee success**
complexity (advanced ML) over simplicity(simple task to replace)

**responsible ai**
[responsible ai docs](https://www.microsoft.com/en-us/research/publication/responsible-bots/)

Azure AI Bot Service. A cloud service that enables bot delivery through one or more channels, and integration with other services.
Bot Framework Service. A component of Azure AI Bot Service that provides a REST API for handling bot activities.
Bot Framework SDK. A set of tools and libraries for end-to-end bot development that abstracts the REST interface, enabling bot development in a range of programming languages.

**developing a bot with framework sdk**
3 templates
- empty: basic skeleteon
- echo: simple; echoes message text back to user
- core: comprhensive, common functionality and integrated with language understanding service

**bot application classes and logic**
Adapter class handles communication
framework service notifies adapter when activity occurs calling Process activity method,
adapter creates context for turn and call's bot Turn Handler

**testing**
bot framework emulater enables testing local or remote and connect to interactive web chat interface

**implement activity handlers and dialogs**
Activity handlers: Event methods that you can override to handle different kinds of activities.
Dialogs: More complex patterns for handling stateful, multi-turn conversations.

**activity handlers**
[bot framework sdk](https://learn.microsoft.com/en-us/azure/bot-service/bot-activity-handler-concept?view=azure-bot-service-4.0&tabs=csharp)

**dialogs**
component dialogs: think pizza ordering bot size, toppings
adaptive dialogs: allows for interruptions, cancellations, and context switches at any point in convo
- root contains the dialog and flow of actions, triggers initiated by actions or by recognizer. recognizer analyzes natural language input and detects intents which can be mapped to triggers

**deploy a bot**
need azure application registration
register an azure app: az ad app create (cli)
bot channels registration resource and associated application service and plan: az deployment group create


## create a bot with bot framework composer


