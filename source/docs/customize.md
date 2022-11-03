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

### 训练代码

- 在`gts_engine_service/train.py`中实现一个pipeline，命名为`your_task_pipeline`，例如`classification_pipeline`；
- **注意**，需要将你推理时用到的配置、数据和模型统一都放到`args.save_path`路径下；
- **注意**，训练数据和测试数据的格式定义即你自己定义的格式；
- 在`main`函数中添加你的训练pipeline，并给你的任务命名一个唯一的`task_type`；

### 推理代码

- 在`gts_engine_service/inference.py`中实现两个接口
    - 加载推理文件的接口`prepare_your_task_inference`，例如`prepare_classification_inference`；
    - 推理的接口`your_task_inference`，例如`classification_inference`；
- **注意**，训练数据和测试数据的格式定义、输出预测结果的格式定义即你自己定义的格式；
- 在`preprare_inference`和`inference_samples`函数中分别添加你的接口，并给你的任务命名一个唯一的`task_type`；

至此，就可以开始通过启动GTS Engine Service以及调用GTS Engine Client测试你自定义的功能了。