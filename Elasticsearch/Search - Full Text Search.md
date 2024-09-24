- Text Analysis : `Tokenization`, `Lowercasing`, `Stemming`, `Stop word Removal`
- `score` 가 높을수록 검색에 가까움
### Create Index & Document
```json
PUT /articles
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text"
      },
      "content": {
        "type": "text"
      },
      "summary": {
        "type": "text"
      }
    }
  }
}

POST /articles/_doc/1
{
  "title": "Introduction to Machine Learning",
  "content": "Machine learning is a field of artificial intelligence that uses statistical techniques to give computers the ability to learn from data. Neural networks are a key component of many machine learning algorithms.",
  "summary": "An introduction to machine learning and neural networks."
}

POST /articles/_doc/2
{
  "title": "Deep Learning with Neural Networks",
  "content": "Deep learning is a subset of machine learning that involves training large neural networks on vast amounts of data. It is used in applications like image and speech recognition.",
  "summary": "A deep dive into deep learning and its applications."
}

POST /articles/_doc/3
{
  "title": "Understanding Artificial Intelligence",
  "content": "Artificial intelligence encompasses machine learning, deep learning, and other techniques that enable machines to mimic human intelligence. Neural networks play a crucial role in AI development.",
  "summary": "A comprehensive guide to artificial intelligence."
}

POST /articles/_doc/4
{
  "title": "Getting Started with Data Science",
  "content": "Data science involves extracting insights from large datasets using various techniques, including machine learning. However, neural networks are not typically a primary focus in introductory data science.",
  "summary": "An overview of data science and its key concepts."
}


```

### Full Text Search
- `content` 필드에 `learning` 포함 된 데이터 검색 대/소문자 구분 X
```json
GET /articles/_search
{
  "query": {
    "match": {
      "content": "learning"
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "articles",
        "_id": "3",
        "_score": 0.14853522,
        "_source": {
          "title": "Understanding Artificial Intelligence",
          "content": "Artificial intelligence encompasses machine learning, deep learning, and other techniques that enable machines to mimic human intelligence. Neural networks play a crucial role in AI development.",
          "summary": "A comprehensive guide to artificial intelligence."
        }
      },
      {
        "_index": "articles",
        "_id": "2",
        "_score": 0.1441594,
        "_source": {
          "title": "Deep Learning with Neural Networks",
          "content": "Deep learning is a subset of machine learning that involves training large neural networks on vast amounts of data. It is used in applications like image and speech recognition.",
          "summary": "A deep dive into deep learning and its applications."
        }
      },
      {
        "_index": "articles",
        "_id": "1",
        "_score": 0.14003402,
        "_source": {
          "title": "Introduction to Machine Learning",
          "content": "Machine learning is a field of artificial intelligence that uses statistical techniques to give computers the ability to learn from data. Neural networks are a key component of many machine learning algorithms.",
          "summary": "An introduction to machine learning and neural networks."
        }
      },
      {
        "_index": "articles",
        "_id": "4",
        "_score": 0.107678965,
        "_source": {
          "title": "Getting Started with Data Science",
          "content": "Data science involves extracting insights from large datasets using various techniques, including machine learning. However, neural networks are not typically a primary focus in introductory data science.",
          "summary": "An overview of data science and its key concepts."
        }
      }
    ]
```

- `content` 필드에 `Data` or `Science` 포함 된 데이터 검색 대/소문자 구분 X
```json
GET /articles/_search
{
  "query": {
    "match": {
      "content": {
        "query": "Data Science",
        "operator": "or"
      }
    }
  }
}
```
- 결과
```json
    "hits": [
      {
        "_index": "articles",
        "_id": "4",
        "_score": 2.178133,
        "_source": {
          "title": "Getting Started with Data Science",
          "content": "Data science involves extracting insights from large datasets using various techniques, including machine learning. However, neural networks are not typically a primary focus in introductory data science.",
          "summary": "An overview of data science and its key concepts."
        }
      },
      {
        "_index": "articles",
        "_id": "2",
        "_score": 0.35413334,
        "_source": {
          "title": "Deep Learning with Neural Networks",
          "content": "Deep learning is a subset of machine learning that involves training large neural networks on vast amounts of data. It is used in applications like image and speech recognition.",
          "summary": "A deep dive into deep learning and its applications."
        }
      },
      {
        "_index": "articles",
        "_id": "1",
        "_score": 0.33961305,
        "_source": {
          "title": "Introduction to Machine Learning",
          "content": "Machine learning is a field of artificial intelligence that uses statistical techniques to give computers the ability to learn from data. Neural networks are a key component of many machine learning algorithms.",
          "summary": "An introduction to machine learning and neural networks."
        }
      }
    ]
```

- `content` 필드에 `Data` and `Science`  포함 된 데이터 검색 대/소문자 구분 X
```json
GET /articles/_search
{
  "query": {
    "match": {
      "content": {
        "query": "Data Science",
        "operator": "and"
      }
    }
  }
}

```
- 결과
```json
    "hits": [
      {
        "_index": "articles",
        "_id": "4",
        "_score": 2.178133,
        "_source": {
          "title": "Getting Started with Data Science",
          "content": "Data science involves extracting insights from large datasets using various techniques, including machine learning. However, neural networks are not typically a primary focus in introductory data science.",
          "summary": "An overview of data science and its key concepts."
        }
      }
    ]
```
