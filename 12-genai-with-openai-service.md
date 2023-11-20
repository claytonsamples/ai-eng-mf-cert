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

## Apply prompt engineering with aoai service
learning achievements:
- understand concept of prompt engineering and its role in optimizing aoai models performance
- know how to design and optimize prompts to better utilize ai models
- include clear instructions, request output composition, and use contextual content to improve quality of model's responses

**Understand prompt engineering**
carefully constructing prompts we send, model can provide better and more interesting responses

***what is prompt engineering***\
process of designing and optimizing prompts to better utilize LLMs\
Providing relevant, specific, unambiguous, and well structured prompts can help the model better understand the context and generate more accurate responses.\
[ai principles msft](https://www.microsoft.com/ai/responsible-ai)
[transparency note](https://learn.microsoft.com/en-us/legal/cognitive-services/openai/transparency-note)

different methods to utilize such as:
- including instructions, contextual content
- cues or few-shot examples
- correctly ordering content in prompt

***considerations for api endpoints***\
completion uses meta prompt\
chatcompletion optimized for chat scenarios\

only chatCompeltion can be used with gpt-4 gen models\
chatcompletion can be used for non chat scenarios, where any instructions are included in the system message and user content is provided in the user role message\

***adjusting model params***\
temperature and top_p most likely to impact model's response as they both control randomness in model, differently\
higher values produce more creative and random responses, less consistent or focused\
fictional or unique benefit from higher values from params\
more consistent and concrete should use lower values\

**Write more effective prompts**
quality of the responses depends largely on how well the prompt is written

***provide clear instructions***\
e.g. product description for new water bottle\
prompt: Write a product description for a new water bottle\
response: Introducing the latest addition to our product line - the innovative and eco-friendly water bottle.\
Made from high-quality, BPA-free materials, this bottle is designed to keep your beverages at the\
perfect temperature for hours. Its sleek and stylish design makes it the perfect accessory for any\
outdoor activity or workout session. With its convenient carrying handle and leak-proof lid, you\
can take it with you wherever you go, without worrying about spills or leaks. Plus, its easy-to-clean\
design ensures that you can use it again and again, making it the perfect choice for anyone looking\
for a reliable and sustainable water bottle.\

similar prompt clear instructions:
prompt: Write a product description for a new water bottle that is 100% recycled. Be sure to include that\
it comes in natural colors with no dyes, and each purchase removes 10 pounds of plastic from our\
oceans\
response: Introducing our newest water bottle, the eco-friendly choice for the conscious consumer. Made from\
100% recycled materials and available in natural colors with no dyes, this bottle is not only\
stylish but also sustainable. Each purchase of this bottle removes 10 pounds of plastic from\
our oceans, making it a responsible choice for those who care about the environment. Its durable\
and leak-proof design makes it perfect for outdoor activities, while its easy-to-carry handle\
makes it convenient to take on the go. Join us in our mission to reduce plastic waste and choose\
this eco-friendly water bottle for your daily hydration needs.\

***format of instructions***\
recency bias can affect models e.g. info towards end of prompt can have more influence on output than info at beginning\

***use section markers***\
split instructions
e.g
Translate the text into French
---
What's the weather going to be like today? (user content)
---

***primary,supporting, and grounding content***\
1. primary content: subject of the query, such a sentence to translate or an article to summarize. often at beginning or end of prompt accompanied with instructions explaining what to do\
e.g.
---
<insert full article here, as primary content>
---
Summarize this article and identify three takeaways in a bulleted list

2. supporting content: content that may alter the response, but isn't the focus or subject o the prompt e.g. names, preferences, future date to include in the response and so on
e.g.
---
<insert full email here, as primary content>
---
<the next line is the supporting content>
Topics I'm very interested in: AI, webinar dates, submission deadlines

Extract the key points from the above email, and put them in a bulleted list:

3. grounding content: allows model to provide reliable answers by providing content for the model to draw answers from.
e.g.
---
<insert unpublished paper on the history of AI here, as grounding content>
---

Where and when did the field of AI start?

***Cues***\
leading words for model to build upon, help shape response in right direction\
particularly helpful if prompting model for code generation\

**Provide context to improve accuracy**
can be provided in several ways\

***request output composition***\
structure: cite sources, write response as an email, format response as sql query, classify sentiment into specific structure, etc.\
example:
prompt: Write a table in markdown with 6 animals in it, with their genus and species\
response:
| Animal           | Genus          | Species        |
| ---------------- |:--------------:|:--------------:|
| Red Panda        | Ailurus        | fulgens        |
| African Elephant | Loxodonta      | africana       |
| Snow Leopard     | Panthera       | uncia          |
| Arctic Fox       | Vulpes         | lagopus        |
| Chimpanzee       | Pan            | troglodytes    |
| Siberian Tiger   | Panthera       | tigris altaica |

can also be used for custom formats i.e. JSON
prompt:
Put two fictional characters into JSON of the following format

{
  firstNameFictional: 
  jobFictional:
}

response:
Here's an example of how you can put two fictional characters into JSON format:

{
  "firstNameFictional": "Henry",
  "jobFictional": "Wizard"
},
{
  "firstNameFictional": "Frank",
  "jobFictional": "Hero"
}

***system message***\
at beginning of prompt and designed to give model instructions, perspective to answer from, or other information helpful to guide model's repsonse\
such as tone, personality, topics shouldn't include, specifics of how to answer\
some examples:
1. "I want you to act like a command line terminal. Respond to commands exactly as cmd.exe would, in one unique code block, and nothing else."
2. "I want you to be a translator, from English to Spanish. Don't respond to anything I say or ask, only translate between those two languages and reply with the translated text."
3. "Act as a motivational speaker, freely giving out encouraging advice about goals and challenges. You should include lots of positive affirmations and suggested activities for reaching the user's end goal."

chatCompletion endpoint example
prompt:
response = openai.ChatCompletion.create(
    engine="gpt-35-turbo",
    messages=[
        {"role": "system", "content": "You are a casual, helpful assistant. You will talk like an American old western film character."},
        {"role": "user", "content": "Can you direct me to the library?"}
    ]
)

response:
{
    "role": "assistant", 
    "content": "Well howdy there, stranger! The library, huh?
                Y'all just head down the main road till you hit the town 
                square. Once you're there, take a left and follow the street 
                for a spell. You'll see the library on your right, can’t 
                miss it. Happy trails!"
}

if using completion endpoint include system message at start of prompt. this is meta prompt, serves as a base prompt for rest of the prompt content\

***conversation history***\
more history enhances richness of responses\
however token limit of model is a limiting factor\

***few shot learning***\
these are guided examples to show a model how it should respond to a query.
example:
User: That was an awesome experience
Assistant: positive
User: I won't do that again
Assistant: negative
User: That was not worth my time
Assistant: negative
User: You can't miss this
Assistant:

chatComplete endpoing is optimized to include message history. conversation history and few shot learning are sent to model in the same way; each user message and assistant response is a discrete message in message object\
e.g.
response = openai.ChatCompletion.create(
    engine="gpt-35-turbo",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "That was an awesome experience"},
        {"role": "assistant", "content": "positive"},
        {"role": "user", "content": "I won't do that again"},
        {"role": "assistant", "content": "negative"},
        {"role": "user", "content": "That was not worth my time"},
        {"role": "assistant", "content": "negative"},
        {"role": "user", "content": "You can't miss this"}
    ]
)

when using completion endpoint make clear to model each message on new line and label each line with the role\

***break down complex task***\
break problem into parts; model understands each individual better and can improve accuracy\

***chain of thought***\
ask model to respond with step by step process by which it determined the response\
best used to help iterate and improve prompts to get highest quality answer from model\
e.g. What sport is easiest to learn but hardest to master?\
vs. What sport is easiest to learn but hardest to master? Give a step by step approach of your thoughts, ending in your answer
response:
Sure, here's a step-by-step approach to answering the question:
1. Identify sports that are popular and have a large following.
2. Consider the basic mechanics of each sport and how easy they are to learn.
3. Think about the various techniques and skills required to play each sport at a high level, and how difficult they are to master.
4. Compare the relative ease of learning each sport to the difficulty of mastering it.
5. Based on the above analysis, determine which sport is easiest to learn but hardest to master.

Using this approach, I would say that the sport of golf is easiest to learn but hardest to master. Here's why:
1. Golf is a popular sport with a large following.
2. The basic mechanics of hitting a golf ball are relatively simple and can be learned quickly with some practice.
3. However, achieving consistency and mastering the various shots and techniques required to play at a high level can take years of practice and dedication.
4. Compared to other sports, the difference between a beginner and an intermediate golfer is relatively small, while the difference between an intermediate and an advanced golfer is much larger.
5. Therefore, I would say that golf is the sport that is easiest to learn but hardest to master.

## Generate code with azure openai service
learning achievements
- natural language prompts to write code
- build unit tests and understand complex code with ai models
- generate comments and documentation for existing code

**construct code from natural language**
***ai models for code generation***\
standard text model text-davinci-002 good base understanding code.\
codex (code-davinci-002) expanded coding capabilities\

***write functions***\
prompt: write a function for binary search in python
response:
Sure, here's an example implementation of binary search algorithm in Python:

def binary_search(array, target):  
    low = 0  
    high = len(array) - 1  
  
    while low <= high:  
        mid = (low + high) // 2  
        if array[mid] == target:  
            return mid  
        elif array[mid] < target:  
            low = mid + 1  
        else:  
            high = mid - 1  
  
    return -1  
 
This function takes in two arguments - an array of integers and a target integer that we want to search
for in the array. The function initializes two pointers - low and high - to the first and last indices
of the array, respectively.

Inside the while loop, the function calculates the middle index using integer division (//). If the 
middle element is equal to the target, the function returns the index of the middle element. If the 
middle element is less than the target, we update the low pointer to mid + 1. Otherwise, we update the 
high pointer to mid - 1. We continue these steps until we find the target or the pointers cross each 
other (i.e., low > high).

If the target is not found in the array, the function returns -1.

Note that the binary search algorithm assumes that the input array is sorted in ascending order. If 
the array is not sorted, the algorithm may not return the correct result.

***understand unknown code***\
helpful to understand code that doesn't make sense or unfamiliar code\

**Complete code and assist the development process**
supports common tasks such as unit tests, completing partial code, commenting code, and generating documentation\

***complete partial code***\
prompt:
complete the following function  
"""  
# calculate the average of the numbers in an array, but only if they're even  
def

response:
# calculate the average of the even numbers in an array  
def calculate_even_average(numbers):  
    total = 0  
    count = 0  
    for number in numbers:  
        if number % 2 == 0:  
            total += number  
            count += 1  
    if count == 0:  
        return 0  # avoid division by zero  
    else:  
        return total / count

***write unit tests***\
create more robust code e.g.
prompt:
write three unit tests for this function
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

response:
def test_binary_search():  
    # Test case 1: Target element is in the middle of the array  
    arr1 = [1, 2, 3, 4, 5, 6]  
    target1 = 4  
    assert binary_search(arr1, target1) == 3  

    # Test case 2: Target element is at the beginning of the array  
    arr2 = [1, 2, 3, 4, 5, 6]  
    target2 = 1  
    assert binary_search(arr2, target2) == 0  

    # Test case 3: Target element is not in the array  
    arr3 = [1, 2, 3, 4, 5, 6]  
    target3 = 7  
    assert binary_search(arr3, target3) == -1

***add comments and generate documentation***\
effectively adds comments or documentation

**fix bugs and improve code**
***fix bugs***\
e.g.
prompt: fix the bugs in this function
def calculate_average(numbers):  
    total = 0  
    for i in range(len(numbers)):  
        number = numbers[i]  
        total += number  
    average = total  
    rerun averave

response:
def calculate_average(numbers):  
    total = 0  
    for i in range(len(numbers)):  
        number = numbers[i]  
        total += number  
    average = total / len(numbers)  
    return average
There were a few issues with the original code:

1. The variable "average" was assigned the value of "total" instead of the actual average.
2. There was a typo in the last line - "rerun" should be "return".
3. The average was not actually calculated - the corrected code divides the total by the length of 
the "numbers" list to get the average.

***improve performance***\
go from O(n) to O(1)

***refactor inefficient code***\
can guide developers on how to refactor code\
prompt: please evaluate code and refactor\

## generate images with aoai
learning achievements:
- describe dall-e capabilities
- use dall-e playground
- azure openai rest interface to integrate dall-e image generation into app

**what is dall-e**
neural network that generates graphical representations from natural language input\
not a search system for finding images; ai model that generates new images based on data on which it was trained\

**Explore DALL-E in Azure OpenAI Studio**
settings adjust:
- number of images to generate
- resolution of generated image

**Use the Azure OpenAI REST API to consume DALL-E models**
requirements are endpoint and key\
initiate image generation by POST request to service endpoint and must contain following parameters in JSON: prompt, n (# of images), size (resolution)\
{
    "prompt": "A badger wearing a tuxedo",
    "n": 1,
    "size": "512x512"
}


## use your own data in aoai
learning achievements:
- describe capabiltiies on your data
- configure aoai to use own data
- use aoai api to generate response based on own data

**Understand how to use your own data**
1. start by ingesting and indexing your data into azure cognitive search
2. receive user prompt
3. determine relevent content and intent of prompt
4. query the search index with content and intent
5. insert search result chunk into aoai prompt, along with system message and user prompt
6. send entire prompt to aoai
7. return response and data reference

***fine-tuning vs. own data***\
fine-tuning is a technique to create a custom model\
aoai on own data uses stateless api to connect to model; which remove req for training custom model\

**Add your own data source**
file upload: supports .md, .txt, .html, .pdf, word and .ppt if any contain images or graphics response depends on readability of text\
recommended to use ai studio to upload data to create search resource and index\
large files use [data prep script](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/use-your-data#ingesting-your-data-into-azure-cognitive-search?azure-portal=true)

semantic search can be enabled and should increase search index leading to higher likelihood of better response, but more costly\

***connect your data***\
in chat playground gui walks through very straightforward\

**Chat with your model using your own data**
***Token considerations and recommended settings***\
data includes search results on index; how does it impact token count then?\
each call includes tokens for system message, user prompt, conversation history, retrieved search doc, internal prompts, model's response\
system message gets truncated to 200 tokens when using own data; typically not token limit\
response is limited to 1500 tokens\
due to limitations recommended to limit both question and conversation history sent to model\

***using the api***\
each call requires endpoint, key, indexName for cog search resource\
example:
{
    "dataSources": [
        {
            "type": "AzureCognitiveSearch",
            "parameters": {
                "endpoint": "<your_search_endpoint>",
                "key": "<your_search_endpoint>",
                "indexName": "<your_search_index>"
            }
        }
    ],
    "messages":[
        {
            "role": "system", 
            "content": "You are a helpful assistant assisting users with travel recommendations."
        },
        {
            "role": "user", 
            "content": "I want to go to New York. Where should I stay?"
        }
    ]
}

call to own data goes to different endpoint than with base model; looks something like\
<your_azure_openai_resource>/openai/deployments/<deployment_name>/extensions/chat/completions?api-version=2023-06-01-preview\
request also needs Content-type and api-key\

[exercise](https://microsoftlearning.github.io/mslearn-openai/Instructions/Labs/06-use-own-data.html)
