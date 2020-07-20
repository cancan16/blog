---
title: ES基本使用
date: 2020-05-24 11:45:30
update: 2020-05-24 11:45:30
categories: Elasticsearch
tags: [Elasticsearch]
---

<div style="text-align: center"><iframe height="60" width="260" src="https://www.ximalaya.com/thirdparty/player/sound/player.html?id=179549096&type=red" frameborder=0 allowfullscreen></iframe></div>

<p style="font-family:FZShuTi;font-weight:bold;font-size: 25px;text-indent:2em">逆转思维中有个概率“幸存者偏差”，它告诉我们在日常生活中更容易看到成功看不到失败，因此你会系统性得高估成功的希望，思维一定要学会逆转，不然你很容易成为韭菜，其实你看到的所有成功者都是幸存者，你永远不知道幸存者的背后立着多少失败者的墓碑，幸存者偏差会像哈哈镜一样扭曲概率，这意味着：要想缓解幸存者偏差，你就要尽可能常去逛逛曾经大有希望的项目投资和事业的墓地，这样的散步虽然伤感，但一定对你的思维有好处。</p>
<img style="width: 80%;height:80%" src="https://volc1612.gitee.io/blog/images/ES基本使用/安吉.jpg"/>

<!-- more -->

### 纲要思维导图

<details>
<summary>点击展开思维导图</summary>
<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:100%; height:400px;" src="https://www.processon.com/embed/5e9da6c5f346fb177b86bd70"></iframe>
</details>

### 搜索的使用


#### 词条查询(term)

词条查询不会分析查询条件，只有当词条和查询字符串完全匹配时，才匹配搜索。


* 单条term查询

    ```
    POST http://192.168.25.11:9200/nba/_search
    ```

    请求参数

    ```json
    {
        "query": {
            "term": {
                "jerse_no": "23"
            }
        }
    }
    ```

    响应：匹配到jerse_no字段值为23的所有文档返回

    ```json
    {
        "took": 967,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 2,
                "relation": "eq"
            },
            "max_score": 0.6931471,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "3",
                    "_score": 0.6931471,
                    "_source": {
                        "name": "詹姆斯",
                        "team_name": "湖⼈",
                        "position": "⼩前锋",
                        "play_year": 15,
                        "jerse_no": "23"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "4",
                    "_score": 0.6931471,
                    "_source": {
                        "name": "詹姆斯2",
                        "team_name": "湖⼈",
                        "position": "⼩前锋",
                        "play_year": 15,
                        "jerse_no": "23"
                    }
                }
            ]
        }
    }
    ```

* 多条term查询

    ```
    POST http://192.168.25.11:9200/nba/_search
    ```

    请求参数

    ```json
    {
        "query": {
            "terms": {
                "jerse_no": [
                    "23",
                    "13"
                ]
            }
        }
    }
    ```

    响应：匹配到jerse_no字段值为23或13的所有文档返回

    ```json
    {
        "took": 36,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 3,
                "relation": "eq"
            },
            "max_score": 1.0,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "1",
                    "_score": 1.0,
                    "_source": {
                        "name": "哈登",
                        "team_name": "⽕箭",
                        "position": "得分后卫",
                        "play_year": 10,
                        "jerse_no": "13"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "3",
                    "_score": 1.0,
                    "_source": {
                        "name": "詹姆斯",
                        "team_name": "湖⼈",
                        "position": "⼩前锋",
                        "play_year": 15,
                        "jerse_no": "23"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "4",
                    "_score": 1.0,
                    "_source": {
                        "name": "詹姆斯2",
                        "team_name": "湖⼈",
                        "position": "⼩前锋",
                        "play_year": 15,
                        "jerse_no": "23"
                    }
                }
            ]
        }
    }
    ```

#### 全⽂查询（full text）

ElasticSearch引擎会先分析查询字符串，将其拆分成多个分词，只要已分析的字段中包含词条的任意⼀个，或全部包含，就匹配查询条件，返回该⽂档；如果不包含任意⼀个分词，表示没有任何⽂档匹配查询条件。


* match_all(查询所有文档数据)

    ```
    POST localhost:9200/nba/_search
    ```

    请求参数：在不指定from和size时，默认查询10记录

    ```json
    {
        "query": {
            "match_all": {}
        },
        "from": 0,
        "size": 10
    }
    ```

    响应
    
    ```json
    {
        "took": 20,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 4,
                "relation": "eq"
            },
            "max_score": 1.0,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "1",
                    "_score": 1.0,
                    "_source": {
                        "name": "哈登",
                        "team_name": "⽕箭",
                        "position": "得分后卫",
                        "play_year": 10,
                        "jerse_no": "13"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "2",
                    "_score": 1.0,
                    "_source": {
                        "name": "库⾥",
                        "team_name": "勇⼠",
                        "position": "控球后卫",
                        "play_year": 10,
                        "jerse_no": "30"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "3",
                    "_score": 1.0,
                    "_source": {
                        "name": "詹姆斯",
                        "team_name": "湖⼈",
                        "position": "⼩前锋",
                        "play_year": 15,
                        "jerse_no": "23"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "4",
                    "_score": 1.0,
                    "_source": {
                        "name": "詹姆斯2",
                        "team_name": "湖⼈",
                        "position": "⼩前锋",
                        "play_year": 15,
                        "jerse_no": "23"
                    }
                }
            ]
        }
    }
    ```

* match(包含匹配查询)

    ```
    POST localhost:9200/nba/_search
    ```

    请求参数：在不指定from和size时，默认查询10记录

    ```json
    {
        "query": {
            "match": {
                "name":"库里宝宝"
            }
        },
        "from": 0,
        "size": 10
    }
    ```

    响应: **`name`字段是`text`类型的，那么在所有文档中包含"库里宝宝"任意一个字或连续多个字都可以匹配出来，如果`name`为`keyword`类型，则是等值匹配**
    
    ```json
    {
        "took": 17,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 2,
                "relation": "eq"
            },
            "max_score": 4.592435,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "5",
                    "_score": 4.592435,
                    "_source": {
                        "name": "里宝",
                        "team_name": "⽕箭",
                        "position": "得分后卫1",
                        "play_year": "10",
                        "jerse_no": "13"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "2",
                    "_score": 1.5308115,
                    "_source": {
                        "name": "库⾥",
                        "team_name": "勇⼠",
                        "position": "控球后卫",
                        "play_year": 10,
                        "jerse_no": "30"
                    }
                }
            ]
        }
    }
    ```

* multi_match(多字段混合匹配)

    ```
    POST localhost:9200/nba/_search
    ```

    请求参数一：查询字段`title`或`name`中包含`shooter`字符串的文档查询出来

    ```json
    {
        "query": {
            "multi_match": {
                "query": "shooter",
                "fields": [
                    "title",
                    "name"
                ]
            }
        }
    }
    ```

    响应一：

    ```json
    {
        "took": 8,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 1,
                "relation": "eq"
            },
            "max_score": 1.4655045,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "1",
                    "_score": 1.4655045,
                    "_source": {
                        "name": "哈登shooter",
                        "team_name": "⽕箭",
                        "position": "双能卫",
                        "play_year": "10",
                        "jerse_no": "13",
                        "title": "shooter"
                    }
                }
            ]
        }
    }
    ```

    请求参数二：查询包含字段`title`或`name`字段中包含`shooter`字符串的文档查询出来

    ```json
    {
        "query": {
            "multi_match": {
                "query": "shooter",
                "fields": [
                    "*title",
                    "name"
                ]
            }
        }
    }
    ```

    响应：

    ```json
    {
        "took": 2,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 2,
                "relation": "eq"
            },
            "max_score": 1.567127,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "1",
                    "_score": 1.567127,
                    "_source": {
                        "name": "哈登shooter",
                        "team_name": "⽕箭",
                        "position": "双能卫",
                        "play_year": "10",
                        "jerse_no": "13",
                        "title": "shooter"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "YWTbRHIBE_RV24_JQjBa",
                    "_score": 0.2876821,
                    "_source": {
                        "name": "哈登",
                        "team_name": "⽕箭",
                        "position": "得分后卫",
                        "play_year": "10",
                        "jerse_no": "13",
                        "1title": "shooter"
                    }
                }
            ]
        }
    }
    ```

* match_phrase(等值包含匹配-不会匹配拆开的英语单词)

    ```
    post localhost:9200/nba/_search
    ```

    参数： 文档中`position`字段中包含`得分后卫`则返回该文档数据

    ```json
    {
        "query": {
            "match_phrase": {
                "position": "得分后卫"
            }
        }
    }
    ```

    响应：

    ```json
    {
        "took": 2,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 3,
                "relation": "eq"
            },
            "max_score": 2.1456752,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "YWTbRHIBE_RV24_JQjBa",
                    "_score": 2.1456752,
                    "_source": {
                        "name": "哈登",
                        "team_name": "⽕箭",
                        "position": "得分后卫",
                        "play_year": "10",
                        "jerse_no": "13",
                        "1title": "shooter"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "aWRRRXIBE_RV24_JYTAT",
                    "_score": 2.1456752,
                    "_source": {
                        "name": "哈登",
                        "team_name": "⽕箭",
                        "position": "得分后卫",
                        "play_year": "10",
                        "jerse_no": "13",
                        "title": "the best student"
                    }
                },
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "5",
                    "_score": 1.9396904,
                    "_source": {
                        "name": "里宝",
                        "team_name": "⽕箭",
                        "position": "得分后卫1",
                        "play_year": "10",
                        "jerse_no": "13"
                    }
                }
            ]
        }
    }
    ```

    * 不会匹配分开的英语单词

        ```
        post localhost:9200/nba/_search
        ```

        参数： 文档中`title`字段中包含`the be`，但是`be`并不是完整的单词，所以会不返回文档数据

        ```json
        {
            "query": {
                "match_phrase": {
                    "title": "the be"
                }
            }
        }
        ```

        响应：

        ```json
        {
            "took": 2,
            "timed_out": false,
            "_shards": {
                "total": 1,
                "successful": 1,
                "skipped": 0,
                "failed": 0
            },
            "hits": {
                "total": {
                    "value": 0,
                    "relation": "eq"
                },
                "max_score": null,
                "hits": []
            }
        }
        ```



* match_phrase_prefix(匹配前缀查询，相当于like '*%')

    ```
    POST localhost:9200/nba/_search
    ```

    参数：返回`title`字段中前缀包含`the best s`的文档

    ```json
    {
        "query": {
            "match_phrase_prefix": {
                "title": "the best s"
            }
        }
    }
    ```

    响应：

    ```json
    {
        "took": 534,
        "timed_out": false,
        "_shards": {
            "total": 1,
            "successful": 1,
            "skipped": 0,
            "failed": 0
        },
        "hits": {
            "total": {
                "value": 1,
                "relation": "eq"
            },
            "max_score": 2.3017716,
            "hits": [
                {
                    "_index": "nba",
                    "_type": "_doc",
                    "_id": "aWRRRXIBE_RV24_JYTAT",
                    "_score": 2.3017716,
                    "_source": {
                        "name": "哈登",
                        "team_name": "⽕箭",
                        "position": "得分后卫",
                        "play_year": "10",
                        "jerse_no": "13",
                        "title": "the best student"
                    }
                }
            ]
        }
    }
    ```


### 分词器的介绍和使用


* 什么是分词器

    * 将⽤户输⼊的⼀段⽂本，按照⼀定逻辑，分析成多个词语的⼀种⼯具
    * example: The best 3-points shooter is Curry!


* 常用的内置分词器

    * standard analyzer
    * simple analyzer
    * whitespace analyzer
    * stop analyzer
    * language analyzer
    * pattern analyzer


* `standard analyzer`

    * 标准分析器是默认分词器，如果未指定，则使⽤该分词器。
    * POST localhost:9200/_analyze

        ```
        {
            "analyzer": "standard",
            "text": "The best 3-points shooter is Curry!"
        }
        ```

        响应：

        ```
        {
            "tokens": [
                {
                    "token": "the",
                    "start_offset": 0,
                    "end_offset": 3,
                    "type": "<ALPHANUM>",
                    "position": 0
                },
                {
                    "token": "best",
                    "start_offset": 4,
                    "end_offset": 8,
                    "type": "<ALPHANUM>",
                    "position": 1
                },
                {
                    "token": "3",
                    "start_offset": 9,
                    "end_offset": 10,
                    "type": "<NUM>",
                    "position": 2
                },
                {
                    "token": "points",
                    "start_offset": 11,
                    "end_offset": 17,
                    "type": "<ALPHANUM>",
                    "position": 3
                },
                {
                    "token": "shooter",
                    "start_offset": 18,
                    "end_offset": 25,
                    "type": "<ALPHANUM>",
                    "position": 4
                },
                {
                    "token": "is",
                    "start_offset": 26,
                    "end_offset": 28,
                    "type": "<ALPHANUM>",
                    "position": 5
                },
                {
                    "token": "curry",
                    "start_offset": 29,
                    "end_offset": 34,
                    "type": "<ALPHANUM>",
                    "position": 6
                }
            ]
        }
        ```
* simple analyzer

    simple 分析器当它遇到只要不是字⺟的字符，就将⽂本解析成term，⽽且所有的term都是⼩写的。只保留字母。
    POST localhost:9200/_analyze

    ```
    {
        "analyzer": "simple",
        "text": "The best 3-points shooter is Curry!"
    }
    ```

    响应：
    ```
    {
        "tokens": [
            {
                "token": "the",
                "start_offset": 0,
                "end_offset": 3,
                "type": "word",
                "position": 0
            },
            {
                "token": "best",
                "start_offset": 4,
                "end_offset": 8,
                "type": "word",
                "position": 1
            },
            {
                "token": "points",
                "start_offset": 11,
                "end_offset": 17,
                "type": "word",
                "position": 2
            },
            {
                "token": "shooter",
                "start_offset": 18,
                "end_offset": 25,
                "type": "word",
                "position": 3
            },
            {
                "token": "is",
                "start_offset": 26,
                "end_offset": 28,
                "type": "word",
                "position": 4
            },
            {
                "token": "curry",
                "start_offset": 29,
                "end_offset": 34,
                "type": "word",
                "position": 5
            }
        ]
    }
    ```
* `whitespace analyzer`
    `whitespace`分析器，当它遇到空⽩字符时，就将⽂本解析成`terms`
    POST localhost:9200/_analyze

    ```
    {
    "analyzer": "whitespace",
    "text": "The best 3-points shooter is Curry!"
    }
    ```

    ```
    {
        "tokens": [
            {
                "token": "The",
                "start_offset": 0,
                "end_offset": 3,
                "type": "word",
                "position": 0
            },
            {
                "token": "best",
                "start_offset": 4,
                "end_offset": 8,
                "type": "word",
                "position": 1
            },
            {
                "token": "3-points",
                "start_offset": 9,
                "end_offset": 17,
                "type": "word",
                "position": 2
            },
            {
                "token": "shooter",
                "start_offset": 18,
                "end_offset": 25,
                "type": "word",
                "position": 3
            },
            {
                "token": "is",
                "start_offset": 26,
                "end_offset": 28,
                "type": "word",
                "position": 4
            },
            {
                "token": "Curry!",
                "start_offset": 29,
                "end_offset": 35,
                "type": "word",
                "position": 5
            }
        ]
    }
    ```

* `stop analyzer`
    `stop 分析器` 和 `simple 分析器`很像，唯⼀不同的是，stop 分析器增加了对删除停⽌词的⽀持，默认使⽤了`english`停⽌词
    `stopwords` 预定义的停⽌词列表，⽐如 (the,a,an,this,of,at)等等
    POST localhost:9200/_analyze

    ```
    {
    "analyzer": "stop",
    "text": "The best 3-points shooter is Curry!"
    }
    ```

    ```
    {
        "tokens": [
            {
                "token": "best",
                "start_offset": 4,
                "end_offset": 8,
                "type": "word",
                "position": 1
            },
            {
                "token": "points",
                "start_offset": 11,
                "end_offset": 17,
                "type": "word",
                "position": 2
            },
            {
                "token": "shooter",
                "start_offset": 18,
                "end_offset": 25,
                "type": "word",
                "position": 3
            },
            {
                "token": "curry",
                "start_offset": 29,
                "end_offset": 34,
                "type": "word",
                "position": 5
            }
        ]
    }
    ```

* language analyzer

    （特定的语⾔的分词器，⽐如说，english，英语分词器),内置语⾔：arabic, armenian,
    basque, bengali, brazilian, bulgarian, catalan, cjk, czech, danish, dutch, english, finnish,
    french, galician, german, greek, hindi, hungarian, indonesian, irish, italian, latvian,
    lithuanian, norwegian, persian, portuguese, romanian, russian, sorani, spanish,
    swedish, turkish, thai
    Post localhost:9200/_analyze

    ```
    {
    "analyzer": "english",
    "text": "The best 3-points shooter is Curry!"
    }
    ```

    ```
    {
        "tokens": [
            {
                "token": "best",
                "start_offset": 4,
                "end_offset": 8,
                "type": "<ALPHANUM>",
                "position": 1
            },
            {
                "token": "3",
                "start_offset": 9,
                "end_offset": 10,
                "type": "<NUM>",
                "position": 2
            },
            {
                "token": "point",
                "start_offset": 11,
                "end_offset": 17,
                "type": "<ALPHANUM>",
                "position": 3
            },
            {
                "token": "shooter",
                "start_offset": 18,
                "end_offset": 25,
                "type": "<ALPHANUM>",
                "position": 4
            },
            {
                "token": "curri",
                "start_offset": 29,
                "end_offset": 34,
                "type": "<ALPHANUM>",
                "position": 6
            }
        ]
    }
    ```

*   `pattern analyzer`

    ⽤正则表达式来将⽂本分割成terms，默认的正则表达式是\W+（⾮单词字符）
    POST localhost:9200/_analyze

    ```
    {
    "analyzer": "pattern",
    "text": "The best 3-points shooter is Curry!"
    }
    ```

    ```
    {
        "tokens": [
            {
                "token": "the",
                "start_offset": 0,
                "end_offset": 3,
                "type": "word",
                "position": 0
            },
            {
                "token": "best",
                "start_offset": 4,
                "end_offset": 8,
                "type": "word",
                "position": 1
            },
            {
                "token": "3",
                "start_offset": 9,
                "end_offset": 10,
                "type": "word",
                "position": 2
            },
            {
                "token": "points",
                "start_offset": 11,
                "end_offset": 17,
                "type": "word",
                "position": 3
            },
            {
                "token": "shooter",
                "start_offset": 18,
                "end_offset": 25,
                "type": "word",
                "position": 4
            },
            {
                "token": "is",
                "start_offset": 26,
                "end_offset": 28,
                "type": "word",
                "position": 5
            },
            {
                "token": "curry",
                "start_offset": 29,
                "end_offset": 34,
                "type": "word",
                "position": 6
            }
        ]
    }
    ```

* 添加指定分词器的索引

    * 添加指定分词器的索引
    
        PUT localhost:9200/my_index

        ```
        {
            "settings": {
                "analysis": {
                    "analyzer": {
                        "my_analyzer": {
                            "type": "whitespace"
                        }
                    }
                }
            },
            "mappings": {
                "properties": {
                    "name": {
                        "type": "text"
                    },
                    "team_name": {
                        "type": "text"
                    },
                    "position": {
                        "type": "text"
                    },
                    "play_year": {
                        "type": "long"
                    },
                    "jerse_no": {
                        "type": "keyword"
                    },
                    "title": {
                        "type": "text",
                        "analyzer": "my_analyzer"
                    }
                }
            }
        }
        ```

    * 向文档中添加数据

        PUT localhost:9200/my_index/_doc/1

        ```
        {
            "name": "库⾥",
            "team_name": "勇⼠",
            "position": "控球后卫",
            "play_year": 10,
            "jerse_no": "30",
            "title": "The best 3-points shooter is Curry!"
        }
        ```

    * 查询指定了分词器的文档数据

        get {{url}}/my_index/_doc/1

        ```
        {
            "_index": "my_index",
            "_type": "_doc",
            "_id": "1",
            "_version": 1,
            "_seq_no": 0,
            "_primary_term": 1,
            "found": true,
            "_source": {
                "name": "库⾥",
                "team_name": "勇⼠",
                "position": "控球后卫",
                "play_year": 10,
                "jerse_no": "30",
                "title": "The best 3-points shooter is Curry!"
            }
        }
        ```

    * 搜索文档
    
        POST localhost:9200/my_index/_search

        请求参数-匹配`Curry!`的文档数据

        ```
        {
            "query": {
                "match": {
                    "title": "Curry!"
                }
            }
        }
        ```

        响应结果

        ```
        {
            "took": 2,
            "timed_out": false,
            "_shards": {
                "total": 1,
                "successful": 1,
                "skipped": 0,
                "failed": 0
            },
            "hits": {
                "total": {
                    "value": 1,
                    "relation": "eq"
                },
                "max_score": 0.2876821,
                "hits": [
                    {
                        "_index": "my_index",
                        "_type": "_doc",
                        "_id": "1",
                        "_score": 0.2876821,
                        "_source": {
                            "name": "库⾥",
                            "team_name": "勇⼠",
                            "position": "控球后卫",
                            "play_year": 10,
                            "jerse_no": "30",
                            "title": "The best 3-points shooter is Curry!"
                        }
                    }
                ]
            }
        }
        ```


        请求参数-匹配`Curry`的文档数据

        ```
        {
            "query": {
                "match": {
                    "title": "Curry"
                }
            }
        }
        ```

        无法搜索到添加的文档，原因是该文档是以空格分词器来分词，分词列表中包含`Curry!`而不包含`Curry`。

        

### 常见的中文分词器的使用


如果⽤默认的分词器`standard`

POST localhost:9200/_analyze】

```
{
    "analyzer": "standard",
    "text": "我是一位出色的java架构师！"
}
```

查看分词效果

```
{
    "tokens": [
        {
            "token": "我",
            "start_offset": 0,
            "end_offset": 1,
            "type": "<IDEOGRAPHIC>",
            "position": 0
        },
        {
            "token": "是",
            "start_offset": 1,
            "end_offset": 2,
            "type": "<IDEOGRAPHIC>",
            "position": 1
        },
        {
            "token": "一",
            "start_offset": 2,
            "end_offset": 3,
            "type": "<IDEOGRAPHIC>",
            "position": 2
        },
        {
            "token": "位",
            "start_offset": 3,
            "end_offset": 4,
            "type": "<IDEOGRAPHIC>",
            "position": 3
        },
        {
            "token": "出",
            "start_offset": 4,
            "end_offset": 5,
            "type": "<IDEOGRAPHIC>",
            "position": 4
        },
        {
            "token": "色",
            "start_offset": 5,
            "end_offset": 6,
            "type": "<IDEOGRAPHIC>",
            "position": 5
        },
        {
            "token": "的",
            "start_offset": 6,
            "end_offset": 7,
            "type": "<IDEOGRAPHIC>",
            "position": 6
        },
        {
            "token": "java",
            "start_offset": 7,
            "end_offset": 11,
            "type": "<ALPHANUM>",
            "position": 7
        },
        {
            "token": "架",
            "start_offset": 11,
            "end_offset": 12,
            "type": "<IDEOGRAPHIC>",
            "position": 8
        },
        {
            "token": "构",
            "start_offset": 12,
            "end_offset": 13,
            "type": "<IDEOGRAPHIC>",
            "position": 9
        },
        {
            "token": "师",
            "start_offset": 13,
            "end_offset": 14,
            "type": "<IDEOGRAPHIC>",
            "position": 10
        }
    ]
}
```

这种分词效果，显然不符合搜索场景，不能按照单个汉子进行分词。


#### 常⻅的中文分词器

* `smartCN` ⼀个简单的中⽂或中英⽂混合⽂本的分词器
* `IK分词器` 更智能更友好的中⽂分词器


##### smartCn

* 安装

进入es安装目录的bin目录，执行命令

```
sh elasticsearch-plugin install analysis-smartcn
```

安装后重新启动es

POST localhost:9200/_analyze

```
{
    "analyzer": "smartcn",
    "text": "我是一位出色的java架构师！"
}
```

响应结果

```
{
    "tokens": [
        {
            "token": "我",
            "start_offset": 0,
            "end_offset": 1,
            "type": "word",
            "position": 0
        },
        {
            "token": "是",
            "start_offset": 1,
            "end_offset": 2,
            "type": "word",
            "position": 1
        },
        {
            "token": "一",
            "start_offset": 2,
            "end_offset": 3,
            "type": "word",
            "position": 2
        },
        {
            "token": "位",
            "start_offset": 3,
            "end_offset": 4,
            "type": "word",
            "position": 3
        },
        {
            "token": "出色",
            "start_offset": 4,
            "end_offset": 6,
            "type": "word",
            "position": 4
        },
        {
            "token": "的",
            "start_offset": 6,
            "end_offset": 7,
            "type": "word",
            "position": 5
        },
        {
            "token": "java",
            "start_offset": 7,
            "end_offset": 11,
            "type": "word",
            "position": 6
        },
        {
            "token": "架构",
            "start_offset": 11,
            "end_offset": 13,
            "type": "word",
            "position": 7
        },
        {
            "token": "师",
            "start_offset": 13,
            "end_offset": 14,
            "type": "word",
            "position": 8
        }
    ]
}
```

* 卸载 sh elasticsearch-plugin remove analysis-smartcn

#### IK分词器

<a href="https://github.com/medcl/elasticsearch-analysis-ik/releases">下载</a>
 
需要找到对应es版本的IK分词器版本

安装 解压安装到es安装目录的`plugins`⽬录

安装后重新启动

POST localhost:9200/_analyze

``
{
 "analyzer": "ik_max_word",
 "text": "⽕箭明年总冠军"
}
``

结果

```
{
    "tokens": [
        {
            "token": "我",
            "start_offset": 0,
            "end_offset": 1,
            "type": "CN_CHAR",
            "position": 0
        },
        {
            "token": "是",
            "start_offset": 1,
            "end_offset": 2,
            "type": "CN_CHAR",
            "position": 1
        },
        {
            "token": "一位",
            "start_offset": 2,
            "end_offset": 4,
            "type": "CN_WORD",
            "position": 2
        },
        {
            "token": "一",
            "start_offset": 2,
            "end_offset": 3,
            "type": "TYPE_CNUM",
            "position": 3
        },
        {
            "token": "位",
            "start_offset": 3,
            "end_offset": 4,
            "type": "COUNT",
            "position": 4
        },
        {
            "token": "出色",
            "start_offset": 4,
            "end_offset": 6,
            "type": "CN_WORD",
            "position": 5
        },
        {
            "token": "出",
            "start_offset": 4,
            "end_offset": 5,
            "type": "COUNT",
            "position": 6
        },
        {
            "token": "色",
            "start_offset": 5,
            "end_offset": 6,
            "type": "CN_CHAR",
            "position": 7
        },
        {
            "token": "的",
            "start_offset": 6,
            "end_offset": 7,
            "type": "CN_CHAR",
            "position": 8
        },
        {
            "token": "java",
            "start_offset": 7,
            "end_offset": 11,
            "type": "ENGLISH",
            "position": 9
        },
        {
            "token": "架构师",
            "start_offset": 11,
            "end_offset": 14,
            "type": "CN_WORD",
            "position": 10
        },
        {
            "token": "架构",
            "start_offset": 11,
            "end_offset": 13,
            "type": "CN_WORD",
            "position": 11
        },
        {
            "token": "师",
            "start_offset": 13,
            "end_offset": 14,
            "type": "CN_CHAR",
            "position": 12
        }
    ]
}
```

### 常见的数据类型

* 数据类型
    * 核⼼数据类型
    * 复杂数据类型
    * 专⽤数据类型


#### 核⼼数据类型

* 字符串

    * text

        ⽤于全⽂索引，该类型的字段将通过分词器进⾏分词
    * keyword

        不分词，只能搜索该字段的完整的值
    * 数值型

        long, integer, short, byte, double, float, half_float, scaled_float
    * 布尔 - boolean
    * ⼆进制 - binary

        * 该类型的字段把值当做经过 base64 编码的字符串，默认不存储，且不可搜索
    * 范围类型

        * 范围类型表示值是⼀个范围，⽽不是⼀个具体的值
        * integer_range, float_range, long_range, double_range, date_range
        * 譬如 age 的类型是 integer_range，那么值可以是 {"gte" : 20, "lte" : 40}；搜索 "term" : {"age": 21} 可以搜索该值
    * ⽇期 - date

        * 由于Json没有date类型，所以es通过识别字符串是否符合format定义的格式来判断是否为date类型

        * format默认为：strict_date_optional_time||epoch_millis
        * 格式

            "2022-01-01" "2022/01/01 12:10:30" 这种字符串格式
        * 从开始纪元（1970年1⽉1⽇0点） 开始的毫秒数

            从开始纪元开始的秒数
        * PUT localhost:9200/nba/_mapping



        




