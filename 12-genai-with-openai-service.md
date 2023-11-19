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

***completions playground***\
text-in text out interface must select deployments\
playground parameters:
1. temperature: controls randomness of response. lower produces more repetitive & deterministic responses. Increase more unexpected or creative responses
2. max length(tokens): 1 token roughly 4 characters. sets limit per model response. 4k for prompt and query
3. stop sequences: responses stop at desired point, e.g. end of sentence or list. specify up to 4 sequences where model will stop generating further tokens in a response. return text won't contain stop sequence
4. top probabilities: controls randomness but uses diff method to temp. narrows token selection to likelier tokens. increase top p lets model choose from both high and low likelihood. adjust 1 not both
5. frequency penalty: reduce repeating token proportionally based on how often it has appeared in text. decreases likelihood of repeating exact same text
6. presence penalty: reduce chance of repeating any token that has appeared in text. increases likelihood of intro new topics in a response
7. pre-response text: insert text after user's input and before the model's response. help prepare model for response
8. post-response text: insert text after model's generated response to encourage furter user input

***chat playground parameters***\
1. temperature: equal to playground parameter definition
2. max response: limit on number of tokens per model response. equivalent constraints to max length
3. top p: same as playground definition
4. past messages included: # of past messages to include in each new api request. helps give model context for new user queries. setting of 10 will include 5 user queries and 5 system responses

## Build nlp solutions with openai service
learning achievements:
- integrate azure open ai into apps
- differentiate between different endpoints available to your app
- generate completions to prompts using rest api and language specific sdk

**integrate azure openai into app**\
3 main model families:
1. text or gpt: understand and generate natural language and some code. models are best at general tasks, conversations, and chat formats
2. code: built on top of gpt models, and trained on millions of lines of code. understand and generate code, including interpreting comments or natural lanugage to generate code
3. embeddings: understand and use embeddings, which are a special format of data that can be used by machine learning models and algos
[aoai docs](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models)

***authentication and specs of deployed model***\
config app, you need to specify resource endpoint, key, and deployment name\

***prompt engineering***\
e.g.:
Classify the following news headline into 1 of the following categories: Business, Tech, Politics, Sport, Entertainment

Headline 1: Donna Steffensen Is Cooking Up a New Kind of Perfection. The Internet’s most beloved cooking guru has a buzzy new book and a fresh new perspective
Category: Entertainment

Headline 2: Major Retailer Announces Plans to Close Over 100 Stores
Category:
[prompt docs](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/prompt-engineering)

***available endpoints***\
endpoints avaialble for interacting with deployed model are used differently, and certain endpoints can only use certain models\
available endpoints:
1. completion: takes input and generates one or more predicted completions
2. chatCompletion: takes input in the form of a chat conversation(roles specified with message they send), and next chat completion is generated
3. embeddings: takes input and returns a vector representation of that input

**Use Azure OpenAI REST API**
|Placeholder name	|Value|
--|--|
|YOUR_ENDPOINT_NAME|	This base endpoint is found in the Keys & Endpoint section in the Azure portal. It's the base endpoint of your resource, such as https://sample.openai.azure.com/.|
|YOUR_API_KEY|	Keys are found in the Keys & Endpoint section in the Azure portal. You can use either key for your resource.|
|YOUR_DEPLOYMENT_NAME|	This deployment name is the name provided when you deployed your model in the Azure OpenAI Studio.|

***completions***\
once deployed model send prompt to service using POST request. one endpoint is completions, which generater completion of prompt\
example:
curl https://YOUR_ENDPOINT_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/completions?api-version=2022-12-01\
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d "{
  \"prompt\": \"Your favorite Shakespeare is\",
  \"max_tokens\": 5
}"

response from api looks like:
{
    "id": "<id>",
    "object": "text_completion",
    "created": 1679001781,
    "model": "text-davinci-003",
    "choices": [
        {
            "text": "Macbeth",
            "index": 0,
            "logprobs": null,
            "finish_reason": "stop"
        }
    ]
}
completion response to look for is within choices[].text. finish_reason provides insight into why the response ended (filter, max_tokens, stop, harmful content,etc)

***chat completions***\
similar to completions, but works best when prompt is chat exchange\
e.g.:
curl https://YOUR_ENDPOINT_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/chat/completions?api-version=2023-03-15-preview \
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d '{"messages":[{"role": "system", "content": "You are a helpful assistant, teaching people about AI."},
{"role": "user", "content": "Does Azure OpenAI support multiple languages?"},
{"role": "assistant", "content": "Yes, Azure OpenAI supports several languages, and can translate between them."},
{"role": "user", "content": "Do other Azure AI Services support translation too?"}]}'

response example:
{
    "id": "chatcmpl-6v7mkQj980V1yBec6ETrKPRqFjNw9",
    "object": "chat.completion",
    "created": 1679001781,
    "model": "gpt-35-turbo",
    "usage": {
        "prompt_tokens": 95,
        "completion_tokens": 84,
        "total_tokens": 179
    },
    "choices": [
        {
            "message":
                {
                    "role": "assistant",
                    "content": "Yes, other Azure AI Services also support translation. Azure AI Services offer translation between multiple languages for text, documents, or custom translation through Azure AI Services Translator."
                },
            "finish_reason": "stop",
            "index": 0
        }
    ]
}

***embeddings***\
POST request to embeddings endpoint:
curl https://YOUR_ENDPOINT_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/embeddings?api-version=2022-12-01 \
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d "{\"input\": \"The food was delicious and the waiter...\"}"

model meant for embeddings starts with text-embedding or text-similarity\
response from api example:
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "embedding": [
        0.0172990688066482523,
        -0.0291879814639389515,
        ....
        0.0134544348834753042,
      ],
      "index": 0
    }
  ],
  "model": "text-embedding-ada:002"
}

**Use Azure OpenAI SDK**
***install libraries***\
pip install openai
***configure app to access aoai resource***\
necessary params:
endpoint, key, engine (name of deployment) example:
# Add OpenAI library
import openai

openai.api_key = '<YOUR_API_KEY>'
openai.api_base =  '<YOUR_ENDPOINT_NAME>' 
openai.api_type = 'azure' # Necessary for using the OpenAI library with Azure OpenAI
openai.api_version = '20xx-xx-xx' # Latest / target version of the API

deployment_name = '<YOUR_DEPLOYMENT_NAME>' # SDK calls this "engine", but naming
                                           # it "deployment_name" for clarity

***call azure open ai resource***\
send to endpoint ex:
prompt = 'What is Azure OpenAI?'
response = openai.Completion.create(engine=deployment_name, prompt=prompt)

print(response.choices[0].text)

for chat completion:
response = openai.ChatCompletion.create(
    engine=deployment_name,
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What is Azure OpenAI?"}
    ]
)

print(response['choices'][0]['message']['content'])



