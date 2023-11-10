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
add JSOn below, which adds a compounded field to the index to store the class in a category field that is searchable. the second confidenceScore field stores the confidence percentage in a double field
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







## bld ml custom skill for cog search
## search data outside azure platform in cog search using data factory
## maintain cog search solution
## use semantic search to get better search result in cog search
## improve search results using vector search in cog search
