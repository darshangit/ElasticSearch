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

