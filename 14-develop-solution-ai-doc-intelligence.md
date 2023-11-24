# Develop solutions with Azure AI Document Intelligence
learning achievement
- design solution that analyzes your business forms by using azure ai doc intelligence
- create a solution that analyzes common documents by using document intelligence
- create a solution that analyses different custom form types by using document intelligence
- Include Azure ai doc intelligence service as custom skill in azure cog search pipeline

## plan azure ai doc intell solution
learning achievement
- describe components of azure doc intell
- create and connect to azure ai doc intelligence resources
- choose whether to use prebuilt, custom, or composed model

**Understand AI Document Intelligence**\
how do you create a reliable solution?

alternative to data entry\
ai principles\
Fairness. All AI systems should treat people fairly, regardless of race, belief, gender, sexuality, or other factors.\
Reliability and safety. All AI systems should give reliable answers with quantifiable confidence levels.\
Privacy and security. All AI systems should secure and protect sensitive data and operate within applicable data protection laws.\
Inclusiveness. All AI systems should be available to all users, regardless of their abilities.\
Transparency. All AI systems should operate understandably and openly.\
Accountability. All AI systems should be run by people who are accountable for the actions of those systems.\

***Using models with Azure AI Document Intelligence***
can use model to inform ai doc intelligence what type of data expected to be in forms\
json output format from doc intell\
3 models for general analysis:
1. read
2. general document
3. layout
other prebuilds:
invoice, receipt, w-2, id card, business card, health insurance\
composed model: associate multiple model, in single model. enables service different document types to single service. identifies doc type and selects most appropriate custom model

***Azure AI Document Intelligence and Azure AI Vision***\
extract simple words and text from pic of form or document, without context, choose ai vision ocr\

***Azure AI Document Intelligence tools***\
[doc intel studio docs](https://formrecognizer.appliedai.azure.com/)

**Plan Azure AI Document Intelligence resources**
free is not available in multi-service resource

***Create an Azure AI Document Intelligence resource***\
***Connect to Azure AI Document Intelligence***\
write app need:
endpoint: url resource can be contacted
access key: authenticate

example code:
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential

# set `<your-endpoint>` and `<your-key>` variables with the values from the Azure portal
endpoint = "<your-endpoint>"
key = "<your-key>"

docUrl = "<url-of-document-to-analyze>"

# create your `DocumentAnalysisClient` instance and `AzureKeyCredential` variable
document_analysis_client = DocumentAnalysisClient(endpoint=endpoint, 
    credential=AzureKeyCredential(key))

poller = document_analysis_client.begin_analyze_document_from_url(
    "prebuilt-document", docUrl)
result = poller.result()

[doc intell resource](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/create-document-intelligence-resource)
[doc intell quickstart](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/quickstarts/get-started-sdks-rest-api)
[service quotas and limits](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/service-limits)

**Choose a model type**
uses models to describe documents and forms to expect\

***prebuild models***\
rely on key-value pair structures to extra e.g. invoices total cost, sum etc same key just named different\

***General document analysis models***\
3 prebuilts handle general docs, extract words, lines, structures and other info such as language of doc\
1. Read: model to extract words and lines from both printed and hand-written docs. also detects the language
2. general doc: model to extract key value pairs and tables
3. layout: extract tables, text, structure information from forms. can also recognize selection marks such as check boxes and radio buttons

***Specific document type models***\
prebuilds to specific docs\
invoice: extract key information from sales invoices in English and Spanish.\
receipt: extract data from printed and handwritten receipts.
w-2: data from United States government's W-2 tax declaration form.
id doc: data from United States driver's licenses and international passports.
business card:  extract names and contact details from business cards.

***custom models***\
train custom model needs 5 examples of completed form\
more samples increased confidence levels\
increase in variety will lead to high sample requirements\
two kinds of custom models:
1. custom template model: consistent visual template. remove all user entered data and if the forms are the same use custom template model. support 9 different languages for handwritten text; wide range for printed text
2. custom neural model: works across structured and unstructured. work on english with highest accuracy and marginal drop for latin based german, french, italian, spanish, and dutch

***composed models***\
consists of multiple custom models\
typical scenario for use: don't know document type, want to classify it and then analyze\

standard pricing tier up to 100 custom models into single composed model. free tier only up to 5 custom model

## use prebuilt azure ai doc intell models
learning achievements:
- identify business problems that you can solve using prebuilt models in azure ai doc intell
- analyze forms by using the general document, read, and layout models
- analyze forms by financial, id, and tax prebuilt models

**Understand prebuilt models**
***what are prebuilt models***\
prebuilt models are trained on specific form types:
Invoice model. Extracts common fields and their values from invoices.\
Receipt model. Extracts common fields and their values from receipts.\
W2 model. Extracts common fields and their values from the US Government's W2 tax declaration form.\
ID document model. Extracts common fields and their values from US drivers' licenses and international passports.\
Business card model. Extracts common fields and their values from business cards.\
Health insurance card model. Extracts common fields and their values from health insurance cards.\
3 types of models with less specific structures: read, general, layout

***features of prebuilt models***\
requirements to select right model:
- text extraction: all prebuilt models extract lines of text and words from hand-written and printed text
- key-value pairs: identify a label or key and its response ore value are extracted by many models as key-values pairs
- entities: text inclusive of common, more complex data structures can be extracted as entities. include people, locations, and dates
- selection marks: radio buttons and selection marks indicates a choice can be extracted
- tables: many models can extract tables in scanned forms included the data contained in cells, # of cols and rows, and col and row headings. tables with merged cells are supported
- fields: specific form type identify the values of fixed set of fields

***input requirements***\
flexible and can improve results with submission of 1 clear photo or high-quality scan for each document\
additional requirements:
1. file must be in jpeg, png, bmp, tiff, or pdf format. Read can accept microsoft office files
2. file must be smaller than 500 MB for standard tier, 4MB for free
3. image dimensions between 50x50 pixels and 10,000x10,000 pixels
4. pdf doc must have dimensions less than 17x17 inches or A3 paper size
5. pdf doc must not be protected
note pdf and tiff files can have any number of pages but standard tier only first 2000 are analyzed and free only first two\

***compare prebuilt models***\
|model|text extraction|key-value pairs|Entities|selection marks|tables|fields|
--|--|--|--|--|--|--|
|Read|	X	||||||
|General document|	X|	X	|X	|X|	X|	|
|Layout|	X	|||		X	|X|	|
|Invoice|	X	|X|		|X|	X|	X|
|Receipt|	X|	X|	|	|	|	X|
|W2|	X	|X|		|X	|X|	X|
|ID document|	X	|X|	|		|	|X|
|Business card|	X|	X	|	|	|	|X|
[doc intell studio]](https://formrecognizer.appliedai.azure.com/studio)

***calling prebuilt models by using apis***\
apis available for: C# and other .NET languages, java, python, javaScript\

to make a connection you need service endpoint (value is the URL where service is published) and API key\

best to use asynchronous calls to submit a form and then obtain results from the analysis
document_analysis_client = DocumentAnalysisClient(endpoint=endpoint, 
    credential=AzureKeyCredential(key))

task = document_analysis_client.begin_analyze_document_from_url(
    "prebuilt-document", docUrl)

result = task.result()

**Use the General Document, Read, and Layout models**
***using the read model***\

detect language that a line of text is written in and classify whether it's handwritten or printed (latin langugaes only)\
multi-age pdf or tiff files, use pages parameter in request to fix a page range for analysis\
ideal for extracting words and lines from documents wiht no fixed or predictable structure\

***using the general document model***\
extract key-value pairs, entities, selection marks, and tables from structured, semi-structured, and unstructured docs\
only model to support entity extraction\
entities that can be detected:\
person\
persontype: job title or role\
location: buildings, geographical features, geopolitical entities\
organization: companies, gov bodies, sports clubs, musical bands, other groups\
event: social gatherings, historical events, anniversaries\
products: object bought and sold\
skill: capability belonging to a person\
address: mailing address for physical location\
phone number: dialing codes and numbers for mobile phones and landlines\
email:\
URL:\
IP address:\
datetime:\
quantity:\

***using the layout model***\
extract text and returns selection marks and tables from input image or pdf file\
good model for rich information about structure of a document\
selection marks are extracted with bounding box, confidence indicator, whether they're selected or not\

each table cell is extracted with:
Its content text.\
The size and position of its bounding box.\
If it's part of a header column.\
Indexes to indicate its row and column position in the table.\

**use financial, id, and tax models**
***using the invoice model***\
fields for extraction include:
- customer name and ref id
- purchase order number
- invoice and due dates
- details about vendor, such as name, tax id, physical address
- similar details about customer
- billing and shipping addresses
- amounts such as total tax, invoice total, and amount due

invoice also includes line for purchased items and includes:
- description, product code
- amounts i.e. unit price, quantity of items, tax incurred, line total

***using the receipt model***\
amounts paid instead of charged\
reliably identifies fields including:
- merchant details such as name, phone number, and address
- amounts such as receipt total, tax, and tip
- date and time of transaction

***using id doc model***\
us drivers licnese and international passports (biographical pages of passports can be analyzed only) only trained docs\
fields extracted:
- first and last names
- personal info such as sex, dob, nationality
- country and region where doc was issued
- unique numbers such as doc number and machine readable zone
- endorsements, restrictions, and vehicle classifications

***business card model***\
fields:
- first and last names
- postal addresses
- email and website addresses
- various telephone numbers

***using w-2 model***\
fields extracted include:
- info about employer, name and address
- info about employee, name, address, social security number
- info about axes employee has paid

## create a composed form recognizer model
learning achievements:
- describe business problems to use custom models and composed models to solve
- train a custom model to obtain data from forms with unusual structures
- create a composed model that can analyze forms in multiple formats

**understand composed models**
enable user to submit form and don't know best model to use\

***what are composed models***\
2 types:
1. custom template: forms have consistent visual template. formatting and layout should be consistent across all completed examples of teh form
2. custom neural models: forms are less consistent, semi-structured or unstructured

***using composed models***\
GUi in studio enables creating a set of custom models or startcreatecomposedmodelasync() method in custom code\
docType field identifies custom model used for analysis\
number of custom models depends on type of custom forms and tier:

|Type of model|	Maximum number in Free (F0) tier	|Maximum number in Standard (S0) tier|
--|--|--|
|Custom Template|	500	|5000|
|Custom Neural|	100|	500|
|Composed|	5	|200|

max custom models to single composed model is 100

***custom model compatibility***\
restrictions:
1. custom template models are composable with other custom template models across 3.0 and 2.1 api versions\
2. custom neural models are composable with other custom neural models
3. custom neural models can't be composed with custom template models
[composed custom models docs](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/concept-composed-models)
[code sample - model compose](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/Sample_ModelCompose.md)

**assemble composed models**
***create a composed model in doc intell studio***\
before start need:
- azure ai doc intell resource in azure sub
- set of custom models, trained and labeled, you wan tot add to composed model
[compose custom model docs](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/concept-composed-models)

## Build an Azure AI Document Intelligence custom skill for Azure Cognitive Search
learning achievements:
- describe how a custom skill can enrich content passed through an azure cog search pipeline
- build a custom skill that calls an azure forms analyzer solution to obtain data from forms

doc intell models can enrich cog search index\
module covers creating custom skill that calls a doc intell model to help index documents\

**Understand Azure Cognitive Search enrichment pipelines**
index can be enriched with fields doc intell models are trained to extract\

***indexing content in cog search***\
during indexing process, cog search crawls content, processes it, and creates a list of words that will be added to the index, together with their location\
5 stages to indexing process:
1. document cracking: indexer opens content files and extracts content
2. field mappings: fields such as titles, names, dates, and more are extracted from content. field mappings to control how they're stored in index
3. skillset execution: optional skillset execution stage, custom ai processing done on the content to enrich teh final index
4. output filed mappings: using custom skillset, output is mapped to index fields in this stage
5. push to index: results of indexing process are stored in index within azure cog search

****what is a cog search skillset***\
each skill is a call to an AI process that enriches the index\
built-in skills use pre-trained ai models such as:
- key phrase extraction: detects important phrases in text based on the placement of terms, linguistic rules, and proximity to other terms
- language detection: detects predominantly used language in text
- merge: merges text from several fields in to a single field
- sentiment: detects sentiments i.e. positive, negative, and neutral
- translation: from original language into another to create multilingual index
- image analysis: detects contents of an image and generates description
- optical character recognition: recognizes printed and handwritten text in an image
[built-in skills list](https://learn.microsoft.com/en-us/azure/search/cognitive-search-predefined-skills)

call create skillset rest api to create and sent JSON definition code: looks like\
PUT https://[service name].search.windows.net/skillsets/[skillset name]?api-version=[api version]
  Content-Type: application/json  
  api-key: [admin key]

[service name] is the name of your Cognitive Search service in Azure.\
[skillset name] is a name for the skillset you're creating.\
[api version] is the version of the Search REST API.\
[admin key] is the API key for the Search service. You can obtain this key from the Azure portal.\

example json code that defines skillset:
{   
  "name" : "A name for the skillset",  
  "description" : "Optionally, add a descriptive text.",   
  "skills" : [

    ],
  "cognitiveServices": 
      {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "key": "<admin key>"
      },
  "knowledgeStore": { ... },
  "encryptionKey": { }
}

json code above def:
- cognitiveservices is required if using billable service apis. need api key
- knowledgeStore is where output from skills can be stored
- encryptionkey specifies key from key vault and encrypts sensitive content in pipieline

skill section (in above json) defines one or more built-in or custom kill that will analyze content
"skills":[
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "name": "Entity recognition",
    "context": "/document",
    "categories": [ "Organization" ],
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "organizations",
        "targetName": "orgs"
      }
  },
  {
      "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
      "name": "Image analysis",
      "context": "/document/normalized_images/*",
      "visualFeatures": [
          "brands"
      ],
      "inputs": [
          {
              "name": "image",
              "source": "/document/normalized_images/*"
          }
      ],
      "outputs": [
          {
              "name": "brands"
          }
      ]
  }
]

***what is a custom skill***\
use for 2 reasons:
- built-in skills doesn't include type of ai enrichment needed
- want to train your own model

2 types of custom skills:
1. AML: enrich by calling AML model
2. custom web api skills: enrich index by calling a web service. i.e. applied ai services, such as AI doc intell

web service requires certain requirements be met i.e.:
- service must accept a json payload as an input and return a second json payload as its results
- output JSON should have a top-level entity named value that contains an array of objects
- number of objects sent to service should match number of objects in teh values entity
- each object in values should include a unique recordid property, a data property with the returned info, a warnings property, and an errors property

***integrate cog search and azure ai doc intell***\
integrate using these steps:
1. create ai doc intell resource in azure subscription
2. configure one or more models in doc intell either with prebuilt models or train your own model for unusual or unique form types
3. develop and deploy a web service that can call your azure ai document intell resource
4. add custom web api skill, with config to cog search skillset. skill should be configed to send requests to web service

**Build an Azure AI Document Intelligence custom skill**
write web service that integrates custom skill interface (must have)\

***custom skill interface and requirements***\
ensuring compatibility with other skills and cog search indexing should be first consideration\
code must hanlde the following input values in JSON body of REST request:\
values: The JSON body will include a collection named values. Each item in this collection represents a form to analyze.\
    recordId: Each item in the values collection has a recordId. You must include this ID in the output JSON so that Cognitive Search can match input forms with their results.\
    data. Each item in the values includes a data collection with two values:\
      formUrl. This is the location of the form to analyze.\
      formSasToken. If the form is stored in Azure Storage, this token enables your code to authenticate with that account.\

Response includes a JSON body and cog search expects response format to include:\
values. A collection in which each item is one of the submitted forms.\
    recordId. Cognitive Search uses this value to match results to one of the input forms.\
    data. Use the data collection to return the fields that Azure AI Document Intelligence has extracted from each input form.\
    errors. If you couldn't obtain the analysis for a form, use the errors collection to indicate why.\
    warnings, If you have obtained results but some non-critical problem has arisen, use the warnings collection to report the issue.\

***testing the custom skill***\
test by sending rest requests and observing responses e.g. postman\
there is a Code + test tool in azure to formulate and submit test REST requests\
in visual studio, deploy function locally with F5, then submit requests to function by sending URL: POST https://localhost:7071/api/analyze-form\
requests specifies a form to analyze by its url included in json body:
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>" # if in storage account authenticate with token with Get shared access signature in azure storage explorer
            }
        }
    ]
}

response to above json request could look like:
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024"
            },
            "errors": null,
            "warnings": null
        }
    ]
}

***hosting a custom skill***\
host within azure can deploy with:
- azure function
- container in azure container instance service
- container in azure kubernetes services

***add custom skill to skillset***\
custom skill completed, tested, and hosted config cog search to call it\
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "description": "A custom skill that calls Azure AI Document Intelligence",
  "uri": "https://contoso.com/formrecognizer",
  "batchSize": 1,
  "context": "/document",
  "inputs": [
    {
      "name": "formUrl",
      "source": "/document/metadata_storage_path"
    }
  ],
  "outputs":[ 
    { 
      "name":"address",
      "targetName":"address"
    },
    { 
      "name":"recipient",
      "targetName":"recipient"
    }
  ]
}

Microsoft.Skills.Custom.WebApiSkill is required to define this as a Web API skill.\
uri is the location of the web service. In this module, the web service is implemented as an Azure Function. The Function is the interface between the search pipeline and Azure AI Document Intelligence.\
inputs determines the data that is sent to the skill for analysis.\
outputs determines the data that is returned from the skill.\
[custom skill to pipeline doc](https://learn.microsoft.com/en-us/azure/search/cognitive-search-custom-skill-interface)

