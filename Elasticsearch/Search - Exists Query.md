- 필드가 없을경우 -> `false`
- Null Value -> `false`
- Empty Arrays -> `false`
### Create Index & Document
```json
POST /my_product/_doc
{
  "name": "iphone15"
}

POST /my_product/_doc
{
  "name": "iphone15",
  "publish_date": "2024-01-01"
}

POST /my_product/_doc
{
  "name": "iphone15",
  "publish_date": null
}

POST /my_product/_doc
{
  "name": "iphone15",
  "publish_date": null,
  "tag": []
}

POST /my_product/_doc
{
  "name": "iphone15",
  "publish_date": null,
  "tag": ["moblie"]
}
```

### Exists Query
- `name` 필드 존재
```json
GET /my_product/_search
{
  "query": {
    "exists": {
      "field": "name"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_product",
        "_id": "DN3hHpIBwyeEKBBzCj_F",
        "_score": 1,
        "_source": {
          "name": "iphone15"
        }
      },
      {
        "_index": "my_product",
        "_id": "Dd3hHpIBwyeEKBBzDz93",
        "_score": 1,
        "_source": {
          "name": "iphone15",
          "publish_date": "2024-01-01"
        }
      },
      {
        "_index": "my_product",
        "_id": "Dt3hHpIBwyeEKBBzFT_E",
        "_score": 1,
        "_source": {
          "name": "iphone15",
          "publish_date": null
        }
      },
      {
        "_index": "my_product",
        "_id": "D93hHpIBwyeEKBBzGz8l",
        "_score": 1,
        "_source": {
          "name": "iphone15",
          "publish_date": null,
          "tag": []
        }
      },
      {
        "_index": "my_product",
        "_id": "EN3hHpIBwyeEKBBzID85",
        "_score": 1,
        "_source": {
          "name": "iphone15",
          "publish_date": null,
          "tag": [
            "moblie"
          ]
        }
      }
    ]
```

- `publish_date` 필드 존재
```json
GET /my_product/_search
{
  "query": {
    "exists": {
      "field": "publish_date"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_product",
        "_id": "Dd3hHpIBwyeEKBBzDz93",
        "_score": 1,
        "_source": {
          "name": "iphone15",
          "publish_date": "2024-01-01"
        }
      }
    ]
```

`tag` 필드 존재
```json
GET /my_product/_search
{
  "query": {
    "exists": {
      "field": "tag"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_product",
        "_id": "EN3hHpIBwyeEKBBzID85",
        "_score": 1,
        "_source": {
          "name": "iphone15",
          "publish_date": null,
          "tag": [
            "moblie"
          ]
        }
      }
    ]
```

`bool query` 안에 사용
```json
GET /my_product/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "exists": {
            "field": "publish_date"
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
        "_index": "my_product",
        "_id": "Dd3hHpIBwyeEKBBzDz93",
        "_score": 1,
        "_source": {
          "name": "iphone15",
          "publish_date": "2024-01-01"
        }
      }
    ]
```