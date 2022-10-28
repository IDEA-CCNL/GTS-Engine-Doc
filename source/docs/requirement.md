# 环境需求

GTS Engine目前支持两种使用方式：

- 使用Docker加载镜像，启动服务；（推荐）
- 直接克隆源码，在您自己的开发环境中启动；

## 硬件环境要求

- CPU
  - 支持x86_64（或称作 x64、Intel 64、AMD64）架构，不支持arm64架构。
- 内存
  - 24G或以上。(此为训练Erlangshen-MegatronBert-1.3B模型所需内存空间，训练轻量模型则可以更小。预测部署时空间消耗10G左右。)
- 存储空间
  - 24G或以上。(此为训练Erlangshen-MegatronBert-1.3B标准模型所需硬盘空间，训练轻量模型则可以更小。预测部署时空间消耗10G左右)
- GPU
  - 训练Erlangshen-MegatronBert-1.3B的GPU是GeForce RTX ™ 3090 及以上，NVIDIA Ampere 架构。更好的V100,A100显卡可以大幅提升训练速度。
- 显存
  - 24G及以上

## 不同环境下的测试效果

以fewclue榜单上的csldcp和iflytek任务为例进行测试：


| 显卡  | 显存  | gradient checkpoint | Optimizer | precision | 超参                                                                        | 任务    | 耗费时间(min)train/eval/knn | total training step | final epoch | test public acc | knn acc |
| ------- | ------- | --------------------- | ----------- | ----------- | ----------------------------------------------------------------------------- | --------- | ----------------------------- | --------------------- | ------------- | ----------------- | --------- |
| A100  | 40GB  | False               | AdamW     | 32        | batch_size=2, max_len=768, accumulate_batch = 16，val_check_internal = 0.25 | csldcp  | 48, 2 ,12                   | 837                 | 5           | 61.74           | 64.76   |
| <br/> | <br/> | <br/>               | <br/>     | <br/>     | <br/>                                                                       | iflytek | 48, 3, 6                    | 1450                | 6           | 52.37           | 54.15   |
| V100  | 32GB  | True                | AdamW     | 32        | batch_size=4, max_len=768, accumulate_batch = 8，val_check_internal = 0.25  | csldcp  | (42, 2, 12) * 3             | 837                 | 6           | 61.74           | 64.59   |
| <br/> | <br/> | <br/>               | <br/>     | <br/>     | <br/>                                                                       | iflytek | (46, 3, 6) *3               | 1450                | 6           | 52.94           | 54.03   |
| 3090  | 24GB  | True                | Adafactor | 32        | batch_size=4, max_len=768, accumulate_batch = 8，val_check_internal = 0.25  | csldcp  | 346, 6, 29                  | 837                 | 24          | 61.06           | 64.87   |
| <br/> | <br/> | <br/>               | <br/>     | <br/>     | <br/>                                                                       | iflytek | 195, 7 ,15                  | 1450                | 11          | 51.06           | 53.52   |
| <br/> | <br/> | <br/>               | <br/>     | 16        | <br/>                                                                       | csldcp  | 182, 6, 29                  | 837                 | 17          | 59.50           | 64.20   |
| <br/> | <br/> | <br/>               | <br/>     | <br/>     | <br/>                                                                       | iflytek | 209, 7, 15                  | 1450                | 16          | 51.74           | 53.63   |

注：

## 软件环境要求

建议您使用我们打包好的Docker镜像，如果您需要直接使用源码，请使用python=3.8并且需要满足下列的软件安装依赖：

```python
numpy
pandas
sklearn
scipy
matplotlib
torch>=1.10.0
pytorch-lightning==1.5.10
protobuf==3.6.0
transformers==4.18.0
roformer>=0.4.0
loralib
decorator
rich
web.py
```
