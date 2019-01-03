# Highlight in Elastisearch
### 1. Creating a template 

We are creating a template called template_1 with the mappings(a tables) known as event and type1.

```
curl -X PUT \
  http://localhost:9200/_template/template_1/ \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: d6fe7974-f1b0-41b2-a390-969ad3a59692' \
  -H 'cache-control: no-cache' \
  -d '{
  "template": "highlig*",
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "type1": {
      "_source": {
        "enabled": true
      },
      "properties": {
        "host_name": {
          "type": "keyword"
        },
        "created_at": {
          "type": "date",
          "format": "EEE MMM dd HH:mm:ss Z YYYY"
        }
      }
    },
    "event":{
    	"properties":{
    	"title":{
    		"type":"text"
    	},
    	"description":{
    		"type":"text"
    	},
    	"explanation":{
    		"type":"text"
    	}
    	}
    }
    
  }
}'
```
### 2. Creating a new index called highlight

```
curl -X PUT \
  http://localhost:9200/highlight \
  -H 'Postman-Token: 41791895-82bf-49d2-a4af-edc082ef8a80' \
  -H 'cache-control: no-cache'
```

### 3. Now making a GET request to highlight index to return the template_1 mapping with its properties
```
curl -X GET \
  http://localhost:9200/highlight \
  -H 'Postman-Token: 148813ab-b234-4dd1-9e9d-f2dd3ab57c7e' \
  -H 'cache-control: no-cache'
```

The output will be:
```
{
    "highlight": {
        "aliases": {},
        "mappings": {
            "event": {
                "properties": {
                    "description": {
                        "type": "text"
                    },
                    "explanation": {
                        "type": "text"
                    },
                    "title": {
                        "type": "text"
                    }
                }
            },
            "type1": {
                "properties": {
                    "created_at": {
                        "type": "date",
                        "format": "EEE MMM dd HH:mm:ss Z YYYY"
                    },
                    "host_name": {
                        "type": "keyword"
                    }
                }
            }
        },
        "settings": {
            "index": {
                "creation_date": "1546503933162",
                "number_of_shards": "1",
                "number_of_replicas": "1",
                "uuid": "jMbw0yQhTgmTqdFNBWZZRw",
                "version": {
                    "created": "5020299"
                },
                "provided_name": "highlight"
            }
        }
    }
}
```

### 4. Now add data to the map(table) called event in highlight index using POST request

```
curl -X POST \
  http://localhost:9200/highlight/event \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 5c39378b-7b8b-477b-af05-0cfa212d1c86' \
  -H 'cache-control: no-cache' \
  -d '{
	"title":"Introduction to Elasticsearch",
	"description":"An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
	"explanation":"We are using elasticsearch"
}'


curl -X POST \
  http://localhost:9200/highlight/event \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: c4140fdc-f83b-458d-807d-445628cabade' \
  -H 'cache-control: no-cache' \
  -d '{
	"title":"3. Introduction to Elasticsearch",
	"description":"3. An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
	"explanation":"3.. We are using elasticsearch"
}'
```

### 5. Now making search request to event map in highlight index

```
curl -X GET \
  http://localhost:9200/highlight/event/_search \
  -H 'Postman-Token: d9f41e45-2216-4aef-a122-dde23f170fc3' \
  -H 'cache-control: no-cache'
```

The output will be:

```
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "failed": 0
    },
    "hits": {
        "total": 3,
        "max_score": 1,
        "hits": [
            {
                "_index": "highlight",
                "_type": "event",
                "_id": "AWgS2BB-u_8OQskN2XIK",
                "_score": 1,
                "_source": {
                    "title": "Introduction to Elasticsearch",
                    "description": "An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
                    "explanation": "We are using elasticsearch"
                }
            },
            {
                "_index": "highlight",
                "_type": "event",
                "_id": "AWgS2VD1u_8OQskN2XIL",
                "_score": 1,
                "_source": {
                    "title": "2. Introduction to Elasticsearch",
                    "description": "2. An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
                    "explanation": "2. We are using elasticsearch"
                }
            },
            {
                "_index": "highlight",
                "_type": "event",
                "_id": "AWgS2XRLu_8OQskN2XIM",
                "_score": 1,
                "_source": {
                    "title": "3. Introduction to Elasticsearch",
                    "description": "3. An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
                    "explanation": "3.. We are using elasticsearch"
                }
            }
        ]
    }
}
```

### 6. Now performing search highlight query operation for search query "elasticsearch"

```
curl -X GET \
  http://localhost:9200/highlight/event/_search \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 51f8f5b6-005c-46b0-8282-917dc1a78e12' \
  -H 'cache-control: no-cache' \
  -d '{
	"query":{
		"multi_match" : {
            "query" : "elasticsearch",
            "fields" : ["title", "description", "explanation"]
        }
	},
	"highlight": {
	"fields": {
		"title":{},
		"description":{},
		"explanation": {}
	 }
	},
	"_source": true 
	
}'
``` 
The output response for the highlight query operation is:
```
{
    "took": 3,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "failed": 0
    },
    "hits": {
        "total": 3,
        "max_score": 0.14181954,
        "hits": [
            {
                "_index": "highlight",
                "_type": "event",
                "_id": "AWgS2BB-u_8OQskN2XIK",
                "_score": 0.14181954,
                "_source": {
                    "title": "Introduction to Elasticsearch",
                    "description": "An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
                    "explanation": "We are using elasticsearch"
                },
                "highlight": {
                    "description": [
                        "An introduction to ES and each other. We can meet and greet and I will present on some <em>Elasticsearch</em> basics and how we use it."
                    ],
                    "title": [
                        "Introduction to <em>Elasticsearch</em>"
                    ],
                    "explanation": [
                        "We are using <em>elasticsearch</em>"
                    ]
                }
            },
            {
                "_index": "highlight",
                "_type": "event",
                "_id": "AWgS2VD1u_8OQskN2XIL",
                "_score": 0.12874341,
                "_source": {
                    "title": "2. Introduction to Elasticsearch",
                    "description": "2. An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
                    "explanation": "2. We are using elasticsearch"
                },
                "highlight": {
                    "description": [
                        "2. An introduction to ES and each other. We can meet and greet and I will present on some <em>Elasticsearch</em> basics and how we use it."
                    ],
                    "title": [
                        "2. Introduction to <em>Elasticsearch</em>"
                    ],
                    "explanation": [
                        "2. We are using <em>elasticsearch</em>"
                    ]
                }
            },
            {
                "_index": "highlight",
                "_type": "event",
                "_id": "AWgS2XRLu_8OQskN2XIM",
                "_score": 0.12874341,
                "_source": {
                    "title": "3. Introduction to Elasticsearch",
                    "description": "3. An introduction to ES and each other. We can meet and greet and I will present on some Elasticsearch basics and how we use it.",
                    "explanation": "3.. We are using elasticsearch"
                },
                "highlight": {
                    "description": [
                        "3. An introduction to ES and each other. We can meet and greet and I will present on some <em>Elasticsearch</em> basics and how we use it."
                    ],
                    "title": [
                        "3. Introduction to <em>Elasticsearch</em>"
                    ],
                    "explanation": [
                        "3.. We are using <em>elasticsearch</em>"
                    ]
                }
            }
        ]
    }
}
```

##### References:
1. https://dzone.com/articles/23-useful-elasticsearch-example-queries
2. https://manning-content.s3.amazonaws.com/download/5/2f40785-9c66-460e-adfa-a1f2ae19c23c/sample_AppC_Gheorghe_Elasticsearch_November12.pdf