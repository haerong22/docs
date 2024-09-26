
### Create Index & Document
```json
PUT /products
{
  "mappings": {
    "properties": {
      "name": {
        "type": "text"
      },
      "category": {
        "type": "keyword"
      },
      "price": {
        "type": "float"
      },
      "in_stock": {
        "type": "boolean"
      },
      "discontinued": {
        "type": "boolean"
      }
    }
  }
}

POST /products/_doc/1
{
  "name": "Smartphone XYZ",
  "category": "electronics",
  "price": 299.99,
  "in_stock": true,
  "discontinued": false
}

POST /products/_doc/2
{
  "name": "Laptop ABC",
  "category": "electronics",
  "price": 999.99,
  "in_stock": false,
  "discontinued": false
}

POST /products/_doc/3
{
  "name": "Wireless Mouse",
  "category": "electronics",
  "price": 25.99,
  "in_stock": true,
  "discontinued": false
}

POST /products/_doc/4
{
  "name": "Office Chair",
  "category": "furniture",
  "price": 149.99,
  "in_stock": true,
  "discontinued": false
}

POST /products/_doc/5
{
  "name": "Bluetooth Speaker",
  "category": "electronics",
  "price": 59.99,
  "in_stock": true,
  "discontinued": true
}

POST /products/_doc/6
{
  "name": "Gaming Console",
  "category": "electronics",
  "price": 299.99,
  "in_stock": false,
  "discontinued": false
}

POST /products/_doc/7
{
  "name": "Dining Table",
  "category": "furniture",
  "price": 399.99,
  "in_stock": true,
  "discontinued": false
}

POST /products/_doc/8
{
  "name": "Electric Kettle",
  "category": "kitchen",
  "price": 45.99,
  "in_stock": true,
  "discontinued": false
}

```

### Leaf Query
- 하나의 컨디션만 있는 쿼리
```json
GET /my_index/_search
{
  "query": {
    "term": {
      "status": "published"
    }
  }
}

GET /my_index/_search
{
  "query": {
    "range": {
      "publish_date": {
        "gte": "2023-01-01",
        "lte": "2023-12-31"
      }
    }
  }
}

GET /my_index/_search
{
  "query": {
    "match": {
      "content": "Elasticsearch"
    }
  }
}
```

### Compound Query
- 여러 쿼리가 합쳐져 있는 쿼리
- `bool` query: `must`, `must_not`, `should`, `filter`

- `SELECT * FROM products WHERE category = 'electronics' AND in_stock = true;`
```json
GET /products/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "category": "electronics" } },
        { "term": { "in_stock": true } }
      ]
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "products",
        "_id": "1",
        "_score": 0.81789887,
        "_source": {
          "name": "Smartphone XYZ",
          "category": "electronics",
          "price": 299.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "3",
        "_score": 0.81789887,
        "_source": {
          "name": "Wireless Mouse",
          "category": "electronics",
          "price": 25.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "5",
        "_score": 0.81789887,
        "_source": {
          "name": "Bluetooth Speaker",
          "category": "electronics",
          "price": 59.99,
          "in_stock": true,
          "discontinued": true
        }
      }
    ]
```

- `SELECT * FROM products WHERE category = 'electronics' OR price < 300;`
```json
GET /products/_search
{
  "query": {
    "bool": {
      "should": [
        { "term": { "category": "electronics" } },
        { "range": { "price": { "lt": 300 } } }
      ],
      "minimum_should_match": 1
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "products",
        "_id": "1",
        "_score": 1.4924765,
        "_source": {
          "name": "Smartphone XYZ",
          "category": "electronics",
          "price": 299.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "3",
        "_score": 1.4924765,
        "_source": {
          "name": "Wireless Mouse",
          "category": "electronics",
          "price": 25.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "5",
        "_score": 1.4924765,
        "_source": {
          "name": "Bluetooth Speaker",
          "category": "electronics",
          "price": 59.99,
          "in_stock": true,
          "discontinued": true
        }
      },
      {
        "_index": "products",
        "_id": "6",
        "_score": 1.4924765,
        "_source": {
          "name": "Gaming Console",
          "category": "electronics",
          "price": 299.99,
          "in_stock": false,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "4",
        "_score": 1,
        "_source": {
          "name": "Office Chair",
          "category": "furniture",
          "price": 149.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "8",
        "_score": 1,
        "_source": {
          "name": "Electric Kettle",
          "category": "kitchen",
          "price": 45.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "2",
        "_score": 0.49247646,
        "_source": {
          "name": "Laptop ABC",
          "category": "electronics",
          "price": 999.99,
          "in_stock": false,
          "discontinued": false
        }
      }
    ]
```

- `SELECT * FROM products WHERE category = 'electronics' AND (price < 300 OR in_stock = true) AND NOT discontinued = true;`
```json
GET /products/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "category": "electronics" } },
        {
          "bool": {
            "should": [
              { "range": { "price": { "lt": 300 } } },
              { "term": { "in_stock": true } }
            ]
          }
        }
      ],
      "must_not": [
        { "term": { "discontinued": true } }
      ]
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "products",
        "_id": "1",
        "_score": 1.8178989,
        "_source": {
          "name": "Smartphone XYZ",
          "category": "electronics",
          "price": 299.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "3",
        "_score": 1.8178989,
        "_source": {
          "name": "Wireless Mouse",
          "category": "electronics",
          "price": 25.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "6",
        "_score": 1.4924765,
        "_source": {
          "name": "Gaming Console",
          "category": "electronics",
          "price": 299.99,
          "in_stock": false,
          "discontinued": false
        }
      }
    ]
```

- `SELECT * FROM products WHERE category = 'electronics' AND price < 300 AND in_stock = true;`
```json
GET /products/_search
{
  "query": {
    "bool": {
      "must": [
        { "term": { "category": "electronics" } }
      ],
      "filter": [
        { "range": { "price": { "lt": 300 } } },
        { "term": { "in_stock": true } }
      ]
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "products",
        "_id": "1",
        "_score": 0.49247646,
        "_source": {
          "name": "Smartphone XYZ",
          "category": "electronics",
          "price": 299.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "3",
        "_score": 0.49247646,
        "_source": {
          "name": "Wireless Mouse",
          "category": "electronics",
          "price": 25.99,
          "in_stock": true,
          "discontinued": false
        }
      },
      {
        "_index": "products",
        "_id": "5",
        "_score": 0.49247646,
        "_source": {
          "name": "Bluetooth Speaker",
          "category": "electronics",
          "price": 59.99,
          "in_stock": true,
          "discontinued": true
        }
      }
    ]
```

- `SELECT * FROM producs WHERE category = 'electronics' AND in_stock = true;`
```json

```
- 결과
```json

```

- `SELECT * FROM producs WHERE category = 'electronics' AND in_stock = true;`
```json

```
- 결과
```json

```