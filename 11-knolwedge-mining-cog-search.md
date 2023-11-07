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







## implement adv search ft in cog search
## bld ml custom skill for cog search
## search data outside azure platform in cog search using data factory
## maintain cog search solution
## use semantic search to get better search result in cog search
## improve search results using vector search in cog search
