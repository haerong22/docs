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

### Multi Match Query
- 여러 필드에 포함되는지 검색
- 스코어가 높은 순 조회
```json
GET /articles/_search
{
  "query": {
    "multi_match": {
      "query": "Data Science",
      "fields": ["title", "content", "summary"]
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
        "_score": 2.2908022,
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

- 특정 필드에 스코어 가중치
```json
GET /articles/_search
{
  "query": {
    "multi_match": {
      "query": "Data Science",
      "fields": ["title^2", "content", "summary"]
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
        "_score": 4.491629,
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

- 같은 경우 점수 가중치
```json
GET /articles/_search
{
  "query": {
    "multi_match": {
      "query": "Data Science",
      "fields": ["title^2", "content", "summary"],
      "tie_breaker": 0.3
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
        "_score": 5.8323097,
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