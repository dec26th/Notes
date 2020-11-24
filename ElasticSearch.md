[TOC]

# Elastic Search学习

## RDS && ES

| 关系型数据库 | ElasticSearch     |
| ------------ | ----------------- |
| DataBase     | Index             |
| Table        | Type              |
| Row          | Document(Json 串) |
| Column       | Fidld             |
| Schema       | Mapping           |



## 各个查询的区别

### match && term

- **match**

```json
{
  "query": {
    "match": {
      "content": {
        "query": "查一查看一看"
      }
    }
  }
}
```

会对查询的内容进行分词，然后根据lucene的评分机制来进行评分



- **multi_match**

  - 在多个字段进行匹配

  - ```json
    {
      "query": {
        "multi_match": {
            "query" : "我的宝马多少马力",
            "fields" : ["title", "content"]
        }
      }
    }
    ```

  - 同时在title， content来进行内容的匹配

- **term完全匹配**

### bool 联合查询:must should, must_not

```json
{
	"query": {
    	"bool": {
        	"must": {
            	"term": {
                "content": "baoma"
              }
          },
        "must_not": {
          	"term": {
              	"tags": "baoma"
            }
        }
      }
  }
}
```



## 创建索引

```json
PUT /{index_name}
{
  "settings": {}, // es settings
  "mappings": {
    "field_one": {"type": ""},
    "field_two": {"type": ""},
    ...
  }
}
```