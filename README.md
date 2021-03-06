# ElasticSearch

# Start Command
start elasticsrach.bat -Enode.name=dash -Ecluster.name=awesome

# UI
localhost:9200
<br/>
localhost:9200/_cat/health?v
<br/>
localhost:9200/_cat/nodes?n

# Curl Commands
curl -XGET 'localhost:9200/_cat/nodes?n'
## To Create Index
curl -XPUT 'localhost:9200/products?&pretty'

## Check the indices
curl -XGET 'localhost:9200/_cat/indices?v'

## Creating documents in the index
curl -XPUT 'localhost:9200/products/mobiles/1?pretty' -d'
{
"name":"iphone7",
"camera": "12MP",
"storage": "256GB",
"display": "4.7inch",
"battery": "1,960mAh",
"reviews": ["Incredibly happy after using one week", "Best Iphone so far", "Expensive", "Stick to android"]
}
' -H "Content-Type: application/json"

## Retrieving Documents
curl -XGET 'localhost:9200/products/mobiles/1?pretty'

### Retireiving documents without content
curl -XGET 'localhost:9200/products/mobiles/1?pretty&_source=false'

### specific source
curl -XGET 'localhost:9200/products/mobiles/1?pretty&_source=name,reviews'

## To update a part of the document - use POST with _update

$ curl -XPOST 'localhost:9200/products/mobiles/2/_update?pretty' -d'
{
"doc": {
"dolor": "black"
}
}
' -H "Content-Type: ^C

### To increment value of the year by 2

curl -XPOST 'localhost:9200/products/mobiles/2/_update?pretty' -d'
> {
> "script": "ctx._source.year +=2"
> }
> ' -H "Content-Type: application/json"

## Delete a document

curl -XDELETE 'localhost:9200/products/mobiles/1?pretty'

### Deleting index

curl -XDELETE 'localhost:9200/customers?pretty'

## Bulk Operation 

curl -XPOST 'localhost:9200/_bulk?pretty' -d'
{ "index" : { "_index" : "products", "_type" : "mobiles", "_id" : "4" }}
{ "name": "XIOMiXIOMI", "camera": "10px", "storage": "22px" }
{ "index" : { "_index" : "products", "_type" : "mobiles", "_id" : "6" }}
{ "name": "dasAwesomeXiomi", "camera": "10px", "storage": "22px" }
' -H "Content-Type: application/json"

### Bulk opertaion with content in file

curl -H "Content-Type: application/x-ndjson" -XPOST 'localhost:9200/customers/personal/_bulk?pretty&refresh' --data-binary @"customers_full.json"

## Search

curl -XGET 'localhost:9200/customers/_search?q=24&pretty'

### search with sort
http://localhost:9200/customers/_search?q=female&sort=age&pretty

### asc & desc
http://localhost:9200/customers/_search?q=female&sort=age:desc&pretty 

### Explain Search
http://localhost:9200/customers/_search?q=female&explain=true&pretty

### Search All
curl -XGET 'localhost:9200/products/_search?pretty' -d'
{
"query": { "match_all": {} }
}
' -H "Content-type: application/json"

### Search with size
curl -XGET 'localhost:9200/products/_search?pretty' -d'
{
"query": { "match_all": {} }, "size": 3
}
' -H "Content-type: application/json"

### Search multiple indices
curl -XGET 'localhost:9200/products,customers/_search?pretty' -d'
{
"query": { "match_all": {} }, "size": 200
}
' -H "Content-type: application/json"

### Search with relevant terms
curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": { "term": {"name": "kim"} }
}
' -H "Content-type: application/json"

### _Source field to determine what fields we require

 curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"_source": "st*",
"query": {"term" :{"street": "street"}}
}
' -H "Content-Type: application/json"

### _source field with Includes/ excludes

curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"_source": {"includes": ["st*", "*n*"], "excludes": ["*der"]},
"query": {"term" :{"street": "street"}}
}
' -H "Content-Type: application/json"

## Match, Match_all

### match - seaches frnak or norris

curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": {
"match": {
"name" : {
"query" : "frank norris",
"operator" : "or"
}
}
}
}
' -H "Content-Type: application/json"

### match phrase - complete phrase is matched
curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": {
"match_phrase": {
"name" : {
"query" : "Lara baird"
}
}
}
}
' -H "Content-Type: application/json"

### match phrase prefix - like operator

curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": {
"match_phrase_prefix": {
"name" : {
"query" : "er"
}
}
}
}
' -H "Content-Type: application/json"

## Boolean Query

#### must

curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": {
"bool": {
"must": [
{"match": {"street": "randolph"}},
{"match": {"street": "346"}}
]
}
}
}
' -H "Content-Type: application/json"
#### should

curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": {
"bool": {
"should": [
{"match": {"street": "randolph"}},
{"match": {"street": "Street"}}
]
}
}
}
' -H "Content-Type: application/json"
#### must_not

curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": {
"bool": {
"must_not": [
{"match": {"street": "randolph"}},
{"match": {"street": "Street"}}
]
}
}
}
' -H "Content-Type: application/json"

#### filter

curl -XGET 'localhost:9200/customers/_search?pretty' -d'
{
"query": {
"bool": {
"must":{ "match_all": {} },
"filter": {
"range": {
"age": {
"gte": 20,
"lte": 30
}
}
}
}
}
}
' -H "Content-Type: application/json"

## Metric

#### avg
curl -XPOST 'localhost:9200/customers/_search?&pretty' -d'
{
"size": 0,
"aggs": {
"avg_age": {
"avg": {
"field": "age"
}
}
}
}
' -H "Content-Type: application/json"

#### stats : gives all the values (min / max/sum etc)
curl -XPOST 'localhost:9200/customers/_search?&pretty' -d'
{
"size": 0,
"aggs": {
"age_stats": {
"stats": {
"field": "age"
}
}
}
}
' -H "Content-Type: application/json"

#### cardinality - The number of unique values of the field specified




