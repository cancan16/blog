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




