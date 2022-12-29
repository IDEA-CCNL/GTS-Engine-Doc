# 环境需求

GTS Engine目前支持两种使用方式：

- 使用Docker加载镜像，启动服务；（推荐）
- 直接克隆源码，在您自己的开发环境中启动；

## 硬件环境要求

- CPU
  - 支持x86_64（或称作 x64、Intel 64、AMD64）架构，不支持arm64架构。
- 内存
  - 乾坤鼎需要24G或以上。(此为训练Erlangshen-MegatronBert-1.3B模型所需内存空间，训练轻量模型则可以更小。预测部署时空间消耗10G左右。)
  - 八卦炉需要8G或以上
- 存储空间
  - 乾坤鼎需要24G或以上。(此为训练Erlangshen-MegatronBert-1.3B标准模型所需硬盘空间，训练轻量模型则可以更小。预测部署时空间消耗10G左右)
  - 八卦炉需要8G或以上
- GPU
  - 训练Erlangshen-MegatronBert-1.3B的GPU是GeForce RTX ™ 3090 及以上，NVIDIA Ampere 架构。更好的V100,A100显卡可以大幅提升训练速度。
- 显存
  -  乾坤鼎需要24G或以上
  -  八卦炉需要8G或以上

## 不同环境下的测试效果

以fewCLUE榜单上的csldcp和iflytek任务为例进行测试(test acc项是test_public.json的准确率)：

乾坤鼎测试结果：

| 显卡 | 显存 | gradient checkpoint | optimizer | precision | 超参 | 任务 | 耗费时间(min) | total training step | final epoch | test acc |
| ---- | --- | ------------------- | --------- | --------- | ---- | --- | ------------- | ------------------- | ----------- | -------- |
| A100 | 40GB | False | AdamW | 32 | batch_size=2, max_len=768, accumulate_batch = 16, val_check_internal = 0.25 | csldcp  | 62 | 837 | 5 | 64.76 |
| A100 | 40GB | False | AdamW | 32 | batch_size=2, max_len=768, accumulate_batch = 16, val_check_internal = 0.25 | iflytek | 57 | 1450 | 6 | 54.15 |
| V100 | 32GB | True | AdamW | 32 | batch_size=4, max_len=768, accumulate_batch = 8, val_check_internal = 0.25 | csldcp | 168 | 837 | 6 | 64.59 |
| V100 | 32GB | True | AdamW | 32 | batch_size=4, max_len=768, accumulate_batch = 8, val_check_internal = 0.25 | iflytek | 165 | 1450 | 6 | 54.03 |
| 3090 | 24GB | True | Adafactor | 32 | batch_size=4, max_len=768, accumulate_batch = 8, val_check_internal = 0.25 | csldcp  | 381 | 837 | 24 | 64.87 |
| 3090 | 24GB | True | Adafactor | 32 | batch_size=4, max_len=768, accumulate_batch = 8, val_check_internal = 0.25 | iflytek | 217 | 1450 | 11 | 53.52 |
| 3090 | 24GB | True | Adafactor | 16 | batch_size=4, max_len=768, accumulate_batch = 8, val_check_internal = 0.25 | csldcp  | 217 | 837 | 17 | 64.20 |
| 3090 | 24GB | True | Adafactor | 16 | batch_size=4, max_len=768, accumulate_batch = 8, val_check_internal = 0.25 | iflytek | 231 | 1450 | 16 | 53.63 |

八卦炉文本分类测试结果
| 显卡 | 显存 | gradient checkpoint | optimizer | precision | 超参                      | 任务    | 真实显存占用(G) | 耗费时间(min) | total training step | final epoch | test acc |
| ---- | ---- | ------------------- | --------- | --------- | ------------------------- | ------- | --------------- | ------------- | ------------------- | ----------- | -------- |
| A100 | 40GB | FALSE               | AdamW     | 32        | batch_size=8, max_len=512 | csldcp  | 14.77           | 18            | 3184                   | 5        | 60.58    |
| A100 | 40GB | FALSE               | AdamW     | 32        | batch_size=8, max_len=512 | iflytek | 14.77           | 32            | 5494                   | 5        | 51.12    |
| V100 | 32GB | TRUE                | AdamW     | 16        | batch_size=8, max_len=512 | csldcp  | 5.25            | 25            | 3189                   | 5        | 61.74    |
| V100 | 32GB | TRUE                | AdamW     | 16        | batch_size=8, max_len=512 | iflytek | 5.25            | 38            | 5489                   | 5        | 51.63    |
| 3090 | 24G  | FALSE               | AdamW     | 16        | batch_size=8, max_len=512 | csldcp  | 15.36           | 25            | 3189                   | 5        | 60.74    |
| 3090 | 24G  | FALSE               | AdamW     | 16        | batch_size=8, max_len=512 | iflytek | 15.36           | 43            | 5494                   | 5        | 51.36    |
| 3090 | 24G  | TRUE                | AdamW     | 16        | batch_size=8, max_len=512 | csldcp  | 6.29            | 29            | 3184                   | 5        | 60.52    |
| 3090 | 24G  | TRUE                | AdamW     | 16        | batch_size=8, max_len=512 | iflytek | 6.29            | 50            | 5484                   | 5        | 51.1     |

八卦炉信息抽取测试结果

> 备注：zh_weibo/MSRA/OntoNote4/Resume为NER任务，其中MSRA在原始数据下进行测试；SanWen/FinRE作为实体关系联合抽取任务进行测试，非单一关系分类任务

| 显卡 | 显存 | gradient checkpoint | optimizer | precision | 超参                      | 任务    | 真实显存占用(G) | 耗费时间(min) | total training step | final epoch | test f1 |
| ---- | ---- | ------------------- | --------- | --------- | ------------------------- | ------- | --------------- | ------------- | ------------------- | ----------- | -------- |
| A100 | 40G  | TRUE                | AdamW     | 16        | batch_size=16, max_len=512 | zh_weibo | 6.32            | 5            | 960                   | 12        | 0.6756     |
| A100 | 40G  | TRUE                | AdamW     | 16        | batch_size=16, max_len=512 | MSRA | 21.54            | 39            | 15480                   | 6        | 0.9337     |
| A100 | 40G  | TRUE                | AdamW     | 16        | batch_size=16, max_len=512 | OntoNote4 | 13.95            | 19            | 6380                   | 7        | 0.8653     |
| A100 | 40G  | TRUE                | AdamW     | 16        | batch_size=16, max_len=512 | Resume | 7.26            | 17            | 5000                   | 21        | 0.9614     |
| A100 | 40G  | TRUE                | AdamW     | 16        | batch_size=16, max_len=512 | SanWen | 9.25            | 23            | 9680                   | 9        | 0.2704     |
| A100 | 40G  | TRUE                | AdamW     | 16        | batch_size=16, max_len=512 | FinRE | 5.60           | 18            | 3060                   | 8        | 0.4692     |

## 软件环境要求

建议您使用我们打包好的Docker镜像，如果您需要直接使用源码，请使用`python>=3.7+`并且需要满足下列的软件安装依赖：

```python
fastapi==0.86.0
numpy==1.22.3
psutil==5.8.0
pydantic==1.10.2
pynvml==11.0.0
pytorch_lightning==1.7.6
scikit_learn==1.1.3
setuptools==58.0.4
starlette==0.20.4
torch==1.11.0+cu113
tqdm==4.62.3
transformers==4.18.0
uvicorn==0.19.0
python-multipart==0.0.5
sentence-transformers==2.2.2
LAC==2.1.2
textda==0.1.0.6
```
