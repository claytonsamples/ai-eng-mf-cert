# Create computer vision solutions with Azure AI Vision
## Analyze images
**provision AI vision resource**
purpose: extract info from the functionality provided for:
- description & tag generation: determine caption for image, identify relevant "tags" used for keywords to indicate subject
- object detection - detecting the presence and location of specific objects in image
- face detection: presence, location, and features of human faces in image
- image metadata, color, and type analysis: determine format and size of image, dominate color palette, and if contains clip art
- Category identification: appropriate cat for image, and if it contains any known landmarks
- brand detection: detect the presence of any known brands or logos
- moderation rating: determine if the image includes any adult or violent content
- optical character recognition: read text in the image
- smart thumbnail generation: main region of interest in the image to create a smaller "thumbnail" version

single and multi-service resource [vision service docs](https://learn.microsoft.com/en-us/training/modules/read-text-images-documents-with-computer-vision-service/)

- analyze image use analyze image REST method or equivalent SDK
[limited access policy](https://aka.ms/cog-services-limited-access)
[addition of policy in Responsible AI standard](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/)

**generate smart-cropped thumbnail**
used to provide smaller versions of images in apps and websites

## Analyze video
Learning achievements:
- describe azure video indexer capabilities
- extract custom insights
- azure video indexer widgets and api's

**describe azure video indexer capabilities**
- facial recognition: detecting the presence of individual people in the video.
- Optical character recognition: reading text in the video
- speech transcription: creating a text transcript of spoken dialog in the video
- topics: identification of key topics discussed in the video
- sentiment: how positive or negative segments within video are
- labels: label tags identify key objects or themes
- content moderation: detection of adult or violent themes
- scene segmentation: breakdown of video into its constituent scenes
You can use a free, standalone version of the Video Analyzer service (with some limitations), or you can connect it to an Azure Media Services resource in your Azure subscription for full functionality.

**extract custom insights**
predefined models can recognize well-known celebrities, and brands, and transcribe spoken phrases into text
custom model creation for:
1. people: add images of faces you want to recognize in videos, and train a model
2. language: specific terminology that may not be in common usage, train custom model to detect and transcribe
3. brands: train to recognize specific names as brands, for example to identify products, projects, or companies that are relevant to business
4. animated characters

**Use Video Analyzer widgets and APIs**
insert into custom apps:
1. azure indexer widgets: embed in custom HTML interfaces
2. indexer API: subscription key, then use to consume and uatomate video indexing tasks, such as uploading and indexing videos, retrieving insights, and determining endpoints for widgets
## Classify images
Learning achievements:
- AI custom vision, provision
- describe image classification
- train image classifier

**Provision Azure resources for Azure AI Custom Vision**
build own cv for image or object detection
to provision service you must have two kinds of resources:
1. training resource: either azure ai services resource or azure ai custom vision (training) resource
2. prediction resource: used by client to get predictions from model.  either azure ai services resource or azure ai custom vision (prediction) resource

**understand image classification**
key definitions:
multiclass classification: multiple classes in image, but full image can belong to only 1 class
multilabel classification: image might be associated with multiple labels

**Train an image classifier**
options:
- use azure ai custom vision portal
- use custom vision REST API or SDK
- or combination of the two

The portal provides a graphical interface that you can use to:

Create an image classification project for your model and associate it with a training resource.
Upload images, assigning class label tags to them.
Review and edit tagged images.
Train and evaluate a classification model.
Test a trained model.
Publish a trained model to a prediction resource.

## Detect objects in images


## Detect, analyze, and recognize faces
