# IPRE_Model
此项目为本科毕业设计所研究课题，即用传统实体关系抽取模型跑IPRE数据集并比较结果，在本毕业设计中也提出了新的模型，比其他本文中所用模型更快，但是同时损失了一定的精度，总体来说仍然不错

对于IPRE数据集，集成CNN，PCNN-one，BiLSTM，Bert以及自己设计的LBert模型的代码文件

## 实现总览
环境：
- python 3.7.6
- pytorch 1.1.0
- jieba
- numpy
- gensim #用于word2vec预训练

简单介绍主要目录：
```
├── data             # 数据集目录
| ├── dev             # 验证集
| | ├── bag_relation_dev.txt             # 以包形式存储的验证集语句
| | ├── sent_dev.txt             # 验证集语句
| | ├── sent_relation_dev.txt             # 验证集对应标签
| ├── test             # 测试集
| | ├── bag_relation_test.txt
| | ├── sent_test.txt
| | ├── sent_relation_test.txt
| ├── train             # 训练集
| | ├── bag_relation_train.txt
| | ├── sent_train.txt
| | ├── sent_relation_train.txt
| ├── relation2id.txt # tag的id对应的关系列表
├── word2vec.bin          # 预训练得到的word2vec词向量文件
├── IPRE_Model.ipynb          # 所有的代码都包括在了这个jupytor文件中
```
### 关于数据
#### IPRE数据集
IPRE数据集是2019年苏州大学提出的中文的，关于人物关系的实体关系抽取数据集，内涵41000条人工标注的人物关系数据，以及大量机器远程监督得到的空标签数据集

#### Word2Vec预训练数据集
采用了2019年维基百科中文词条，引用自
[nlp_chinese_corpus](https://github.com/brightmart/nlp_chinese_corpus)
经过实验结果验证，该数据集其实跟本毕业设计所采用的IPRE数据集的单词分布相差较大，很多单词仍然是missing的，使用者可以自行尝试去找更加合适的数据集

### 模型
此次毕业设计主要运用了PCNN-on，BiLSTM，Bert，CNN等模型，都是参考了相关的工作自行编写代码设计的。另外提出了自己的LBert模型，损失精度提升了效率
以下是主要参考文献：
- PCNN:Distant Supervision for Relation Extraction via Piecewise Convolutional Neural Networks.
- Bert:Pre-training of deep bidirectional transformers for language understanding.
- BiLSTM:Attention-Based Bidirectional Long Short-Term Memory Networks for Relation Classification
CNN模型是魔改了PCNN模型得到
为了避免制造更多信息垃圾，Bert模型使用者请自行下载，链接为[Bert下载](https://huggingface.co/bert-base-chinese)

### 数据预处理
项目代码中对于数据的预处理是在load的时候完成的，因此对于数据集本身不造成更改。
做的预处理为 将实体对用 （#前者，#后者）的形式代替，是为了防止出现看人名就辨认出关系的过拟合情况

### 调参优化
在完成项目的过程中尝试了很多的东西，虽然大多数都是负面优化，但是本人仍然认为在不同的场景下能引发新的效果
- 优化函数使用的是`Adam`，用`SGD`也是可以的，但是会慢一些，然而为了防止过拟合，为啥不去尝试一下这种小火慢炖呢（笑
- 在PCNN以及CNN的代码中对于一个句子的分段Max pooling处理貌似有更加快速的带mask的方法，但是还没有去实现
- 由于数据集标签分布是非常不均衡的，所以项目代码中包含被注释掉的关于欠采样，过采样处理的代码，使用者可以自行尝试调参取得较好的效果。注意如果要用，需要将训练的dataloader的shuffle选项置为`'False'`
- 虽然模型训练中使用的是最常见的交叉熵，但仍然尝试过`Focal Loss`以及依据总体标签分布以及一个Batch含有的标签分布对训练的标签添加权重的代码，可以自行参考琢磨
- 甚至突发奇想设置了一个`F1 Loss`，但是效果貌似不太好，所以也没用，代码里也有

### 使用
此项目从数据读入到训练等都是用一个Jupytor文件完成，其中模型都经过一个代码块进行封装，可以找到相应的代码块自行更改以及调试
