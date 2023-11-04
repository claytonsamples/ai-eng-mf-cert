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
object detection: identify location of specific classes of objects in image
learning achievements:
- describe object detection
- train object detector
- options for labeling images

**understand object detection**
components:
1. class label of each object detected in image
2. location of each object within the image, indicated as coordinates of bounding box

azure custom vision for object detection
You can use the Azure AI Custom Vision service to train an object detection model. To use the Azure AI Custom Vision service, you must provision two kinds of Azure resource:

A training resource (used to train your models). This can be:
An Azure AI Services resource.
An Azure AI Custom Vision (Training) resource.
A prediction resource, used by client applications to get predictions from your model. This can be:
An Azure AI Services resource.
An Azure AI Custom Vision (Prediction) resource.

**train an object detector**
different between image class and object detection model training is labeling of the images with tags
object detection requires each label consists of tag and region defining the bounding box

**Consider options for labeling images**
- azure ai custom vision portal or,
- [azure machine learning studio](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-label-data),
- [visual object tagging tool](https://github.com/microsoft/VoTT/blob/master/README.md)

***bounding box measurement units**
4 values define bounding boxes:
- left (X) and top (Y) coordinates of the top-left corner of bounding box and width and height. values are expressed as proportional values relative to the source image size
Left: 0.1
Top: 0.5
Width: 0.5
Height: 0.25
This defines a box in which the left is located 0.1 (one tenth) from the left edge of the image, and the top is 0.5 (half the image height) from the top. The box is half the width and a quarter of the height of the overall image.

## Detect, analyze, and recognize faces
learning achievements:
- identify options for face detection, analysis, and identification
- understand considerations for face analysis
- detect faces with azure ai vision
- understand the capabilities of the face service
- implement facial recognition

**Identify options for face detection analysis and identification**
two services for face detection
1. vision service: detect human faces in an image and bounding box
2. face service: more comprehensive analysis capabilities: face detection w bb, facial feature analysis (head pose, spectables, blur, facial landmarks, oclusion, others), face comparison and verification, facial recognition

**Understand considerations for face analysis**
- data privacy and security: facial is personally identifiable. adequate protections for facial data used for model training and inferencing
- transparency: ensure users are informed about facial data and who will have access to it
- fairness and inclusiveness: ensure face-based system cannot be used in a mnaner that is preudicial to individuals based on their appearance, or to unfairly target individuals

**Detect faces with vision service**
call image REST function specifying faces [vision endpoint](https://learn.microsoft.com/en-us/rest/api/computer-vision/)

**Understand capabilities of the face service**
- face detection: results include an ID that identifies the face and bb coordinates
- face attribute analysis: head pose(pitch, roll, and yaw orientation in 3D), glasses, blur(low,m,h), exposure(under,good,over),noise, occlusion(objects obscuring the face)
- facial landmark location: coordiantes for key landmarks(eye corners, pupils, tip of nose,...)
- face comparison: compare multiple images for similarity and verification
- facial recognition: train a model belonging to specific individuals, and use the model to identify people in new images

**Compare and match detected faces**
retains ID (GUID) for 24 hours
ID cached, subsequent images can be used to compare new faces to cached identity and determine if they are similar or to verify

**Implement facial recognition**
train:
1. create a person group defining set of individuals to identify (ex. employees)
2. add a person to the group for each individual to identify
3. add detected faces from multiple images to each person, vary environment and poses. IDs faces will no longer expire after 24 hours (persisted faces)
4. train model
model is stored in face resource - client applications:
- identify individuals
- verify identity
- analyze new images to find faces that are similar to persisted face
