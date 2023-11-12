# Implement knolwedge mining with Cognitive Search
cloud-based solution for indexing and querying a wide range of data sources, creating comprehensive and high-scale search solutions such as:
1. index docs and data from a range of sources
2. cog skills to enrich index data
3. store extracted insights in a knowledge store for analysis and integration

## Create cognitive search solution
learning achievements
- create cog search solution
- develop search application

**Manage capacity**
need a cog search resource and perhaps data storage

***service tiers and capacity mgmt***
- Free (F). Use this tier to explore the service or try the tutorials in the product documentation.
- Basic (B): Use this tier for small-scale search solutions that include a maximum of 15 indexes and 2 GB of index data.
- Standard (S): Use this tier for enterprise-scale solutions. There are multiple variants of this tier, including S, S2, and S3; which offer increasing capacity in terms of indexes and storage, and S3HD, which is optimized for fast read performance on smaller numbers of indexes.
- Storage Optimized (L): Use a storage optimized tier (L1 or L2) when you need to create large indexes, at the cost of higher query latency.

can not change pricing tier. if selection isn't suitable you need to create a new resource

***replicas and partitions***
replicas: instances of search service i.e. nodes in a cluster. more replicas can service multiple concurrent query requests while maintaining indexing operations
partitions: divide index into multiple storage locations, enables split I/O ops such as querying or rebuilding an index

**Understand search components**
process is extracting, enriching, indexing, and searching data

***data source***
1. unstructured files in azure blob storage containers
2. tables in azure sql db
3. docs in cosmos db
 alternative, apps can push json data directly into an index, without pulling it from an existing data source

***skill set***
enrichment pipeline in which each step enhances the source data with insights obtained by specific ai skill. examples:
- language doc is written
- key phrases that might help determine the main theme or topics discussed in a doc
- sentiment score of doc
- specific locations, people, organizations, landmarks mentioned
- ai-generated descriptions of images, or image text extracted by ocr
- custom skills that can be developed to meet spec requirements

***indexer***
engine of index process. takes outputs extracted using skills in skillset, along with data and metadata from source, maps to fields in index
automatically runs when created and can be scheduled to run
when add new fields or skills may need to reset the index

***index***
collection of JSON docs, fields contain values extracted during indexing
clients query index to retrieve, filter, and sort info
index field can be configured with following:
1. key: fields that define a unique key for index records
2. searchable:fields that can be queried using full-text search
3. filterable: fields that can be included in filter expressions to return only docs that match specified constraints
4. sortable: fields that can be used to order results
5. facetable: fields that can be used to determine values for facets (user interface elements used to filter the results based on a list of known field values)
6. retrievable: fields that can be included in search results (default all fields are retrievable unless the attribute is explicitly removed)

**Understand the indexing process**
process:
- document for each indexed entity
- enrichment pipeline iteratively builds the docs that combine metadata from data source with enriched fields (JSON structure; document with index fields mapped to fields extracted directly from source data) e.g.
o document
  - metadata_storage_name
  - metadata_author
  - content
docs contain images place image in normalized_images collection: e.g.
o document
  - metadata_storage_name
  - metadata_author
  - content
  - normalized_images
      -- image0
      -- image1
- each skill adds fields to the document, for example a skill to detect language would append another field to the JSON structure
- structured hierarchically, skills applied to a specific context within hierarchy. skill can be run for each item at a particular level e.g. OCR for each image in normalized images
o document
  - metadata_storage_name
  - metadata_author
  - content
  - normalized_images
      -- image0
        text
      -- image1
        text
  - language

final doc structure at end of pipeline mapped to index fields by indexer in 1 of 2 ways:
1. fields extracted directly from source data are all mapped to index fields. can be implicit (fields automatically mapped to fields iwht same name) or explicity(mapping defined to match a source field to an index field)
2. output fields from skills in the skillset are explicitly mapped from their hierarchical location in the output to the target field in the index

**Search an index**
***full text search***
auzre based on lucene query syntax; two variants supported
1. simple: match literal query terms submitted by user
2. full: complex filtering, reg ex, and more sophisticated queries

client apps submit queries with parameters. common parameters are:
- search: expression includes terms to be found
- queryType: Lucene syntax to be evaluated (simple or full)
- searchFields: index fields to search
- select: fields to include in result
- searchMode: criteria for including results based on multiple search terms.

query processing is in four stages: [query types and composition docs](https://learn.microsoft.com/en-us/azure/search/search-query-overview)
1. query parsing: expression is evaluated and reconstructed as tree of subqueries. subqueries might include term queries (find specific individual words), phrase queries (multi-term phrases), and prefix queries (e.g. air*)
2. lexical analysis: query analyzed and refined with text preprocessing conducted.e.g. lowercase and stopwords removed, words are the stemming and split into constituent terms
3. document retrieval: query terms are matched against indexed terms, matching docs is identified
4. scoring: tf/IDF

**Apply filtering and sorting**
***filtering results***
two filter functions
1. include in simple search expression
2. provide OData filter expression as $filter parameter with full syntax search expression. case sensitive

example: suppose you want to find documents containing the text London that have an author field value of Reviewer. simple search expression:
//simple solution//
search=London+author='Reviewer'
queryType=Simple

//OData solution//
search=London
$filter=author eq 'Reviewer'
queryType=Full

***fitlering with facets***
work best when a field has a small number of discrete values
return all possible values for the author field:
//facets example//
search = *
facet=author
//subsequent query use result from previous//
search=*
$filter=author eq 'selected-facet-value-here'

***sorting results***
sorted based on relevancy by default
//Odata example override//
search=*
$orderby=last_modified desc

**Enhance the index**
***search-as-you-type***
[suggesters docs](https://learn.microsoft.com/en-us/azure/search/search-autocomplete-tutorial)
add suggester to index; enable 2 forms of search-as-you-type experience
1. suggestions: retrieve and display list of suggested results as the user types
2. autocomplete: complete partially typed search terms

suggestion and autocomplete REST API endpoints or to submit partial search term and retrieve list of suggested results or autocompleted terms

***custom scoring and result boosting***
[socring profile docs](https://learn.microsoft.com/en-us/azure/search/index-add-scoring-profiles)
sort relevance is based on tf/idf and is customizable by defining scoring profile
boost results based on field values: increase relevancy score for docs based on how recently they were modified or their file size

***synonyms***
[synonym docs](https://learn.microsoft.com/en-us/azure/search/search-synonyms)
synonym maps: link related terms can be applied to individual fields in index

## create a custom skill for cog search
learning achievements:
- implement custom skill cog search
- integrate custom skill in cog search skillset

examples of custom functionality
- integrate form recognizer service to extract data from forms
- consume ML model to integrate predicted values into an index
- other custom logic

**create custom skill**
***input schema***
{
    "values": [
      {
        "recordId": "<unique_identifier>",
        "data":
           {
             "<input1_name>":  "<input1_value>",
             "<input2_name>": "<input2_value>",
             ...
           }
      },
      {
        "recordId": "<unique_identifier>",
        "data":
           {
             "<input1_name>":  "<input1_value>",
             "<input2_name>": "<input2_value>",
             ...
           }
      },
      ...
    ]
}

***output schema***
{
    "values": [
      {
        "recordId": "<unique_identifier_from_input>",
        "data":
           {
             "<output1_name>":  "<output1_value>",
              ...
           },
         "errors": [...],
         "warnings": [...]
      },
      {
        "recordId": "< unique_identifier_from_input>",
        "data":
           {
             "<output1_name>":  "<output1_value>",
              ...
           },
         "errors": [...],
         "warnings": [...]
      },
      ...
    ]
}

**Add a custom skill to a skillset**
add skill using Custom.WebApiSkill to indexing solution. Skill must:
- specify the URI to web API endpoint, including parameters and headers if necessary
- set context to specify at which point in the document hierarchy the skill should be called
- assign input values, usually from existing document fields
- store output in a new field, optionally specifying a target field name (otherwise the output name is used)
example:
{
    "skills": [
      ...,
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "<custom skill description>",
        "uri": "https://<web_api_endpoint>?<params>",
        "httpHeaders": {
            "<header_name>": "<header_value>"
        },
        "context": "/document/<where_to_apply_skill>",
        "inputs": [
          {
            "name": "<input1_name>",
            "source": "/document/<path_to_input_field>"
          }
        ],
        "outputs": [
          {
            "name": "<output1_name>",
            "targetName": "<optional_field_name>"
          }
        ]
      }
  ]
}

## create knowledge store with cog search
learning achievements:
- create a knowledge store from cog search pipeline
- view data in projections in a knowledge store

search solution with pipeline of ai skills to enrich data and populate an index
- language
- key themes
- sentiment
- NER
- descriptions of images or image text extraction

**Define projections**
each skill in skillset builds a JSON of enrich data for docs being indexed, can persist some or all fields in a projections

***using shaper skill***
organizes the iterative data from the skill to a more well formatted JSON; easier mapping

**define a knowledge store**
knowledgeStore object in the skillset
can define object,table, file projections. must define a separate projection for each projection defined
example of creating each projection:
"knowledgeStore": { 
      "storageConnectionString": "<storage_connection_string>", 
      "projections": [
        {
            "objects": [
                {
                "storageContainer": "<container>",
                "source": "/projection"
                }
            ],
            "tables": [],
            "files": []
        },
        {
            "objects": [],
            "tables": [
                {
                "tableName": "KeyPhrases",
                "generatedKeyName": "keyphrase_id",
                "source": "projection/key_phrases/*",
                },
                {
                "tableName": "docs",
                "generatedKeyName": "document_id", 
                "source": "/projection" 
                }
            ],
            "files": []
        },
        {
            "objects": [],
            "tables": [],
            "files": [
                {
                "storageContainer": "<container>",
                "source": "/document/normalized_images/*"
                }
            ]
        }
    ]
 }

- knoweldge store that contains a relational schema fo enriched data requires a Table projection to be defined.
- knowledge store that contains JSON representation of the indexed documents requires an Object projection.
- knowledge store that contains images extracted from indexed documents requires a File projection (create a .jpg file for each image extracted from a doc)

## enrich search index using language studio
learning achievements:
- language studio to enrich cog search indexes
- enrich cog search index with custom class

**Explore the available features of Azure AI Language**
grouped features
1. classify text
2. understand QnA lang
3. extract info
4. summarize
5. translate

**Create, train, and deploy a conversation language understanding model**
cutomizable features requires different steps to create model
process steps after creating lanugage resource
1. schema defintion. adding intents and entities app is interested in
2. label data: example chats and utterances along with how they map to entities and intents
3. train model: 80/20 split
4. review performance
5. deploy model
6. test deployment

**Enrich a cognitive search index with custom classes and Language Studio**
considerations to enrich search index using custom text classification
1. store docs so they can be accessed by language studio and cognitive search indexers
2. create custom text classification project
3. train and test
4. create search index based on docs
5. create function app that usees deployed model
6. update search solution, index, indexer, custom skillset

***store data***
azure blob storage. make container accessible

***Create your Language Studio project***
create resource and set roles for language resource and storage account [docs for ref](https://learn.microsoft.com/en-us/azure/ai-services/language-service/custom-text-classification/how-to/create-project?tabs=azure-portal%2Cstudio%2Csingle-classification#set-roles-for-your-azure-language-resource-and-storage-account)

***train classification model***
standard 80/20 split comments and test performance
labeling key consideration here

***create search index***
follow [cog search solution](https://learn.microsoft.com/en-us/training/modules/create-azure-cognitive-search-solution/) and update index, indexer, and custom skill after creating function app

***create azure function app***
app pass json to custom text classification endpoint example below
{
    "displayName": "Extracting custom text classification", 
    "analysisInput": {
        "documents": [
            {
                "id": "1", 
                "language": "en-us", 
                "text": "This film takes place during the events of Get Smart. Bruce and Lloyd have been testing out an invisibility cloak, but during a party, Maraguayan agent Isabelle steals it for El Presidente. Now, Bruce and Lloyd must find the cloak on their own because the only non-compromised agents, Agent 99 and Agent 86  are in Russia"
            }
        ]
      }, 
    "tasks": [
        {
        "kind": "CustomMultiLabelClassification", 
        "taskName": "Multi Label Classification", 
        "parameters": {
            "project-name": "movie-classifier", 
            "deployment-name": "test-release"}
        }
    ]
}

then process JSON response from model, for example
{
  "jobId": "be1419f3-61f8-481d-8235-36b7a9335bb7",
  "lastUpdatedDateTime": "2022-06-13T16:24:27Z",
  "createdDateTime": "2022-06-13T16:24:26Z",
  "expirationDateTime": "2022-06-14T16:24:26Z",
  "status": "succeeded",
  "errors": [],
  "displayName": "Extracting custom text classification",
  "tasks": {
    "completed": 1,
    "failed": 0,
    "inProgress": 0,
    "total": 1,
    "items": [
      {
        "kind": "CustomMultiLabelClassificationLROResults",
        "taskName": "Multi Label Classification",
        "lastUpdateDateTime": "2022-06-13T16:24:27.7912131Z",
        "status": "succeeded",
        "results": {
          "documents": [
            {
              "id": "1",
              "class": [
                {
                  "category": "Action",
                  "confidenceScore": 0.99
                },
                {
                  "category": "Comedy",
                  "confidenceScore": 0.96
                }
              ],
              "warnings": []
            }
          ],
          "errors": [],
          "projectName": "movie-classifier",
          "deploymentName": "test-release"
        }
      }
    ]
  }
}

function returns structured JSON message back to custom skillset in cog search, for example
[{"category": "Action", "confidenceScore": 0.99}, {"category": "Comedy", "confidenceScore": 0.96}]

function app needs to know 5 things:
1. text to be classified
2. endpoint for trained custom text classification deployed model (deploying model pane in studio)
3. primary key for custom text class project (project settings)
4. project name (project settings)
5. deployment name (deploying model pane in studio)

***update azure cog search solution***
3 changes in azure portal to make to enrich search index
1. add field to your index to store the custom text classification enrichment
2. add custom skillset to call your function app with the text to classify
3. map response from skillset into the index

***add field to existing index***
add Json below, which adds a compounded field to the index to store the class in a category field that is searchable. the second confidenceScore field stores the confidence percentage in a double field
{
  "name": "classifiedtext",
  "type": "Collection(Edm.ComplexType)",
  "analyzer": null,
  "synonymMaps": [],
  "fields": [
    {
      "name": "category",
      "type": "Edm.String",
      "facetable": true,
      "filterable": true,
      "key": false,
      "retrievable": true,
      "searchable": true,
      "sortable": false,
      "analyzer": "standard.lucene",
      "indexAnalyzer": null,
      "searchAnalyzer": null,
      "synonymMaps": [],
      "fields": []
    },
    {
      "name": "confidenceScore",
      "type": "Edm.Double",
      "facetable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": false,
      "analyzer": null,
      "indexAnalyzer": null,
      "searchAnalyzer": null,
      "synonymMaps": [],
      "fields": []
    }
  ]
}

***edit the custom skillset***
just like above in azure portal, select skillset and add JSON in this format. WebApiSkill definition specifies the language and the contents of a doc are passed as inputs to the function app. app will return JSON text named class
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "name": "Genre Classification",
  "description": "Identify the genre of your movie from its summary",
  "context": "/document",
  "uri": "https://learn-acs-lang-serives.cognitiveservices.azure.com/language/analyze-text/jobs?api-version=2022-05-01",
  "httpMethod": "POST",
  "timeout": "PT30S",
  "batchSize": 1,
  "degreeOfParallelism": 1,
  "inputs": [
    {
      "name": "lang",
      "source": "/document/language"
    },
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "text",
      "targetName": "class"
    }
  ],
  "httpHeaders": {}
}

***map output from function ap into index***
map output into the index. azure portal, select indexer and edit JSON to have a new output mapping. Below, indexer now knows the output from the function app document/class should be stored in the classifiedtext field. defined in compound field (see add field to existing index) function app has to return a JSON array containing a category and confiendenceScore field. You can now search enriched search index for custom classified text

{
  "sourceFieldName": "/document/class",
  "targetFieldName": "classifiedtext"
}

#knowledge check reminders:
where do you copy the full endpoint (you read this as where do you find it. slow down): azure extension inside vs code
which step enables cog search index to store enrichments: update to cog search solution


## Implement adv search ft in cog search
learning achievements:
- improve the ranking of a document with term boosting
- improve the relevance of results by adding scoring profiles
- improve an index with analyzers and tokenized terms
- enhance an index to include multiple languages
- improve search experience by ordering results by distance from a given ref point

**Improve the ranking of a document with term boosting**
write more complex lucene queries; then improve relevance by boosting specific terms in search query

***search an index***
query processing reminder: query parsing, lexical analysis, doc retrieval, scoring

***write a simple query***
search=luxury&$select=Hotelid, HotelName, Category, Tags, Description&$count=true
query parses will search for term luxury across all fields for a document in the index; select limits returned fields; last param asks index to count total results

***enable lucene query parser***
add to query &queryType=full to the query string
Lucene syntax more precise queries with features available such as:
- Boolean operators: and, or, not
- fielded search: fieldName: search term
- fuzzy search: ~ e.g. Description:luxury~
- term proximity search: "term1 term2"~n; returns docs with words within three words of each other
- reg ex search: /regular expression/
- wildcard search: *  is many characters, ? is single character
- precedence grouping: (term AND (term OR term)) e.g (Description:luxury OR category:luxury) and Tags:air?con*
- term boosting: ^ e.g. description:luxury OR category:luxury^3; would give hotels with category luxury a higher score than luxury in description
[lucene query syntax docs](https://learn.microsoft.com/en-us/azure/search/query-lucene-syntax)

***boost search terms***
query + boosting:
search=(Description:luxury OR Category:luxury^3) AND Tags:'air con'*&$select=HotelId, HotelName, Category, Tags, Description&$count=true&queryType=full

**Improve the relevance of results by adding scoring profiles**
cog search uses BM25 similarity ranking algo.
how can we alter to fit criteria we need too?

***how search scores are calculated***
- score = F(# of times identified search terms appear in doc, doc size, rarity of each of the terms) \n
search results are ordered by their search score, highest first; where identical score, break tie by adding $orderby clause

***improve the score for more relevant documents***
scoring profiles to enhance document returns
approaches:
1. most straightforward scoring profile defines different weights for fields in an index
profile can include functions, e.g. distance or freshness
consider, defining the boosting duration applied to newer documents before they score teh same as older document
*key point: instead of boosting a specific term in a search request, you can aplpy a scoring profile to an index so that fields are boosted automatically for all queries

***add a weighted scoring profile***
100 scoring profiles to a search index
azure portal simplest waqy to create. How too:
1. go to search service
2. select indexes, select index to add scoring profile
3. select scoring profile
4. select + Add scoring profile
5. enter unique name in profile name
6. set scoring profile as default to be applied to all searches select set as default profile
7. in field name, select field; then weight
8. save

***use functions in a scoring profile***
available functions:
|function|description
--|--|
|magnitude|alter scores based on a range of values for a numeric field|
|freshness|alter scores based on the freshness of docs as given by a datetimeoffset field|
|distance|alter scores based on the distance between a reference location and a GeographyPoint field|
|tag|alter scores based on common tag values in docs and queries|

**Improve an index with analyzers and tokenized terms**
***analyzers in cog search***
- processing examples: text should be broken into words (by whitespace and punctuation characters as delims); stopswords should be removed; reduce to root (stemming)

analyzers run processing examples in cognitive search. lucene analyzer used if none specified
alternative built-ins:
- language analyzers: advanced capabilities for specific languages (e.g. lemmatization, word decompounding, entity recognition. 50 analyzers for different languages)
- specialized: language-agnostic and used for specialized fields such as zip codes or product IDs. e.g. PatternAnalyzer and specify a reg ex to match token separators

***what is a customer analyzer***
constsist of:
- character filters: process string before it is tokenized
- tokenizer: divide text into tokens to be added to index
- token filters: remove or modify the tokens emitted by tokenizer

***character filters***
1. html_strip: remvoes html constructs such as tags and attributes
2. mapping: enables specify mappings that replace 1 string with another. e.g. replace TX with Texas
3. pattern_replace: specify a reg ex identifying patterns in input text and how matching text should be replaced

***tokenizers***
usually single word, but might want to create unusual tokens e.g.:
- full postal address
- compelte url or email
- words based on grammar of specific language

13 tokenizers to choose from:
1. classic: tokenizes based on grammar for european languges
2. keyword: tokenizer emits entire input as a single token. for fields that should always be indexed as 1 value
3. lowercase: divides text at non-letters and modifies resulting token to all lower case
4. microsoft_language_tokenizer: divides text based on grammer of language specified
5. pattern: divides text where matches reg ex specified
6. whitepace: tokenizer divides text wherever there's white space
[custom analyzer_and_all_tokenizer docs search for Add custom analyzers to string fields in an Azure Cognitive Search index]

***token fitlers***
extra processing after tokenization
41 different token filters available:
1. language-specific filters, such as arabic_normalization: apply language specific grammar rules to ensure forms of words are removed and replaced with roots
2. apostrophe: filter remvoes any apostrophe from a token and any characters after the apostrophe
3. classic: removes english possessives and dots from acronyms
4. keep: remvoes any token that doesn't include one or more words from a list you specify
5. length: removes any token that is longer than your specified min or shorter than your specified amx
6. trim: removes any leading and trailing white space from tokens

***create a customer analyzer***
must use json code below. can't specify custom index in azure portal
can include only one tokenizer but one or more character filters and one or more token fitlers
analyzers section of hte index at design time
uniuqe name and set the @odata.type : '#Microsoft.Azure.Search.CustomeAnalyzer'
"analyzers":(optional)[
   {
      "name":"ContosoAnalyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "WebContentRemover"
      ],
      "tokenizer":"IcelandicTokenizer",
      "tokenFilters":[
         "ApostropheFilter"
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"WebContentRemover",
      "@odata.type":"#html_strip"
   }
],
"tokenizers":(optional)[
   {
      "name":"IcelandicTokenizer",
      "@odata.type":"#microsoft_language_tokenizer",
      "language":"icelandic",
      "isSearchTokenizer":false,
   }
],
"tokenFilters":(optional)[
   {
      "name":"ApostropheFilter",
      "@odata.type":"#apostrophe"
   }
]

***test a custom analyzer***
analyze text function REST API to submit test text and ensure analyzer returns tokens correctly
any rest testing tool to formulate requests such as Postman application

REST request example: 
POST https://<search service name>.search.windows.net/indexes/<index name>/analyze?api-version=<api-version>
   Content-Type: application/json
   api-key: <api key>

- replace <search service name> with cog search resource
- replace <index name> with name of index that includes the custom analyzer
- replace <api-version> with the version number of the rest api
- replace <api-key> with access key for cog search resource

request must also include a json body like:
{
  "text": "Test text to analyze.",
  "analyzer": "<analyzer name>"
}

***use a custom analyzer for a field***
once defined and tested now configure index to use it
analyzer can be specified for each field in index

analzyer field when you want to use the same analyzer for both indexing and search:
"fields": [
 {
   "name": "IcelandicDescription",
   "type": "Edm.String",
   "retrievable": true,
   "searchable": true,
   "analyzer": "ContosoAnalyzer",
   "indexAnalyzer": null,
   "searchAnalyzer": null
 },

you can also use a different analyzer when indexing the field and when searching the field. different set of processing steps when you index a field to when you analyze a query. use the configuration below if you need different set of processing steps
"fields": [
 {
   "name": "IcelandicDescription",
   "type": "Edm.String",
   "retrievable": true,
   "searchable": true,
   "analyzer": null,
   "indexAnalyzer": "ContosoIndexAnalyzer",
   "searchAnalyzer": "ContosoSearchAnalyzer"
 },

**Enhance an index to include multiple languages**
- can add manually by providinga ll translated text fields in all different lanugages you wnat to support
- choose azure ai sdervices for translated text through enrichment pipeline

1. how to add fields iwht different languages to index
2. constrain results to fields wiht specific languages
3. create scoring profile to boost native language of your end users

***add language specific fields***
1. identify all fields that need translation
2. duplicate those fields for each language you want to support and add corresponding language analyzer [docs](https://learn.microsoft.com/en-us/azure/search/index-add-language-analyzers#supported-language-analyzers)
json definition example:
{
      "name": "description",
      "type": "Edm.String",
      "facetable": false,
      "filterable": false,
      "key": false,
      "retrievable": true,
      "searchable": true,
      "sortable": false,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null,
      "synonymMaps": [],
      "fields": []
    },
    {
      "name": "description_de",
      "type": "Edm.String",
      "facetable": false,
      "filterable": false,
      "key": false,
      "retrievable": true,
      "searchable": true,
      "sortable": false,
      "analyzer": "de.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null,
      "synonymMaps": [],
      "fields": []
    },
    {
      "name": "description_fr",
      "type": "Edm.String",
      "facetable": false,
      "filterable": false,
      "key": false,
      "retrievable": true,
      "searchable": true,
      "sortable": false,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null,
      "synonymMaps": [],
      "fields": []
    },
    {
      "name": "description_it",
      "type": "Edm.String",
      "facetable": false,
      "filterable": false,
      "key": false,
      "retrievable": true,
      "searchable": true,
      "sortable": false,
      "analyzer": "it.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null,
      "synonymMaps": [],
      "fields": []
    },

***limit the fields for language***
searchFields and select properties are key here

***enrich an index with multiple languages using AI services***
steps:
1. add fields for each language
2. add skill for each language
3. map translated text to correct fields

***add japanese and ukrainian examples***
{
  "name": "description_jp",
  "type": "Edm.String",
  "facetable": false,
  "filterable": false,
  "key": false,
  "retrievable": true,
  "searchable": true,
  "sortable": false,
  "analyzer": "ja.microsoft",
  "indexAnalyzer": null,
  "searchAnalyzer": null,
  "synonymMaps": [],
  "fields": []
},
{
  "name": "description_uk",
  "type": "Edm.String",
  "facetable": false,
  "filterable": false,
  "key": false,
  "retrievable": true,
  "searchable": true,
  "sortable": false,
  "analyzer": "uk.microsoft",
  "indexAnalyzer": null,
  "searchAnalyzer": null,
  "synonymMaps": [],
  "fields": []
}

***add translation skillsets continuation of japanese and ukranian example***
"skills": [
  {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "name": "#1",
    "description": null,
    "context": "/document/description",
    "defaultFromLanguageCode": "en",
    "defaultToLanguageCode": "ja",
    "suggestedFrom": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/description"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "description_jp"
      }
    ]
  },
  {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "name": "#2",
    "description": null,
    "context": "/document/description",
    "defaultFromLanguageCode": "en",
    "defaultToLanguageCode": "uk",
    "suggestedFrom": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/description"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "description_uk"
      }
    ]
  }
]

***map translated output into index final step of japanese/ukranian example***
update indexer to map translated text into the index
"outputFieldMappings": [
  {
    "sourceFieldName": "/document/description/description_jp",
    "targetFieldName": "description_jp"
  },
  {
    "sourceFieldName": "/document/description/description_uk",
    "targetFieldName": "description_uk"
  }
]

**Improve search experience by ordering results by distance from a given reference point**
near a physical point or within a bounded area

***what are geo-spatial functions***
2 functions for locaiton info. index must include location for results. Location fields should have the datatype Edm.GeographyPoint and store lat?long
1. geo.distance: function returns distance in a straight line across earth's surface from point you specify to the location of search result
2. geo.intersects: function returns true if location of search result is inside a polygon specified

***use geo.distance function***
returns results in kilometers
location: name of field that stores location
geography'POINT: longitude and latitude
le 5: inclusive results if geo.distance function returns a number less than or equal to the set 5 kilos (example)
- important note: when you use geo.distance in a filter, the equal (eq) and not equal (ne) operators are not supported. instead, use lt, le, ft, or ge

can use geo.distance in orderby clause e.g.:
search=(Description:luxury OR Category:luxury)&orderby=geo.distance(Location, geography'POINT(2.294481 48.858370)') asc&$select=HotelId, HotelName, Category, Tags, Description&$count=true

***use geo.intersects function***
needs 3 or more points to create polygon. Additionally points must be specified in counterclockwise order and must be closed i.e. first and last points must be the same. example below:
search=(Description:luxury OR Category:luxury) AND geo.intersects(Location, geography'POLYGON((2.32 48.91, 2.27 48.91, 2.27 48.60, 2.32 48.60, 2.32 48.91))')&$select=HotelId, HotelName, Category, Tags, Description&$count=true

*Note: adding translation to skillset for enhanced search. The Azure portal assumes the first field in the document needs to be translated

## bld ml custom skill for cog search
learning achievements
- understand how to use custom ml skillset
- use aml to enrich cog search indexes

**Understand how to use a custom Azure Machine Learning skillset**
using ml custom works same as adding anoy othe rcustom skill to search index (AmlSkill)

***custom AmlSkill schema***
enrichment happens at document level
example:
{
      "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
      "name": "AML name",
      "description": "AML description",
      "context": "/document",
      "uri": "https://[Your AML endpoint]",
      "key": "Your AML endpoint key",
      "resourceId": null,
      "region": null,
      "timeout": "PT30S",
      "degreeOfParallelism": 1,
      "inputs": [
        {
          "name": "field name in the AML model",
          "source": "field from the document in the index"
        },
        {
          "name": "field name in the AML model",
          "source": "field from the document in the index"
        },

      ],
      "outputs": [
        {
          "name": "result field from the AML model",
          "targetName": "result field in the document"
        }
      ]
    }
note: uri has to use https endpoint. above example has timeout of 30 seconds. depending on infra, increase parallelism and timeout based on needs
- best way to manage efficiency of aml skill is to scale up kubernetes inference cluster

index for docs needs a field to store results from AML model. then add output field mapping to store results from custom skill set to the field on the doc in the index
json output field mapping example to complete:
"outputFieldMappings": [
    {
      "sourceFieldName": "/result field in the document",
      "targetFieldName": "result field from the AML model"
    }
  ]

***Enrich a search index using an Azure Machine Learning model***
1. create model
2. alter scoring code calls model to allow it to be used by custom search skill
3. create kubernetes cluster to host an endpoint for model

***create aml workspace***
creates storage, key store, application insights resource

***create and train model in ml studio***
must be registered in aml learning studio to deploy model to web service

***alter how model works to allow it to be called by the aml custom skill***
code that handles data and passes it to the model needs to be changed to handle single rows. json response from model should also contain only the output prediction e.g.
data is an array of json objects:
[ 
    {
        "attribute-1": null,
        "attribute-2": null
    },
    {
        "attribute-1": null,
        "attribute-2": null
    },
    {
        "attribute-1": null,
        "attribute-2": null
    }
]

then python scoring code will ahve to process the data a row at a time:
data = json.loads(data)
for row in data:
    for key, val in row.items():
        input_entry[key].append(decode_nan(val))

to change input to single record python code needs to become:
data = json.loads(data)
for key, val in data.items():
    input_entry[key].append(decode_nan(val))

response from scoring returns to whole json doc:
return json.dumps({"result": result.data_frame.values.tolist()})

custom skill needs to map to a single response from the model; code should return json of only the last attribute
output = result.data_frame.values.tolist()
//return the last column of the the first row of the dataframe
return {
    "predicted_outcome": output[0][-1]
}

***create an endpoint for your model to use***
|restrictions|
--|
|ml studio deploys to real-time, batch, or web service endpoints. currently custom AmlSkill only supports web service endpoints|
|endpoint has to be an azure kubernetes service, container instances aren't supported|

aml stuido creates and manages cluster natively, don't need to manually create. done in the compute section of studio.

***connect aml custom skill to endpoint***
update cog search service
1. enrich search index by adding new field to include output for the model.
2. update index skillset and add #Microsoft.Skills.Custom.AmlSkill
3. change indexer to map the output from custom skill to field created
4. rerun indexer to enrich index with AML model


## search data outside azure platform in cog search using data factory
learning achievements
- azure data factory to copy data into cog search index
- cog search push api to add an index from any external data source

most flexible way to get data into index is to push data into cog search

**index data from external data sources using azure data factory**
***push data into search index using azure data factory***
factory comes connected to ~100 different data stores
sink is equal to a source or target in pipelines

***create factory pipeline to push data into a search index***
1. create cog search index with all the fields want to store data
2. create pipeline with a copy data step
3. create data source connection to where data resides
4. create sink to connect to your search index
5. map fields from source data to search index
6. run pipeline to push data into index

Example: customer data in json format [example](https://learn.microsoft.com/en-us/training/modules/search-data-outside-azure-platform-cognitive-search/02-index-data-from-external-data-sources-factory)

***create search index***
[learning_module_example](https://learn.microsoft.com/en-us/training/modules/search-data-outside-azure-platform-cognitive-search/02-index-data-from-external-data-sources-factory)
[data factory docs](https://learn.microsoft.com/en-us/azure/data-factory/lab-data-flow-data-share)

**Index any data using the Azure Cognitive Search push API**
***supported rest api operations***
two api operations for cog search [search and managmenet]. search is the focus for this module
|feature|operations|
--|--|
|index|create,delete,updated,and configure|
|document|get,add,update,and delete|
|indexer|configure data sources and scheduling on limited data sources|
|skillset|get,create,delete,list,and update|
|synonym map|get,create,delete,list,and update|

***how to call teh search rest api***
- use https endpoint (over port 443) provided by search service, you must include an api-version in URI
- request header must include an api-key attribute

***add data to an index***
use http post request using the indexes feature in this format
POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]

body of request needs to let rest endpoint know action to take on the doc, which doc to apply action too, and what data to use
{  
  "value": [  
    {  
      "@search.action": "upload (default) | merge | mergeOrUpload | delete",  
      "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)  
      "field_name": field_value (key/value pairs matching index schema)  
        ...  
    },  
    ...  
  ]  
}

|action|description|
--|--|
|upload|similar to upsert in sql, doc will be created or replaced|
|merge|merge updates an existing doc with specific fields. merge will fail if no doc can be found|
|mergeOrUpload|merge updates an existing doc with specified fields, and uploads it if the doc doesn't exist|
|delete|deletes the whole document, you only need to specify the key_field_name|
response 200 if successful [full response codes docs](https://learn.microsoft.com/en-us/rest/api/searchservice/addupdate-or-delete-documents#response)

full example (can add as many docs in value array as you want. optimal performance consider batching max 1k docs or 16 MB):
{
  "value": [
    {
      "@search.action": "upload",
      "id": "5fed1b38309495de1bc4f653",
      "firstName": "Sims",
      "lastName": "Arnold",
      "isAlive": false,
      "age": 35,
      "address": {
        "streetAddress": "Sumner Place",
        "city": "Canoochee",
        "state": "Palau",
        "postalCode": "1558"
      },
      "phoneNumbers": [
        {
          "phoneNumber":  {
            "type": "home",
            "number": "+1 (830) 465-2965"
          }
        },
        {
          "phoneNumber":  {
            "type": "home",
            "number": "+1 (889) 439-3632"
          }
        }
      ]
    }
  ]
}

***use .NET core to index any data***
powershell client library with NuGet
dotnet add package Azure.Search.Documents --version 11.4.0

index performance based on 6 key factors
1. search service tier and how many replicas and partitions enabled
2. complexity of index schema. reduce how many properties (searchable, facetable, sortable) each field has.
3. number of docs in each batch, best size depends on index schema and size of docs
4. multithreaded approach
5. handling errors and throttling. use exponential backoff retry strategy
6. where data resides, try index data as close to search index. e.g. run uploads from inside azure environment

***work out optimal batch size***
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}


***Implement exponential backoff retry strategy***
index starts to throttle requests due to overload a 503 (request rejected due to heavy load) or 207 (some documents failed in batch) status is received
good strategy is to backoff. back off means pausing for some time before ertrying request again. 
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);

***use threading to improve performance***

sample code to complete doc uploading app by combining backoff with threading
public static async Task IndexDataAsync(SearchClient searchClient, List<Hotel> hotels, int batchSize, int numThreads)
        {
            int numDocs = hotels.Count;
            Console.WriteLine("Uploading {0} documents...\n", numDocs.ToString());

            DateTime startTime = DateTime.Now;
            Console.WriteLine("Started at: {0} \n", startTime);
            Console.WriteLine("Creating {0} threads...\n", numThreads);

            // Creating a list to hold active tasks
            List<Task<IndexDocumentsResult>> uploadTasks = new List<Task<IndexDocumentsResult>>();

            for (int i = 0; i < numDocs; i += batchSize)
            {
                List<Hotel> hotelBatch = hotels.GetRange(i, batchSize);
                var task = ExponentialBackoffAsync(searchClient, hotelBatch, i);
                uploadTasks.Add(task);
                Console.WriteLine("Sending a batch of {0} docs starting with doc {1}...\n", batchSize, i);

                // Checking if we've hit the specified number of threads
                if (uploadTasks.Count >= numThreads)
                {
                    Task<IndexDocumentsResult> firstTaskFinished = await Task.WhenAny(uploadTasks);
                    Console.WriteLine("Finished a thread, kicking off another...");
                    uploadTasks.Remove(firstTaskFinished);
                }
            }

            // waiting for the remaining results to finish
            await Task.WhenAll(uploadTasks);

            DateTime endTime = DateTime.Now;

            TimeSpan runningTime = endTime - startTime;
            Console.WriteLine("\nEnded at: {0} \n", endTime);
            Console.WriteLine("Upload time total: {0}", runningTime);

            double timePerBatch = Math.Round(runningTime.TotalMilliseconds / (numDocs / batchSize), 4);
            Console.WriteLine("Upload time per batch: {0} ms", timePerBatch);

            double timePerDoc = Math.Round(runningTime.TotalMilliseconds / numDocs, 4);
            Console.WriteLine("Upload time per document: {0} ms \n", timePerDoc);
        }

   


## maintain cog search solution
## use semantic search to get better search result in cog search
## improve search results using vector search in cog search
