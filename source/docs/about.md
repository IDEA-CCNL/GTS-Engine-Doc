# 关于GTS Engine

GTS引擎（GTS-Engine）是一款开箱即用且性能强大的自然语言理解引擎，能够仅用小样本就能自动化生产NLP模型。它依托于封神榜开源体系的基础模型，并在下游进行了有监督预训练，同时集成了多种小样本学习技术，搭建了一个模型自动生产的流水线。作为GTS Factory的核心组件，集先进的预训练模型、端到端的模型训练和推理于一体，提供一站式NLP开发与服务，让开发者能够在私有化环境上高效地开发模型，搭建服务。

## 应用场景

GTS Engine着眼于小样本任务，支持开发者根据自己的数据集定制化开发下游任务。

GTS Engine包含两个训练引擎：乾坤鼎和八卦炉。**乾坤鼎**系列是以1.3B参数的大模型为底座，通过大模型结合多种小样本学习技术进行训练和推理的引擎。**八卦炉**系列是以110M参数的base模型为底座，融合大模型、数据增强、协同训练等方法进行训练和推理的引擎。GTS Engine支持的任务类型：

| task_type |   乾坤鼎    | 八卦炉     |                                             
| ----------- | -------------- |  ------------------------------------------------------------ |
| 文本分类     | 支持 | 支持 |
| 句子对相似度  | 支持 | 开发中 |
| 自然语言推理  | 支持 | 开发中 |
| 信息抽取     | 暂不支持 | 支持 |
| 关键词生成   | 开发中 | 暂不支持 |
| 摘要生成   | 暂不支持 | 支持 |

我们会在后续的版本中持续迭代，将通用的NLP任务集成进GTS Engine。

## 使用GTS Engine的理由

- 先进的预训练模型
- 强悍的小样本学习能力
- 灵活易用的开发接口，让无算法背景的用户也能轻松开发自己的模型


