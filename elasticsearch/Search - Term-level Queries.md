- 정확히 매칭 검색
- `number`, `date`, `keywords` 데이터에 사용
- 대소문자 구분

### Create index & document
```json
POST /my_index/_doc/1
{
  "content": "Quick book",
  "publish_date": "2024-03-01",
  "status": "published"
}

POST /my_index/_doc/2
{
  "content": "Slow book",
  "publish_date": "2024-04-01",
  "status": "draft"
}
```

### Term-level Queries
```json
GET /my_index/_search
{
  "query": {
    "term": {
      "status": "published"
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
        "_score": 0.6931471,
        "_source": {
          "content": "Quick book",
          "publish_date": "2024-03-01",
          "status": "published"
        }
      }
    ]
```

- 대소문자 구분
```json
GET /my_index/_search
{
  "query": {
    "term": {
      "status": "Published"
    }
  }
}
```
- 결과
```json
    "hits": []
```

- terms
```json
GET /my_index/_search
{
  "query": {
    "terms": {
      "status": ["published", "draft"]
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
          "content": "Quick book",
          "publish_date": "2024-03-01",
          "status": "published"
        }
      },
      {
        "_index": "my_index",
        "_id": "2",
        "_score": 1,
        "_source": {
          "content": "Slow book",
          "publish_date": "2024-04-01",
          "status": "draft"
        }
      }
    ]
```

- `content` 에 `Quick` 포함
- `text` type 은 `tokenizer`, `analyzer` 사용으로 소문자로 저장하기 때문에 검색 X
```json
GET /my_index/_search
{
  "query": {
    "term": {
      "content": "Quick"
    }
  }
}
```
- 결과
```json
    "hits": []
```

- 대소문자 구분X 
```json
GET /my_index/_search
{
  "query": {
    "term": {
      "content": {
        "value": "Quick",
        "case_insensitive": true
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
          "content": "Quick book",
          "publish_date": "2024-03-01",
          "status": "published"
        }
      }
    ]
```
