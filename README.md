# 微控制器的关键字识别

该存储库包含使用的tensorflow模型和训练脚本
在论文中：
[Hello Edge：微控制器上的关键字识别]（https://arxiv.org/pdf/1711.07128.pdf）。
这些脚本改编自[Tensorflow示例]（https://github.com/tensorflow/tensorflow/tree/master/tensorflow/examples/speech_commands）
为了使这些脚本自成一体，这里重复了一些。

要训练具有3个完全连接层的DNN，每层有128个神经元，请运行：

```
python train.py --model_architecture dnn --model_size_info 128 128 128
```
命令行参数*  -  model_size_info *用于传递神经网络层
尺寸，如层数，卷积滤波器大小/步幅作为models.py的列表，
它基于提供的模型架构构建张量流图
和图层尺寸。
有关每个网络体系结构的* model_size_info *的更多信息，请参阅
[models.py]（models.py）。
所有超参数的训练命令都可以重现所显示的模型
[文件]（https://arxiv.org/pdf/1711.07128.pdf）给出了[here]（train_commands.txt）。

要从train / val / test set上的检查点对训练模型进行推断，请运行：
```
python test.py --model_architecture dnn --model_size_info 128 128 128 --checkpoint
<checkpoint path>
```

要将训练好的的模型检查点冻结到.pb文件中，请运行：
```
python freeze.py --model_architecture dnn --model_size_info 128 128 128 --checkpoint
<checkpoint path> --output_file dnn.pb
```

## 预训练模型

针对不同神经网络架构（如DNN）的训练模型（.pb文件），
CNN，基本LSTM，LSTM，GRU，CRNN和DS-CNN如图所示
这[arXiv paper]（https://arxiv.org/pdf/1711.07128.pdf）已添加进来
[Pretrained_models]（Pretrained_models）。验证集上模型的准确性，
他们的内存需求和每个推理的操作也总结在下表。

![image](https://github.com/BgLoveXixi/Keyword_recognition/blob/master/.idea/model_result_info.png)

要通过训练模型（例如DNN）运行音频文件并获得最高预测，请运行：
```
python label_wav.py --wav <audio file> --graph Pretrained_models / DNN / DNN_S.pb
--labels Pretrained_models / labels.txt --how_many_labels 1
```

## 微控制器的量化指南和部署

关于量化KWS神经网络模型的快速指南是[Deployment/ Quant_guide.md）
在[Deployment]中还提供了在Cortex-M开发板上运行DNN模型的示例代码。
