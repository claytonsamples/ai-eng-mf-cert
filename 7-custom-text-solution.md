# Build custom text analytics solutions
- types of classifciaton projects
- build custom classification project
- tag data, train, and deploy a model
- submit classification tassk from own app

## create custom text classification solution
types of classifciatons projects:
- single label: one class e.g. adventure or strategy
- multiple label: multiple classes to each file

**single vs. multiple projects**
**label data**
single: each file assigned 1 class
multiple: assign as many classes per file

**evaluating and improving your model**
false positive: predicts x, but file isn't labled x
false negative: doesn't predict x, but file is in fact x

Azure AI translates these measures into 3 metrics:
recall: true positives to all positives (how many predicted classes are correct)
precision: true positives to all identified positives (e.g. how many predicted labels are correct)
f1: function of recall and precision
[eval metrics](https://learn.microsoft.com/en-us/azure/ai-services/language-service/custom-text-classification/concepts/evaluation-metrics?azure-portal=true)

## Understand how to build text classification projects
project lifecycle
- define labels
- tag data
- train model
- view model
- improve model (circle back to tag data where needed)
- deploy model
- classify text [api docs](https://learn.microsoft.com/en-us/rest/api/language/2023-04-01/text-analysis-runtime)

**split dataset for training**
train: 80% recommended
testing: returns results to calc performance metrics

two split methods:
automatic: azure taakes all data, splits by percentages randomly, applies to training. put all files into training dataset when labeling
manual: manual specification of which files should be in each dataset. specify which files in training during labeling

**deployment options**
example submit to POST to train
<YOUR-ENDPOINT>/language/analyze-text/projects/<PROJECT-NAME>/:train?api-version=<API-VERSION>

submit POSTS to all steps, but prefixed with endpoint provided by Ai language resource created

## Custom named entity recognition
learning achievements
- tagging entities in extraction projects
- build entity recognition projects
- custom named entities and how they are labled
- build a custom named entity extraction prjoect
- label data, train, and deploy an entity extraction model
- submit extraction tasks from own app

entity is a person, place, thing, event, skill, or value

**customer vs. built-in NER**
built-in: recognizes person, location, organizations, or URL [ner docs](https://learn.microsoft.com/en-us/azure/ai-services/language-service/named-entity-recognition/concepts/named-entity-categories?azure-portal=true&tabs=ga-api)
example call to built-in: <YOUR-ENDPOINT>/text/analytics/v3.2-preview.2/entities/recognition/general

custom: legal, bank, or knowledge mining to enhance catalog search, audit policies. each requires specific set of entities and data it needs to extract

**Considerations for data selection and refining entities**
diversity: data represent as many different sources without losing real-life distribution expected
distribution: appropriate distribution of document types
accuracy: as close to real world as possible. synthetic can work to start training, but real data is of higher quality

entities must be distinctly identifiable. model will struggle to differentiate otherwise

**How to extract entities**
extraction task api: documents are a list of dictionaries with id and text key's.

**project limits**
training: at least 10 files not more than 100,000
deployments 10 deployment names per project
APIs:
-authoring: creates project, trains, deploys model. Limits to 10 POST and 100 GET per minute
-analyze: extraction API; requests task and retrieves results. limited to 20 GET or POST
projects: only 1 per account, 500 per resource, 50 trained models per project
entities: each entity must be fewer than 10 words and 100 characters, up to 200 entity types, at least 10 tagged instances per entity

**Label data**
consistency: label same way across all files for training
precision: consistnet label, without unnecessary extra words.
completeness: don't miss any entities

**How to**
language studio is the most straight forward method for labeling data
table:
|Field|	Description|
--|--|
|entityNames|	Array of entities to extract|
|documents|	Array of labeled documents|
|location|	Path to file within container connected to the project|
|culture|	Language of the file|
|entities|	Array of present entities in the current document|
|regionStart	|Inclusive character position for start of text|
|regionLength|Length in characters of the data used in training|
|labels|	Array of labeled entities in the files|
|entity|	Which entity this label references, by index in entityNames|
|start|	Inclusive character position for start of entity|
|length|	Length in characters of the entity|
|datasets|	Which dataset the file is assigned to|

**train and evaluate**
