### Create Index & Document
```json
POST /my_index/_doc/1
{
  "content": "quick book",
  "publish_date": "2024-03-01"
}

POST /my_index/_doc/2
{
  "content": "quick walk",
  "publish_date": "2024-04-01"
}
```

### URI Search
#### `content` 필드 `quick` 검색
```json
GET /my_index/_search?q=content:quick
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "1",
        "_score": 0.18232156,
        "_source": {
          "content": "quick book",
          "publish_date": "2024-03-01"
        }
      },
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 0.18232156,
        "_source": {
          "content": "quick walk",
          "publish_date": "2024-04-01"
        }
      }
    ]
```

#### `content` 필드 `quick` 과 `walk` 포함 검색
```json
GET /my_index/_search?q=content:quick AND content:walk
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 0.8754687,
        "_source": {
          "content": "quick walk",
          "publish_date": "2024-04-01"
        }
      }
    ]
```

### DSL(Domain Specific Language) Queries
#### Basic Search
-  `content` 필드 `quick` 검색
```json
GET /my_index/_search
{
  "query": {
    "match": {
      "content": "quick"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "1",
        "_score": 0.18232156,
        "_source": {
          "content": "quick book",
          "publish_date": "2024-03-01"
        }
      },
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 0.18232156,
        "_source": {
          "content": "quick walk",
          "publish_date": "2024-04-01"
        }
      }
    ]
```

#### Full-Text Search with Query String
-  모든 필드(또는 특정 필드) `quick` 검색
```json
GET /my_index/_search
{
  "query": {
    "query_string": {
      "query": "quick"
    }
  }
}

GET /my_index/_search
{
  "query": {
    "query_string": {
      "fields": ["content"], 
      "query": "quick"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "1",
        "_score": 0.18232156,
        "_source": {
          "content": "quick book",
          "publish_date": "2024-03-01"
        }
      },
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 0.18232156,
        "_source": {
          "content": "quick walk",
          "publish_date": "2024-04-01"
        }
      }
    ]
```

#### Boolean Queries
- `must`, `should`, `must_not` 키워드 사용
-  `content` 필드 `quick`, `walk` 모두 포함 검색
```json
GET /my_index/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": { "content": "quick" } },
        {"match": { "content": "walk" } }
      ]
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 0.8754687,
        "_source": {
          "content": "quick walk",
          "publish_date": "2024-04-01"
        }
      }
    ]
```

#### Filtering Results
- score 관계 없이 필터링
-  `content` 필드 `quick` 검색
```json
GET /my_index/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "content": "quick"
          }
        }
      ]
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "1",
        "_score": 0,
        "_source": {
          "content": "quick book",
          "publish_date": "2024-03-01"
        }
      },
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 0,
        "_source": {
          "content": "quick walk",
          "publish_date": "2024-04-01"
        }
      }
    ]
```

#### Range Queries
-  `publish_date` 필드 `2024-03-01`, `2024-03-10` 범위 검색
```json
GET /my_index/_search
{
  "query": {
    "range": {
      "publish_date": {
        "gte": "2024-03-01",
        "lte": "2024-03-10"
      }
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "1",
        "_score": 1,
        "_source": {
          "content": "quick book",
          "publish_date": "2024-03-01"
        }
      }
    ]
```

### Phrase Search
- 단어 순서 일치 검색
-  `content` 필드 `book`, `quick` 순서 검색
```json
GET /my_index/_search
{
  "query": {
    "match_phrase": {
      "content": "book quick"
    }
  }
}
```
- 결과
```json
    "hits": []
```

#### Wildcard Search
-  * : 모든 문자, ? : 단일 문자
-  `content` 필드 `qui` 포함된 모든 검색
```json
GET /my_index/_search
{
  "query": {
    "wildcard": {
      "content": "qui*"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_index",
        "_id": "1",
        "_score": 1,
        "_source": {
          "content": "quick book",
          "publish_date": "2024-03-01"
        }
      },
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 1,
        "_source": {
          "content": "quick walk",
          "publish_date": "2024-04-01"
        }
      }
    ]
```
-  `content` 필드 `qui` + 문자하나 검색
```json
GET /my_index/_search
{
  "query": {
    "wildcard": {
      "content": "qui?"
    }
  }
}
```
- 결과
```json
    "hits": []
```

### 전체 document 조회
```json
GET /my_index/_search
{
  "query": {
    "match_all": {}
  }
}
```