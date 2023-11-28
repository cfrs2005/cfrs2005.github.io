---
title: Enhancing MySQL to Elasticsearch Migration for Efficient Querying
date: 2023-11-28 08:00:00 +0800
categories: [Technology, Database]
tags: [Elasticsearch, MySQL Migration, Wildcard Queries, Performance Optimization]
img_path: '/assets/img/202311/'
image:
 path: 20231120esoptimize.png
 alt: Explore the synergies of using a Virtual Private Server (VPS) with SingBOX Notes in our guide.
---

When migrating MySQL services to Elasticsearch for query operations, utilizing wildcards in Elasticsearch can significantly improve fuzzy query performance. The introduction of the 'es7.9' parameter in Elasticsearch brings support for wildcard types, allowing for better query performance. Additionally, the use of 'ngram' for data segmentation and storage optimization can further enhance overall system performance.



## Build Index

```shell
{
  "warmsearch" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "aname" : {
          "type" : "wildcard"
        },
        "sn" : {
          "type" : "text"
        },
        "title" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword"
            }
          },
          "analyzer" : "my_analyzer"
        }
      }
    },
    "settings" : {
      "index" : {
        "max_ngram_diff" : "10",
        "routing" : {
          "allocation" : {
            "include" : {
              "_tier_preference" : "data_content"
            }
          }
        },
        "number_of_shards" : "1",
        "provided_name" : "warmsearch",
        "creation_date" : "1701145536507",
        "analysis" : {
          "analyzer" : {
            "my_analyzer" : {
              "tokenizer" : "my_tokenizer"
            }
          },
          "tokenizer" : {
            "my_tokenizer" : {
              "token_chars" : [
                "letter",
                "digit"
              ],
              "min_gram" : "1",
              "type" : "ngram",
              "max_gram" : "10"
            }
          }
        },
        "number_of_replicas" : "1",
        "queries" : {
          "cache" : {
            "enabled" : "false"
          }
        },
        "uuid" : "OG0QGTrxQiejej8Hj0T9eA",
        "version" : {
          "created" : "7100299"
        }
      }
    }
  }
}


```



## Put Data Insert

```python
import random

import requests
import uuid


def add_document_to_es(index, document):
    """向Elasticsearch索引中添加一个文档"""
    response = requests.post(f'http://localhost:9200/{index}/_doc/', json=document)
    return response.json()


# 索引名称
index_name = 'warmsearch'

for i in range(1, 500000):
    # 生成随机数据
    random_title = uuid.uuid4()
    random_sn = uuid.uuid4()
    aname = uuid.uuid4()
    random_number = random.randint(0,1)
    # 根据随机数输出扩展名
    if random_number == 0:
        filetype = ".log"
    else:
        filetype = ".bag"
    # 构建文档
    document = {
        "title": str(random_title),
        "sn": str(random_sn),
        "aname": str(aname) + filetype
    }

    print(document)
    # 添加文档到Elasticsearch
    response = add_document_to_es(index_name, document)
    print(response)

```

## Close ES Index

```shell
POST /warmsearch/_close
```

## Modify ES disable search with cache

```shell

PUT warmsearch/_settings
{
  "index.queries.cache.enabled": false
}


```

## Open Es Index

```shell
POST /warmsearch/_open
```



## Search  Bench Testing
```python
import requests
import time

# Elasticsearch服务器的URL
base_url = 'http://localhost:9200'

# 查询请求主体，分别为match、match_phrase和title.keyword查询

# keyName = "title"
# value = "asd"

keyName = "aname"
value = "a0"

queries = [
    {
        "query": {
            "match": {
                keyName: value
            }
        }
    },
    {
        "query": {
            "match": {
                keyName + ".keyword": value
            }
        }
    },
    {
        "query": {
            "match_phrase": {
                keyName: value
            }
        }
    },

    {
        "query": {
            "match_phrase": {
                keyName + ".keyword": value
            }
        }
    },
    {
        "query": {
            "wildcard": {
                keyName: "*" + value + "*"
            }
        }
    },
    {
        "query": {
            "wildcard": {
                keyName + ".keyword": "*" + value + "*"
            }
        }
    }
]

# 执行50次查询

for query in queries:
    # print(query)
    total_time = 0
    num_queries = 50
    for i in range(num_queries):
        start_time = time.time()
        # 发送查询请求
        response = requests.post(f'{base_url}/warmsearch/_search', json=query)

        end_time = time.time()
        elapsed_time = end_time - start_time

        total_time += elapsed_time

        if (i == (num_queries - 1)):
            response_json = response.json()
            hits = response_json.get('hits', {})
            total = hits.get("total", {})
            # print(total.get("value", {}))

        # 输出每次请求的访问耗时
        # print(f'Request {i + 1}: Elapsed Time = {elapsed_time:.4f} seconds')
        # 计算平均耗时
    average_time = total_time / num_queries
    print(f'Query  {query.get("query")}, | {total["value"]}| {average_time:.4f}')

```


## Result

1. 965658 Logs ，50 times avg ,close cache

| Query | Method | KeyName | Counts | Analysis | Avg Time (s) |
| --- | --- | --- | --- | --- | --- |
| {'match': {'title': '-ah'}}, | match | title | 10000 | Ngram | 0.0056 |
| {'match': {'title.keyword': '-ah'}} | match | title.keyword | 0 | Ngram | 0.0018 |
| {'match_phrase': {'title': '-ah'}} | match_phrase | title | 45 | Ngram | 0.0036 |
| {'match_phrase': {'title.keyword': '-ah'}} | match_phrase | title.keyword | 0 | Ngram | 0.0024 |
| {'wildcard': {'title': '*-ah*'}} | wildcard | title | 0 | Ngram | 0.8781 |
| {'wildcard': {'title.keyword': '*-ah*'}} | wildcard | title.keyword | 0 | Ngram | 0.0580 |
| {'match': {'sn': '5c'}} | match | sn | 0 | Default | 0.0105 |
|  {'match': {'sn.keyword': '5c'}} | match | sn.keyword | 0 | Default | 0.0048 |
| {'match_phrase': {'sn': '5c'}} | match_phrase | sn | 0 | Default | 0.0068 |
| {'match_phrase': {'sn.keyword': '5c'}} | match_phrase | sn.keyword | 0 | Default | 0.0054 |
| {'wildcard': {'sn': '*5c*'}} | wildcard | sn | 10000 | Default | 0.1659 |
|  {'wildcard': {'sn.keyword': '*5c*'}} | wildcard | sn.keyword | 0 | Default | 0.0062 |
| {'match': {'aname': 'a0'}} | match | aname | 0 | wildcard | 0.0138 |
| {'match': {'aname.keyword': 'a0'}} | match | aname.keyword | 0 | wildcard | 0.0065 |
| {'match_phrase': {'aname': 'a0'}} | match_phrase | aname | 0 | wildcard | 0.0061 |
| {'match_phrase': {'aname.keyword': 'a0'}} | match_phrase | aname.keyword | 0 | wildcard | 0.0048 |
| {'wildcard': {'aname': '*a0*'}} | wildcard | aname | 1000 | wildcard | 0.0380 |
| {'wildcard': {'aname.keyword': '*a0*'}} | wildcard | aname.keyword | 0 | wildcard | 0.0086 |




