# 数据预处理

## 数据集类型

训练任务中，GTS Engine要求您至少提供三个数据集：
- 训练集
- 验证集
- 测试集（可选）
- 无标签数据集（可选）
- 标签集（信息抽取无需标签集）


## 数据处理

所有的NLP任务的所有数据集，文件必须为UTF-8格式。每行表示一个样本，不同任务的样本数据格式如下：

### 文本分类任务

- **训练数据**

每行是一个样本，采用json格式，数据字段必须含有`"content"`和`"label"`字段, `"content"`对应的是输入文本，`"label"`字段对应该文本的标签。

```json
# 示例
{
    "content": "佛山市青少年武术比赛开幕，291名武术达人同台竞技", "label": "教育"
}
```

- **验证数据**

验证数据与训练数据格式一致。

```json
# 示例
{
    "content": "王者荣耀：官方悄悄的降价了4个强势英雄，看来米莱狄要来", "label": "电竞"
}
```

- **测试数据**

测试数据与训练数据格式一致。

```json
# 示例
{
    "content": "上联：草根登上星光道，怎么对下联？", "label": "文化"
}
```

- **无标签数据**

每行是一个样本，采用json格式，数据字段必须含有`"content"`字段。

```json
{"content": "挥不去的是记忆，留不住的是年华，拎不起的是失落"}
```


- **标签数据**

数据为json格式，只有一行数据，必须含有"labels"字段，对应的是标签的列表集合。

```json
# 示例
{
    "labels": ["故事", "文化", "娱乐", "体育", "财经", "房产", "汽车", "教育", "科技", "军事", "旅游", "国际", "股票", "农业", "电竞"]
}
```


### 句子对任务

句子对任务包含语义匹配和自然语言推理任务，这两个任务的label比较固定，因此不需要标签文件，只需要训练集/验证集/测试集文件。

- **训练数据**

```json
# 示例

# nli任务，候选标签为["entailment", "contradiction", "neutral"]
{"id": 0, "sentence1": "七五期间开始,国家又投资将武汉市区的部分土堤改建为钢筋泥凝土防水墙", "sentence2": "八五期间会把剩下的土堤都改建完", "label": "neutral"}

# 语义匹配任务，候选标签为["0","1"]，"1"表示两句话语义是匹配的
{"id": 3, "sentence1": "陈情令好不好看", "sentence2": "陈情令好看不好看", "label": "1"}
```

- **验证数据**

格式和训练数据一样

- **测试数据**

```json
# 示例
# nli任务
{"id": 0, "sentence1": "村里还有一个土地庙,里面也装饰得十分气派", "sentence2": "村里只有一个土地庙"}
# 语义匹配任务
{"id": 8, "sentence1": "你会玩什么游戏", "sentence2": "还有什么游戏"}
```

### 信息抽取任务

- **训练数据**

每行是一个样本，采用json格式，数据字段必须含有`"task"`、`"text"`、`"entity_list"/"spo_list"`、`"choice"`字段，其中：

| 字段                            | 类型                               | 说明                                                                                                                                            |
| ----------------------------- | -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| task                          | String                           | 信息抽取任务类型，目前支持"实体识别"及"关系抽取"两种信息抽取任务                                                                                                            |
| text                          | String                           | 输入文本                                                                                                                                          |
| entity_list                   | List[Object]                     | 实体列表信息，当"task"为"实体识别"必须含有该字段                                                                                                                  |
| entity_list.entity_text       | String                           | 实体文本(e.g. "张三")                                                                                                                               |
| entity_list.entity_type       | String                           | 实体类型(e.g. "人名")                                                                                                                               |
| entity_list.entity_index      | [Integer, Integer]               | 实体位置(e.g. [0, 2])                                                                                                                             |
| spo_list                      | List[Object]                     | 关系列表信息，当"task"为"关系抽取"必须含有该字段                                                                                                                  |
| spo_list.predicate            | String                           | 关系类型（谓语）                                                                                                                                      |
| spo_list.subject              | Object                           | 关系中的头实体（主语）                                                                                                                                   |
| spo_list.subject.entity_text  | String                           | 头实体文本                                                                                                                                         |
| spo_list.subject.entity_type  | String                           | 头实体类型                                                                                                                                         |
| spo_list.subject.entity_index | [Integer, Integer]               | 头实体位置                                                                                                                                         |
| spo_list.object               | Object                           | 关系中的尾实体（宾语）                                                                                                                                   |
| spo_list.object.entity_text   | String                           | 尾实体文本                                                                                                                                         |
| spo_list.object.entity_type   | String                           | 尾实体类型                                                                                                                                         |
| spo_list.object.entity_index  | [Integer, Integer]               | 尾实体位置                                                                                                                                         |
| choice                        | List[String] / List[List[String] | 1. 当"task"为"实体识别"时，"choice"字段为实体类型"entity_type"字段的全集<br/>2. 当"task"为"关系抽取"时，"choice"字段为关系三元组类型["entity_type", "predicate", "entity_type"]的全集 |


```python
# 实体识别示例
{
    "task": "实体识别",
    "text": "我们是受到郑振铎先生、阿英先生著作的启示，从个人条件出发，瞄准现代出版史研究的空白，重点集藏解放区、国民党毁禁出版物。",
    "entity_list": [
        {
            "entity_text": "郑振铎",
            "entity_type": "人名",
            "entity_index": [5, 8]
        }, {
            "entity_text": "阿英",
            "entity_type": "人名",
            "entity_index": [11, 13]
        }, {
            "entity_text": "国民党",
            "entity_type": "组织机构",
            "entity_index": [50, 53]
        }
    ],
    "choice": ["组织机构", "人名", "地点"]
}
# 关系抽取示例
{
    "task": "关系抽取",
    "text": "在导师阵容方面，英达有望联手《中国喜剧王》选拔新一代笑星",
    "spo_list": [
        {
            "predicate": "嘉宾",
            "subject": {
                "entity_text": "中国喜剧王",
                "entity_type": "电视综艺",
                "entity_index": [15, 20]
            },
            "object": {
                "entity_text": "英达",
                "entity_type": "人物",
                "entity_index": [8, 10]
            }
        }
    ],
    "choice": [
        ["电视综艺", "嘉宾", "人物"],
        ["影视作品", "主演", "人物"],
        ["影视作品", "编剧", "人物"],
        ["景点", "所在城市", "城市"],
        ["娱乐人物", "获奖", "奖项"],
        ["企业", "董事长", "人物"],
        ["图书作品", "作者", "人物"],
        ["机构", "成立日期", "时间"],
        ["国家", "首都", "城市"]
    ]
}
```

- **验证数据**

验证数据与训练数据格式一致。


- **测试数据**

测试数据与训练数据格式一致。

- **无标签数据**

无标签数据除无需提供`"entity_list"`和`"spo_list"`外，其他字段与训练数据格式一致。
