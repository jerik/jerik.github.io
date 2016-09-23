# Get started with kibana4
Short tutorial how to get started with kibana on OSX for rookies (like me at this topic)

# Install (on *nix flavours)
- elasitcsearch 
- kibana4

# Fireup 
on the shell start `bin/elasticsearch` and `bin/kibana`

- elasticsearch will listen on: http://localhost:9200
- kibana will listen on: http://localhost:5601

# Import data 
Data must be imported to elasticsearch. On Kibana you must point to the index (in RDBM term index = database) on which kibana shall work
elasticsearch works great with json files, but to import you  have to tweak the json that it is conform to [elasticsearch's bulk format](https://www.elastic.co/guide/en/elasticsearch/guide/current/bulk.html). 

## Prepare import data
There are 2 ways: 

1. if you import your data with curl and specify the index (=database) and type (=collection) on the request, then you can use en empty action-line
2. if you import your data with curl and do not specify index and type, you have to specify ist on the action-line

## Example for perparing way 1: 

recipies.json (bulk format, mind the linebreak: `\n` at the end of each line):

    { "index": {}}
    {"recipe":"French fries","condiment":"potatoes, salt, oil","perparation":"cut potatoes to chips, heat oil in chip pan, put chips into oil, when ready fetch chips and add salt"}
    { "index": {}}
    {"recipe":"Yum fries","condiment":"potatoes, salt, oil","perparation":"cut yum to chips, heat oil in chip pan, put chips into oil, when ready fetch chips and add salt"}
  

## shove recipies.json (bulk) into elasticsearch via your terminal. 

    curl -XPOST 'localhost:9200/chef/cooking/_bulk?pretty' --data-binary @recipies.json

Mind that *chef* is the index (= database) and *cooking* the type (= collection)

## delete index
If something went wrong or you have to clean up the index, then run the following command to delete the index: 

    curl -XDELETE 'http://localhost:9200/chef'

## Verify if the data is imported
In the browser or via curl

     localhost:9200/_cat/indices?v

This show all indexes in elasticsearch with the amount of documents 

## Verify the mapping of your index
In the browser or via curl. I had the problem that some integers where recognized as strings instead of numbers

	http://localhost:9200/chef/_mapping

Changing mappings is not directly possible. There are some [workarounds]( https://www.elastic.co/blog/changing-mapping-with-zero-downtime ). Otherwise you can [create the index]( https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html ) with [correct mappings]( https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html ) and import the data again. 

As german dates are not english once, its possible to specify the [date format]( https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html )
# Kibana
Open kibana in your webbrowser http://localhost:5601. Tell kibana to use the index *chef* and that you are not using the time-based-thing. 

If you click on *Discover* you get your chef/cooking data presented and can start to dig/configure around.

---
Created: 2016-02-11 
