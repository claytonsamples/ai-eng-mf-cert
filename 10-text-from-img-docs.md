# Extract text from images and documents
Learning achievements:
how ai vision enables you to read text from images with read api
use vision service with sdks and rest api
develop app can read printed and handwritten text

## Read text in images and docs with ai vision service
***explore vision options for reading text***
2 apis:
1. read api: read small to large volumes of text from images and PDF; new model > accuracy than OCR API; can read printed text in multiple languages and handwritten text in eng; initial fnc call returns asynch operation ID, must be used in subsequent call to retrieve resutls
2. image analysis: still in preview; api to read small amnt of text from img; returns contextual info (line and position); results are synchronous from single fnc; analyzing images past extracting text, incl detecting content; describing or categorizing an image, generating thumbnails

***read api***
pass img url or binary data using READ rest func
pass operation id in subsequent Get Read Results call
results are broken down by page, line, word; text values are in both line and word levels+


## extract data from forms with doc intelligence
Learning achievements:
- how azure document intelligence's layout service, prebuilt models, and customer service can automate processes
- document inteligence's ocr capabilities with sdk, rest, and document intelligence studio
- develop and test custom models

Consider some of the instances when a person needs to process form data:
-When filing claims
-When enrolling new patients in an online management system
-When entering data from receipts to an expense report
-When reviewing an operations report for anomalies
-When selecting data from a report to give to a stakeholder

uses:
- process automation
- knowledge mining
- industry-specific apps

**What is Azure Document Intelligence?**
interface that uses x number of forms to provide a pre-built model providing high-accuracy data extraction with little to no model training
 ***Azure Document Intelligence service components***
 services:
 1. document analysis models: takes input and reutrns JSON with location of text in bb, content, tables, selectio n marks (checkboxes or radio buttons), doc structure
 2. prebuilt models: detect and extract info from doc images and return extracted data in a JSON: forms support w-2, invoices, receipts, id docs, busienss cards
 3. custom models: custom models extract data from forms specific to business [intelligence studio training docs](https://formrecognizer.appliedai.azure.com/studio)

***Access services with the client library SDKs or REST API***
[sdk and rest docs](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/quickstarts/get-started-sdks-rest-api)

**Get started with Azure Document Intelligence**
needs: subscription and selection of form files

***Understand Azure Document Intelligence file input requirements***
rq:
1. format must be jpg, png, bmp, pdf (text or scanned), tiff
2. files size must be < 500 MB for paid and 4 MB for free
3. img dimensions must be between 50x50 and 10000 x 10000 pixels
4. total size of training data must be 500 pages or less

***Decide what component of Azure Document Intelligence to use***
- use ocr capabilities to capture document analysis, use the layout model, read model, or general document model
- create app that extracts data from forms (prebuilt if covered in forms used to train pre-built)
- industry-specific forms, create custom model

**Train custom models**
supervised ML
 custom model training approach
 1. store sample forms in azure blob container, along with json containing layout and label field info; ocr.json for each sample can be done using doc intelligence analyze document function; additionally need a single fields.json file describing the fields you want to extract, and a labels.json filre for each sample form mapping the fields to their location in that format
 2. generate shared access security url for container
 3. build model rest api
 4. use get model rest api OR
 5. use doc intelligence studio to label and train with two types of underlying models for customer forms
  - custom tempalte models: accurately extract labeled key-value pairs, selection marks, tables, regions, and sig from docs. training takes few minutes more than 100 languages supported
  - custom neural models are dl models that combine layout and language features to accurately extract labeled fields from docs. best for semi-structured or unstructured docs

**Use Azure Document Intelligence models**
***APi***
use analyze doc func to extract data from custom model; supply model ID

###python code START###
endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_KEY"

model_id = "YOUR_CUSTOM_BUILT_MODEL_ID"
formUrl = "YOUR_DOCUMENT"

document_analysis_client = DocumentAnalysisClient(
    endpoint=endpoint, credential=AzureKeyCredential(key)
)

# Make sure your document's type is included in the list of document types the custom model can analyze
task = document_analysis_client.begin_analyze_document_from_url(model_id, formUrl)
result = task.result()
###END###

***Understanding confidence scores***
scores low, try improve quality of input docs

**Document Intelligence Studio**
used to analyze form layouts, extract data from prebuilt, train custom
following projects supported:
1. document analysis models
   - read: extract printed and handwritten text lines, words, locations, and detect language from docs and images
   - layout: extract text, tables, selection marks, and structure info from docs and images
   - general: key-value pairs, selection marks, entities from docs
2. prebuilt
3. customer

***build document analysis or prebuilt model model projects***
- create doc intelligence or ai service resource
- select either 'read','layout','general docs', or prebuilt models'
- analyze doc will need endpoint and key 

***build custom model project***
train process
1. create resource
2. collect 5-6 sample forms for training and upload to storage container
3. configure cross-domain resource sharing. enables storage of labeled files in container
4. create custom model project
5. apply labels to text
6. train model. get model ID and average accuracy for ttags
7. test model analyzing new form
[cognitive search integration](https://learn.microsoft.com/en-us/previous-versions/azure/search/cognitive-search-tutorial-aml-designer-custom-skill)
