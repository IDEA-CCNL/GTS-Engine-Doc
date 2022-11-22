# 自定义开发

您可以在我们代码的基础上进行二次开发，自定义您的任务pipeline或者增加新的任务。

## 增加新的任务

### 定义输入输出

首先，你需要自己定义新任务的输入格式和输出格式，目前Engine仅支持每行一个json数据的格式，您的输入输出也要满足这个要求。

以文本分类任务为例，定义**输入字段**为：

- 数据文件
```json
{
    "content": "a sentence",
    "label": "a label"
}
```

- 标签文件
```json
{
    "labels": ["label1", "label2", "label3"]
}
```

定义输出字段为：

```json
{
    "predictions": ["pred_label1", "pred_label2"],
    "probabilities": [[...], [...]]
}
```

### 定义你的pipeline

在gts_engine/pipelines模块中，以`{engine_type}_{task_type}.py`的命名规则新建一个新任务的文件，其中，`engine_type`需要为`qiankunding`或者`bagualu`。

在`{engine_type}_{task_type}.py`中，需要导入registry并用registry装饰你的pipeline函数。

同时，需要实现以下三个函数：

```python
from gts_common.registry import PIPELINE_REGISTRY


@PIPELINE_REGISTRY.register(suffix=__name__)
def train_pipeline(args):
    # implement your task pipeline here

@PIPELINE_REGISTRY.register(suffix=__name__)
def prepare_inference(save_path):
    # implement your inference preparation codes here
    # return a dict named 'inference_suite' including all models and data your need to do inference with 
    inference_suite = {}
    return inference_suite

@PIPELINE_REGISTRY.register(suffix=__name__)
def inference(samples, inference_suite):
    # implement your inference codes here
    # return a dict including inference results
    result = {}
    return result
```

由于使用了装饰器将你的训练和推理的函数注册到了`PIPELINE_REGISTRY`，在启动训练和推理的时候，我们会根据`engine_type`和`task_type`动态调用对应任务的训练和推理函数。

这样你就只需要实现自定义的训练和推理逻辑，就可以进行自定义扩展，无需关心外部调用细节。

#### train_pipeline

在这个函数中定义你的训练流水线，输入参数是`gts_engine_train.py`中通过argparse解析出来的参数，可以在`args`中定义你高阶的训练参数；

**注意**，需要将你推理时用到的配置、数据和模型统一都放到`args.save_path`路径下；

**注意**，训练数据和测试数据的格式定义即你自己定义的格式；

#### prepare_inference

在这个函数中，加载推理时需要的模型和其他数据，以一个字典的格式返回，你需要自己定义`inference_suite`中的key-value，如：

```python
inference_suite = {
    "model": model,
    "args": args,
}
```

#### inference

在这个函数中，实现你的推理逻辑；

`samples`是一个list，list中的每个item是一个dict，dict的数据结构即你自己定义的数据文件的数据结构；

`inference_suite`是`preprare_inference`函数的返回值，包括推理时需要的模型和其他数据；

返回值`result`是一个dict，dict的字段需要和你定义的输出字段一致；



至此，就可以开始通过启动`gts_engine_service`以及调用GTS Engine Client测试你自定义的功能了。