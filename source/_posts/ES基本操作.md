---
title: ES基本操作
date: 2020-07-11 11:11:47
tags: ES
top: 0
---

# Elasticsearch基本操作

## 一、简介

**Elasticsearch（ES）：**一款基于[Apache Lucene(TM)](https://lucene.apache.org/core/)的开源的全文检索和分析引擎。通过简单的`RESTful API`来隐藏其复杂性、同时也做了分布式相关的工作。

**Lucene：**使用Java实现的一套搜索引擎库。

<!--more-->

- 分布式的实时文件存储，每个字段都被索引并可被搜索
- 分布式的实时分析搜索引擎
- 可以扩展到上百台服务器，处理PB级结构化或非结构化数据

#### ES与关系型数据库对比

Elasticsearch集群可以包含多个索引（数据库），每一个索引可以包含多个类型（表），每一个类型包含多个文档（行），然后每个文档包含多个字段（列）

| **关系型数据库** | **数据库**    | **表**     | **行** | **列** |
| ---------------- | ------------- | ---------- | ------ | ------ |
| ElasticSearch    | 索引（index） | 类型(type) | 文档   | 字段   |

**相关概念：** 

1. 集群（cluster）：一个集群就是由一个或多个节点组织在一起，它们共同持有你整个的数据，并一起提供索引和搜索功能。
2. 节点（node）：一个节点就是集群中的一个服务器，作为集群的一部分，参与集群的索引和搜索功能。
3. 索引（index）： 一个索引就是一个具有相似特征的文档集合，相当于一个数据集。
4. 类型（type）：在一个索引中，你可以定义一种或多种类型，相当于对一个索引中数据的逻辑划分（ES官方文档明确说明不推荐使用type，即建议一个索引只有一个type。ES7.0已经废弃了type）。
5. 文档（document）： 一个文档是一个可被索引的基础信息单元，就是索引里面的一条数据，使用JSON格式来表示。
6. 域（field）： 文档中的一个数据字段。一个文档由多个域组成。
7. 分片（shards）：分片是索引的一部分，一个索引由多个分片组成。每个分片可以分布在不同的节点上，ES会根据文档id（也可以指定其他字段）做hash，使用得到的hash值将文档路由到指定分片上。分片是ES做Data Rebalance的最小单元。
8. 副本（replicas）：创建索引时可以为索引指定0个或者多个副本。副本是分片级别的，即索引的分片由1个主分片（primary shard）和0个或者多个副本分片（replica shard）组成。primary shard可以接受读取和写入请求，replica shard只能接受读取请求。所以副本只能提高数据的可用性和并发读取能力。当primary shard所在服务器的节点挂掉以后，ES会通过leader选举机制将replica shard为primary shard。

## 二、基本查询

1. 分页

   ```json
   {
     "query": {
       "match_all": {}
     },
     "from": 0,  
     "size": 1  
   }
   ```

   from：从第几个商品开始查，最开始是 0

   size：要查几个结果

2. 范围查询（range）

   ```json
   {
     "query": {
       "range": {
         "price": {
           "gte": 400,
           "lte": 700
         }
       }
     }
   }
   ```

   gt：大于

   gte：大于等于

   lt：小于

   lte：小于等于

### match

查询的字段内容是进行分词处理的，只要分词的单词结果中，在数据中有满足任意的分词结果都会被查询出来

```json
{
  "query": {
    "match": {
      "product_name": "PHILIPS toothbrush"
    }
  }
}
```

1. operator ：自定义匹配关系

```json
{
  "query": {
    "match": {
      "product_name": {
        "query": "PHILIPS toothbrush",
        "operator": "and"
      }
     }
   }
}
```

2. 分词概率匹配（分词命中50%就返回）

```json
{
  "query": {
    "match": {
      "product_name": {
        "query": "java 程序员 书 推荐",
        "minimum_should_match": "50%"
      }
    }
  }
}
```

#### multi_match：同一条件匹配多个字段

```json
{
  "query": {
    "multi_match": {
      "query": "toothbrush",
      "fields": ["product_name","product_desc"]
    }
  }
}
```

#### match_phrase（短语搜索）

对查询词不进行分词，必须完全匹配查询词才可以作为结果显示。

```json
{
  "query": {
    "match_phrase": {
      "product_name": "PHILIPS toothbrush"
    }
  }
}
```

### term和terms

代表完全匹配，即不进行分词器分析，文档中必须包含整个搜索的词汇

```json
{
  "query": {
      "filter":{
        "term": {
          "product_name": "python"
        }
      }
    }
}
```

相比于数据库中的in

```json
{
  "query": {
      "filter": {
        "terms": {
          "product_name": [
            "python",
            "java"
          ]
        }
      }
    }
}
```

### bool

bool 下包括：

- must（必须匹配，类似于数据库的 =）

- must_not（必须不匹配，类似于数据库的 !=）

- should（没有强制匹配，类似于数据库的 or）

  - 如果组合查询中没有must条件，可通过minimum_should_match 设置匹配多个

    ```json
    {
      "query": {
        "bool": {
          "should": [
            {
              "match": {
                "product_name": "java"
              }
            },
            {
              "match": {
                "product_name": "程序员"
              }
            },
          ],
          "minimum_should_match": 2
        }
      }
    }
    ```

- filter（过滤）

```json
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "product_name": "PHILIPS toothbrush"
          }
        }
      ],
      "should": [
        {
          "match": {
            "product_desc": "刷头"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "product_name": "HX6730"
          }
        }
      ],
      "filter": {
        "range": {
          "price": {
            "gte": 33.00
          }
        }
      }
    }
  }
}
```

#### boost用法（搜索优先级）

例：PHILIPS toothbrush，要比：Braun toothbrush 更加优先

```json
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "product_name": "toothbrush"
          }
        }
      ],
      "should": [
        {
          "match": {
            "product_name": {
              "query": "PHILIPS",
              "boost": 4
            }
          }
        },
        {
          "match": {
            "product_name": {
              "query": "Braun",
              "boost": 3
            }
          }
        }
      ]
    }
  }
}
```

### 模糊查询（性能较差）

1. prefix前缀搜索

   例：iphone-6，iphone-7。搜索 iphone 

   ```bash
   {
     "query": {
       "prefix": {
         "product_name": {
           "value": "iphone"
         }
       }
     }
   }
   ```

2. wildcard通配符搜索

   ```bash
   {
     "query": {
       "wildcard": {
         "product_name": {
           "value": "ipho*"
         }
       }
     }
   }
   ```

3. regexp正则搜索

   ```bash
   {
     "query": {
       "regexp": {
         "product_name": "iphone[0-9].+"
       }
     }
   }
   ```

## 三、聚合分组

ElasticSearch除了致力于搜索之外，也提供了聚合实时分析数据的功能，透过聚合，我们可以得到一个数据的概览，分析和总结全套的数据

对相同的数据进行 搜索/过滤 + 分析，两个愿望一次满足

聚合的两个主要的概念，分别是 桶 和 指标

- 桶(Buckets) : 满足特定条件的文档的集合

  1. 当聚合开始被执行，每个文档会决定符合哪个桶的条件，如果匹配到，文档将放入相应的桶并接着进行聚合操作

     > 像是一个员工属于男性桶或者女性桶

  2. 桶可以被嵌套在其他桶里面

     > 北京能放在中国桶裡

- 指标(Metrics) : 对桶内的文档进行统计计算

  1. 桶能让我们划分文档到有意义的集合， 但是最终我们需要的是对这些桶内的文档进行一些指标的计算

  2. 指标通常是简单的数学运算(像是min、max、avg、sum）

     > 计算像平均薪资、最高出售价格、95%的查询延迟这样的数据

### 执行顺序

当query和aggs一起存在时，会先执行query的主查询，主查询query执行完后会搜出一批结果，而这些结果才会被拿去aggs拿去做聚合

```json
{
    "query": { ... },
    "size": 0,
    "aggs": {
        "custom_name1": {  //aggs后面接著的是一个自定义的name
            "桶": { ... }  //再来才是接桶
        },
        "custom_name2": {  //一个aggs裡可以有很多聚合
            "桶": { ... }
        },
        "custom_name3": {
            "桶": {
               .....
            },
            "aggs": {  //aggs可以嵌套在别的aggs裡面
                "in_name": { //记得使用aggs需要先自定义一个name
                    "桶": { ... } //in_name的桶作用的文档是custom_name3的桶的结果
                }
            }
        }
    }
}
```

### 常见的桶

1. terms桶 : 针对某个field的值进行分组，field有几种值就分成几组

   - terms桶在进行分组时，会爲此field中的每种值创建一个新的桶

   - 要注意此 "terms桶" 和平常用在主查询query中的 "查找terms" 是不同的东西

     > 测试数据
     >
     > ```json
     > { "color": "red" }
     > { "color": "green" }
     > { "color": ["red", "blue"] }
     > ```
     >
     > dsl语句
     >
     > ```json
     > {
     >     "query": {
     >         "match_all": {}
     >     },
     >     "size": 0,
     >     "aggs": {
     >         "my_name": {
     >             "terms": {
     >                 "field": "color" //使用color来进行分组
     >             }
     >         }
     >     }
     > }
     > ```
     >
     > 结果
     >
     > ```json
     > "aggregations": {
     >     "my_name": {
     >         "doc_count_error_upper_bound": 0,
     >         "sum_other_doc_count": 0,
     >         "buckets": [
     >             {
     >                 "key": "blue",
     >                 "doc_count": 1
     >             },
     >             {
     >                 "key": "red",
     >                 "doc_count": 2  //表示color为red的文档有2个，此例中就是 {"color": "red"} 和 {"color": ["red", "blue"]}这两个文档
     >             },
     >             {
     >                 "key": "green",
     >                 "doc_count": 1
     >             }
     >         ]
     >     }
     > }
     > ```
     >
     > 

2. filter桶 : 一个用来过滤的桶

   - 要注意此处的 "filter桶" 和用在主查询query的 "过滤filter" 的用法是一模一样的，都是过滤，不过差别是 "filter桶" 会自己给创建一个新的桶，而不会像 "过滤filter" 一样依附在query下，因为filter桶毕竟还是一个聚合桶，因此他可以和别的桶进行嵌套，但他不是依附在别的桶上

     > 测试数据
     >
     > 同上
     >
     > dsl语句
     >
     > ```json
     > {
     >     "query": {
     >         "match_all": {}
     >     },
     >     "size": 0,
     >     "aggs": {
     >         "my_name": {
     >             "filter": { //因为他用法跟一般的过滤filter一样，所以也能使用bool嵌套
     >                 "bool": {
     >                     "must": {
     >                         "terms": { //注意此terms是查找terms，不是terms桶
     >                             "color": [ "red", "blue" ]
     >                         }
     >                     }
     >                 }
     >             }
     >         }
     >     }
     > }
     > ```
     >
     > 结果
     >
     > ```json
     > "aggregations": {
     >     "my_name": {
     >         "doc_count": 2 //filter桶计算出来的文档数量
     >     }
     > }
     > ```
     >
     > 

3. top_hits桶 : 在某个桶底下找出这个桶的前几笔hits，返回的hits格式和主查询query返回的hits格式一模一样

   - 参数

     1. from、size

     2. sort：sort : 设置返回的hits的排序

        - 要注意，假设在主查询query裡已经对数据设置了排序sort，此sort并不会对aggs裡面的数据造成影响，也就是说主查询query查找出来的数据会先丢进aggs而非先经过sort，因此就算主查询设置了sort，也不会影响aggs数据裡的排序因此如果在top_hits桶裡的返回的hits数据想要排序，需要自己在top_hits桶裡设置sort

        - 如果没有设置sort，默认使用主查询query所查出来的_score排序

     3. _source : 设置返回的字段

     > 测试数据
     >
     > ```json
     > { "color": "red", "price": 100 }
     > { "color": ["red", "blue"], "price": 1000 }
     > ```
     >
     > dsl语句
     >
     > 使用terms桶分组，再使用top_hits桶找出每个group裡面的price最小的前5笔hits
     >
     > ```
     > {
     >     "query": {
     >         "match_all": {}
     >     },
     >     "size": 0,
     >     "aggs": {
     >         "my_name": {
     >             "terms": {
     >                 "field": "color"
     >             },
     >             "aggs": {
     >                 "my_top_hits": {
     >                     "top_hits": {
     >                         "size": 5,
     >                         "sort": {
     >                             "price": "asc"
     >                         }
     >                     }
     >                 }
     >             }
     >         }
     >     }
     > }
     > ```
     >
     > 结果
     >
     > ```json
     > "aggregations": {
     >     "my_name": {
     >         "doc_count_error_upper_bound": 0,
     >         "sum_other_doc_count": 0,
     >         "buckets": [
     >             {
     >                 "key": "red",
     >                 "doc_count": 2,  //terms桶计算出来的color为red的文档数
     >                 "my_top_hits": {
     >                     "hits": {  //top_hits桶找出color为red的这些文档中，price从小到大排序取前5笔
     >                         "total": 2,
     >                         "max_score": null,
     >                         "hits": [
     >                             {
     >                                 "_score": null,
     >                                 "_source": { "color": "red", "price": 100 },
     >                                 "sort": [ 100 ]
     >                             },
     >                             {
     >                                 "_score": null,
     >                                 "_source": { "color": [ "red", "blue" ], "price": 1000 },
     >                                 "sort": [ 1000 ]
     >                             }
     >                         ]
     >                     }
     >                 }
     >             },
     >             {
     >                 "key": "blue",
     >                 "doc_count": 1,  //terms桶计算出来的color为blue的文档数
     >                 "my_top_hits": {
     >                     "hits": { //top_hits桶找出的hits
     >                         "total": 1,
     >                         "max_score": null,
     >                         "hits": [
     >                             {
     >                                 "_source": {
     >                                     "color": [ "red", "blue" ], "price": 1000 },
     >                                 "sort": [ 1000 ]
     >                             }
     >                         ]
     >                     }
     >                 }
     >             }
     >         ]
     >     }
     > }
     > ```
     >
     > 

4. date_histogram桶，对指定时间段内数据进行分组

   - 参数

     1. time_zone:"+08:00"：设置市区（东八区），不指定会影响分组时间错误
     2. interval：聚合时间间隔
        - year（1y）年
        - quarter（1q）季度
        - month（1M）月份
        - week（1w）星期
        - day（1d）天
        - hour（1h）小时
        - minute（1m）分钟
        - second（1s）秒
     3. format：指定返回时间格式

     > dsl语句
     >
     > ```json
     > {
     >     "query": {
     >         "match_all": {}
     >     },
     >     "size": 0,
     > "aggs": {
     >             // 自己取的聚合名字
     >             "group_by_grabTime": {
     >                 // es提供的时间处理函数
     >                 "date_histogram": {
     >                     // 需要聚合分组的字段名称, 类型需要为date, 格式没有要求
     >                     "field": "@timestamp",
     >                     // 按什么时间段聚合, 这里是5分钟, 可用的interval在上面给出
     >                     "interval": "5m",
     >                     // 设置时区, 这样就相当于东八区的时间
     >                     "time_zone":"+08:00",
     >                     // 返回值格式化，HH大写，不然不能区分上午、下午
     >                     "format": "yyyy-MM-dd HH",   
     >                     // 为空的话则填充0
     >                     "min_doc_count": 0,
     >                     // 需要填充0的范围
     >                     "extended_bounds": {
     >                         "min": 1533556800000,
     >                         "max": 1533806520000
     >                     }
     >                 },
     >                 // 聚合
     >                 "aggs": {
     >                     // 自己取的名称
     >                     "group_by_status": {
     >                         // es提供
     >                         "terms": {
     >                             // 聚合字段名
     >                             "field": "LowStatusOfPrice"
     >                         }
     >                     }
     >                 }
     >             }
     >         }
     > ```