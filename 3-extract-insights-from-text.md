# Extract insights from text

[Learning Path](https://learn.microsoft.com/en-us/training/modules/extract-insights-text-with-text-analytics-service/1-introduction)

Learning Achievements:
- detect language
- extract key phrases
- analyze sentiment
- extract entities
- extract linked entities

**Provision an Azure AI Language resource**
- language detection: provides language that text is written
- key phrase extraction: identify main points from important words and key phrases
- sentiment analysis: is the text positive, negative, or neutral
- named entity recognition: detect entities (people, organization, location, time period, etc.)
- entity linking: tieing entities to wikipedia articles

can use single-service or multi-service

**Detect language**
- evaluates text and for each document submitted returns language. 120 languages recognized (single phrase or document)
- less than 5,120 characters
- multilingual document gets a return of the language that has the most representation
- accurate json format
{
  "documents": [
    {
      "countryHint": "US",
      "id": "1",
      "text": "Hello world"
    },
    {
      "id": "2",
      "text": "Bonjour tout le monde"
    }
  ]
}


**Extract Key Phrases**
- extracts main points from documents or document
- works better for larger documents; limit of 5,120 still applicable
- submission and output very similar to detect language

**Analyze sentiment**
- overall document or individual sentence for each document
- all sentences neutral; overall sentiment neutral
- sentence classification positive and neutral, overall sentiment positive
- sentence classification negative and neutral, overall sentiment negative
- sentence classification positive and negative, overall sentiment mixed

**Extract Entities**
[List of all categories](https://learn.microsoft.com/en-us/azure/ai-services/language-service/named-entity-recognition/concepts/named-entity-categories?tabs=ga-api)

**Extract linked entities**
- wikipedia is knowledge base for Text analytics service

## Translate text with the Azure AI Translator service
Learning Achievements:
- provision AI translator resource
- understand language detection, translation, transliteration
- specify translation options
- define customer translations

**Provision translator resource**
-detection: example (input: { 'Text' : 'こんにちは' }) (bash call: curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Region: <your-service-region>" -H "Ocp-Apim-Subscription-Key: <your-key>" -H "Content-Type: application/json" -d "[{ 'Text' : 'こんにちは' }])
-translation (one-to-many): use same curl just change /detect? to /translate?
-transliteration: converting text from native to an alternative script (bash call: curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&fromScript=Jpan&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <your-key>" -H "Ocp-Apim-Subscription-Region: <your-service-region>" -H "Content-Type: application/json" -d "[{ 'Text' : 'こんにちは' }]")

**specify translation options**
- world alignment: creates character mapping from output language to origin language
- sentence length: gives length of sentence (both origin and translated)
- profanity filtering: three variables NoAction, Delete, Marked

**Define custom translations**
- example call: curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=nl&category=<category-id>" -H "Ocp-Apim-Subscription-Key: <your-key" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Where can I find my employee details?'}]"
[Lab](https://microsoftlearning.github.io/mslearn-ai-language/Instructions/Labs/06-translate-text.html)
