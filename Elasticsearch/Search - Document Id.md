### Create Index & Document
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

### Document Id 로 검색
- 생성된 document id 로 검색
```json
GET /my_index/_search
{
  "query": {
    "ids": {
      "values": ["1", "2"]
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