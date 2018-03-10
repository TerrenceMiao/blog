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
