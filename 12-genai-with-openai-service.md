# Develop gen ai solutions with openai service 
Azure OpenAI Service provides access to OpenAI's powerful large language models such as ChatGPT, GPT, Codex, and Embeddings models. These models enable various natural language processing (NLP) solutions to understand, converse, and generate content. Users can access the service through REST APIs, SDKs, and Azure OpenAI Studio.

## Get started with Azure openai service 
Azure OpenAI Service is a fully managed service that allows developers to easily\
integrate OpenAI models into their applications.\
With Azure OpenAI Service, developers can quickly and easily access a wide range of AI models, including natural language processing, computer vision, and more


**Access Azure OpenAI Service**\
***create service resource in azure portal***\
needs: subscription name, resource group name, region, unique instance, pricing tier\

cli implementation:\
az cognitiveservices account create\
-n MyOpenAIResource (unique name)\
-g OAIResourceGroup (resource group name)\
-l eastus (region)\
--kind OpenAI\
--sku s0\
--subscription subscriptionID
note: you can find regions available for service thorugh CLI command az account list-locations\
[cli docs](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/create-resource?pivots=cli#sign-in-to-the-cli?azure-portal=true)

**Azure OpenAi Studio**
- deploy model

**Explore types of generative AI models**\
pricing is model type and token dependent

**deploy gen ai models**\
deploy first to make api calls

***deploy using openai studio***\
straight forward gui enabled

***deploy using azure cli***\
az cognitiveservices account deployment create\
   -g myResourceGroupName\
   -n myResourceName\
   --deployment-name MyModel\
   --model-name gpt-35-turbo\
   --model-version "0301" \
   --model-format OpenAI\
   --scale-settings-scale-type "Standard"\
[deploy with rest](https://learn.microsoft.com/en-us/azure/ai-services/openai/)

**Use prompts to get completions from models**\
prompt is the text portion of a request that is sent to deployed model's completions endpoint. responses are completions.

types of prompts
|task|prompt ex|completion ex|
--|--|--|
|classify content|tweet: i enjoyed the trip. Sentiment:|positive|
|Generating new content|	List ways of traveling|	1. Bike 2. Car ...|
|Holding a conversation|	A friendly AI assistant|	See examples|
|Transformation (translation and symbol conversion)	English: Hello French:|	bonjour|
|Summarizing content	|Provide a summary of the content {text}	|The content shares methods of machine learning.|
|Picking up where you left off|	One way to grow tomatoes	|is to plant seeds.|
|Giving factual responses|	How many moons does Earth have?|	One|

***completion quality***\
facts that affect quality of completions
- prompt engineered structure [docs](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/prompt-engineering?portal=true)
- model parameters
- data model is trained on [model fine-tuning with customization](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/fine-tuning?pivots=programming-language-studio?portal=true)

**test models in openai studio's playground**\
interface to test deployed models

***completions playground***
text-in text out interface must select deployments\
playground parameters: \
1. temperature: controls randomness of response. lower produces more repetitive & deterministic responses. Increase more unexpected or creative responses
2. max length(tokens): 1 token roughly 4 characters. sets limit per model response. 4k for prompt and query
3. stop sequences: responses stop at desired point, e.g. end of sentence or list. specify up to 4 sequences where model will stop generating further tokens in a response. return text won't contain stop sequence
4. top probabilities: controls randomness but uses diff method to temp. narrows token selection to likelier tokens. increase top p lets model choose from both high and low likelihood. adjust 1 not both
5. frequency penalty: reduce repeating token proportionally based on how often it has appeared in text. decreases likelihood of repeating exact same text
6. presence penalty: reduce chance of repeating any token that has appeared in text. increases likelihood of intro new topics in a response
7. pre-response text: insert text after user's input and before the model's response. help prepare model for response
8. post-response text: insert text after model's generated response to encourage furter user input

***chat playground parameters***
1. temperature: equal to playground parameter definition
2. max response: limit on number of tokens per model response. equivalent constraints to max length
3. top p: same as playground definition
4. past messages included: # of past messages to include in each new api request. helps give model context for new user queries. setting of 10 will include 5 user queries and 5 system responses

