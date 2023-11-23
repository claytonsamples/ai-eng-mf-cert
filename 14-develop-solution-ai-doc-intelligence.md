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

