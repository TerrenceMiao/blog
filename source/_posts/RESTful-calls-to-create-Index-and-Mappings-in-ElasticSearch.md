---
title: RESTful calls to create Index and Mappings in ElasticSearch
date: 2018-03-10 10:58:36
tags:
---

There is a simple way, using RESTful client to create Index and Mappings in ElasticSearch, for example, with ElasticSearch Head plugin.

Create Index in ElasticSearch with Settings JSON file. URL: http://localhost:9200/orders/, Method: **PUT**

```bash
{
  "settings": {
    "index": {
      "number_of_shards": 1,
      "number_of_replicas": 0,
      "analysis": {
        "filter": {
          "partial_matching_filter": {
            "type": "edge_ngram",
            "min_gram": 1,
            "max_gram": 40
          }
        },
        "analyzer": {
          "partial_matcher": {
            "type": "custom",
            "tokenizer": "standard",
            "filter": [
              "lowercase",
              "partial_matching_filter"
            ]
          }
        }
      }
    }
  }
}
```

Create Mappings in ElasticSearch with Mappings JSON file. URL: http://localhost:9200/orders/_mapping/order, Method: **PUT**

```bash
{
  "dynamic": "false",
  "_all": { "enabled": false},
  "properties": {
    "customer_id": {
      "type": "keyword"
    },
    "encryption_key_id": {
      "type": "keyword",
      "index": "false"
    },
    "metadata_tags": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "category": {
      "type": "keyword"
    },
    "order_creation_date": {
      "type": "date",
      "format": "date_optional_time"
    },
    "order_reference": {
      "type": "text"
    },
    "order_id": {
      "type": "keyword"
    },
    "to_name": {
      "type": "text",
      "analyzer": "partial_matcher",
      "search_analyzer": "standard"
    },
    "to_suburb": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "to_state": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "to_postcode": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "to_email": {
      "type": "text",
      "analyzer": "whitespace"
    }
  }
}
```

To add a new fields to ElasticSearch, while dynamic mapping is off:

```bash
{
  "dynamic": "false"
}
```

Run with additional fields JSON file, with URL: http://localhost:9200/orders/_mapping/order, Method: **PUT**

```bash
{
  "properties": {
    "from_name": {
      "type": "text",
      "analyzer": "partial_matcher",
      "search_analyzer": "standard"
    },
    "from_business_name": {
      "type": "text",
      "analyzer": "partial_matcher",
      "search_analyzer": "standard"
    },
    "from_suburb": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "from_state": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "from_postcode": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "from_country": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "from_email": {
      "type": "text",
      "analyzer": "whitespace"
    },
    "from_phone": {
      "type": "text",
      "analyzer": "whitespace"
    }
  }
}
```

An ElasticSearch query example, with URL: http://localhost:9200/orders/_search, Method: **POST**

```bash
{
 "query": {
   "bool": {
     "must": [
       {"match": {"customer_id": "1234567890"} },
       {"simple_query_string": {
            "query": "unlabelled+unmanifested",
            "fields": ["metadata_tags"]
       } },
       {
         "bool": {
           "should": [
             {
               "multi_match": {
                 "query": "TerrenceMiao",
                 "fields": [
                   "name", "business_name", "order_id", "shipment_id", "sender_references", "article_ids"
                 ]
               }
             },
             {"match": {"phone": "abracadbravvv5/vv70qBu+/ve+/ve6EuA7vv70g77+9Su+/ve+/vX5y77+95JuV"} },
             {
               "multi_match": {
                 "query": "gdgdggddvvv75/vv70qBu+/ve+/ve6EuA7vv70g77+9Su+/ve+/vX5y77+95JuV",
                 "fields": [
                   "suburb", "state", "country", "postcode", "email"
                 ]
               }
             }
           ]
         }
       },
       {"simple_query_string": {
         "query": "DESPATCH",
         "fields": ["movement_type"]
       } }
     ]
   }
 },
 "sort": [
   {"order_creation_date": {"order": "desc", "unmapped_type": "date"} },
   {"shipment_creation_date": {"order": "desc"} }
 ],
 "from": "0","size": "1"
}
```

An example query with date/time type in a range:

```bash
{
  "query": {
    "range": {
      "shipment_creation_date": {
        "gte": "2018-04-19T15:30:00",
        "lte": "now",
        "time_zone": "+10:00"

      }
    }
  },
  "from": 0,
  "size": 10,
  "sort": [],
  "aggs": {}
}
```
