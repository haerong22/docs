### Create Index & Document
```json
POST /my_product/_doc/1
{
  "name": "iphone15"
}

POST /my_product/_doc/2
{
  "name": "iphone16"
}

POST /my_product/_doc/3
{
  "name": "ipad pro"
}

POST /my_product/_doc/4
{
  "name": "ipad air"
}
```

### Prefix Search
- 단어의 시작이 포함되는 검색(중간에 일치하는 단어 검색 X)
- 대소문자 구분
- 성능 이슈가 있을 수 있음
```json
GET /my_product/_search
{
  "query": {
    "prefix": {
      "name": "ip"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_product",
        "_id": "1",
        "_score": 1,
        "_source": {
          "name": "iphone15"
        }
      },
      {
        "_index": "my_product",
        "_id": "2",
        "_score": 1,
        "_source": {
          "name": "iphone16"
        }
      },
      {
        "_index": "my_product",
        "_id": "3",
        "_score": 1,
        "_source": {
          "name": "ipad pro"
        }
      },
      {
        "_index": "my_product",
        "_id": "4",
        "_score": 1,
        "_source": {
          "name": "ipad air"
        }
      }
    ]
```

- 중간 단어 검색 불가
```json
GET /my_product/_search
{
  "query": {
    "prefix": {
      "name": "ph"
    }
  }
}
```
- 결과
```json
    "hits": []
```

### Wildcard Search
* * : 0개 이상의 문자 포함
* ? : 하나의 문자만 포함
* 와일드 카드로 시작하는 패턴 검색은 성능 이슈 있을 수 있음(e.g. `*phone`)
```json
GET /my_product/_search
{
  "query": {
    "wildcard": {
      "name": "iphone*"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_product",
        "_id": "1",
        "_score": 1,
        "_source": {
          "name": "iphone15"
        }
      },
      {
        "_index": "my_product",
        "_id": "2",
        "_score": 1,
        "_source": {
          "name": "iphone16"
        }
      }
    ]
```

### Regular Expression Search
- 정규 표현식 사용한 패턴 검색
- 성능 이슈가 있을 수 있음
```json
GET /my_product/_search
{
  "query": {
    "regexp": {
      "name": "iph.*"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_product",
        "_id": "1",
        "_score": 1,
        "_source": {
          "name": "iphone15"
        }
      },
      {
        "_index": "my_product",
        "_id": "2",
        "_score": 1,
        "_source": {
          "name": "iphone16"
        }
      }
    ]
```

```json
GET /my_product/_search
{
  "query": {
    "regexp": {
      "name": "iph.*[16]"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "my_product",
        "_id": "2",
        "_score": 1,
        "_source": {
          "name": "iphone16"
        }
      }
    ]
```