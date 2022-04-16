# EasyNLP: A Comprehensive and Easy-to-use NLP Toolkit

<p align="center">
    <br>
    <img src="https://cdn.nlark.com/yuque/0/2022/png/2480469/1649317417481-d20971cd-cd4f-4e29-8587-c342a128b762.png" width="200"/>
    <br>
<p>
    
<p align="center"> <b> EasyNLP is a Comprehensive and Easy-to-use NLP Toolkit </b> </p>
<p align="center">
    <a href="https://www.yuque.com/easyx/easynlp/iobg30">
        <img src="https://cdn.nlark.com/yuque/0/2020/svg/2480469/1600310258840-bfe6302e-d934-409d-917c-8eab455675c1.svg" height="24">
    </a>
    <a href="https://dsw-dev.data.aliyun.com/#/?fileUrl=https://raw.githubusercontent.com/alibaba/EasyTransfer/master/examples/easytransfer-quick_start.ipynb&fileName=easytransfer-quick_start.ipynb">
        <img src="https://cdn.nlark.com/yuque/0/2020/svg/2480469/1600310258886-ad896af5-b7da-4ca6-8369-4b14c23cb7a3.svg" height="24">
    </a>
</p>

EasyNLP is an easy-to-use NLP development and application toolkit in PyTorch, first released inside Alibaba in 2021. It is built with scalable distributed training strategies and supports a comprehensive suite of NLP algorithms for various NLP applications. EasyNLP integrates knowledge distillation and few-shot learning for landing large pre-trained models and provides a unified framework of model training, inference, and deployment for real-world applications. It has powered more than 10 BUs and more than 20 business scenarios within the Alibaba group. It is seamlessly integrated to [Platform of AI (PAI)](https://www.aliyun.com/product/bigdata/product/learn) products, including PAI-DSW for development, PAI-DLC for cloud-native training, PAI-EAS for serving, and PAI-Designer for zero-code model training.

# Main Features

- **Easy to use and highly customizable:** In addition to providing easy-to-use and concise commands to call cutting-edge models, it also abstracts certain custom modules such as AppZoo and ModelZoo to make it easy to build NLP applications. It is equipped with the PAI PyTorch distributed training framework TorchAccelerator to speed up distributed training.
- **Compatible with open-source libraries:** EasyNLP has APIs to support the training of models from Huggingface/Transformers with the PAI distributed framework. It also supports the pre-trained models in [EasyTransfer](https://github.com/alibaba/EasyTransfer) ModelZoo.
- **Knowledge-injected pre-training:** The PAI team has a lot of research on knowledge-injected pre-training, and builds a knowledge-injected model that wins first place in the CCF knowledge pre-training competition. EasyNLP integrates these cutting-edge knowledge pre-trained models, including DKPLM and KGBERT.
- **Landing large pre-trained models:** EasyNLP provides few-shot learning capabilities, allowing users to finetune large models with only a few samples to achieve good results. At the same time, it provides knowledge distillation functions to help quickly distill large models to a small and efficient model to facilitate online deployment.

# Installation

You can either install it from pip

```bash
$ pip install pai-easynlp (to be released)
```

or setup from the source：

```bash
$ git clone https://github.com/alibaba/EasyNLP.git
$ cd EasyNLP
$ python setup.py install
```

This repo is tested on Python3.6, PyTorch >= 1.8.

# Quick Start

Now let's show how to use just a few lines of code to build a text classification model based on BERT.

```python
from easynlp.core import Trainer
from easynlp.appzoo import ClassificationDataset, SequenceClassification
from easynlp.utils import initialize_easynlp

args = initialize_easynlp()

train_dataset = ClassificationDataset(
    pretrained_model_name_or_path=args.pretrained_model_name_or_path,
    data_file=args.tables,
    max_seq_length=args.sequence_length,
    input_schema=args.input_schema,
    first_sequence=args.first_sequence,
    label_name=args.label_name,
    label_enumerate_values=args.label_enumerate_values,
    is_training=True)

model = SequenceClassification(pretrained_model_name_or_path=args.pretrained_model_name_or_path)
Trainer(model=model,  train_dataset=train_dataset).train()
```

Then you can run the code:

```bash
python main.py \
  --mode train \
  --tables=train_toy.tsv \
  --input_schema=label:str:1,sid1:str:1,sid2:str:1,sent1:str:1,sent2:str:1 \
  --first_sequence=sent1 \
  --label_name=label \
  --label_enumerate_values=0,1 \
  --checkpoint_dir=./tmp/ \
  --epoch_num=1  \
  --app_name=text_classify \
  --user_defined_parameters='pretrain_model_name_or_path=bert-tiny-uncased'
```

You can also use AppZoo Command Line Tools to quickly train an App model. Take text classification on SST-2 dataset as an example. First you can download the [train.tsv](http://atp-modelzoo-sh.oss-cn-shanghai.aliyuncs.com/release/tutorials/classification/train.tsv), and [dev.tsv](http://atp-modelzoo-sh.oss-cn-shanghai.aliyuncs.com/release/tutorials/classification/dev.tsv), then start training:

```bash
$ easynlp \
   --mode=train \
   --worker_gpu=1 \
   --tables=train.tsv,dev.tsv \
   --input_schema=label:str:1,sid1:str:1,sid2:str:1,sent1:str:1,sent2:str:1 \
   --first_sequence=sent1 \
   --label_name=label \
   --label_enumerate_values=0,1 \
   --checkpoint_dir=./classification_model \
   --epoch_num=1  \
   --sequence_length=128 \
   --app_name=text_classify \
   --user_defined_parameters='pretrain_model_name_or_path=bert-small-uncased'
```

And then predict:

```bash
$ easynlp \
  --mode=predict \
  --tables=dev.tsv \
  --outputs=dev.pred.tsv \
  --input_schema=label:str:1,sid1:str:1,sid2:str:1,sent1:str:1,sent2:str:1 \
  --output_schema=predictions,probabilities,logits,output \
  --append_cols=label \
  --first_sequence=sent1 \
  --checkpoint_path=./classification_model \
  --app_name=text_classify
```

To learn more about the usage of AppZoo, please refer to our [documentation](https://www.yuque.com/easyx/easynlp/psm6fr).

# Landing Pre-trained Models
EasyNLP provide few-shot learning and knowledge distillation to help land large pre-trained models.

1. PET
2. CP-Tuning
3. Vanilla KD
4. Meta KD
5. Data Augmentation

# ModelZoo Model Architecture
EasyNLP currently provides the following models in ModelZoo:

1. PAI-BERT-zh (from Alibaba PAI): pretrain BERT with a large Chinese corpus
1. DKPLM (from Alibaba PAI): pretrain BERT with Decomposable Knowledge
1. KGBERT (from Alibaba): pretrain BERT with Knowledge
1. BERT (from Google): Original BERT
1. RoBERTa (from Facebook)
1. Chinese RoBERTa (from HFL)
1. MacBERT (from HFL)
1. WOBERT 
1. FashionBERT (from Alibaba PAI): In progress.
1. GEEP (from Alibaba PAI): In progress.

Please refer to this [readme](https://github.com/alibaba/EasyNLP/blob/master/easynlp/modelzoo/README.md) for the usage of these models in EasyNLP.
Meanwhile, EasyNLP supports to load pretrained models from Huggingface/Transformers, please refer to [this tutorial](https://www.yuque.com/easyx/easynlp/qmq8wh) for details.

# Tutorials

- [自定义文本分类示例](https://www.yuque.com/easyx/easynlp/ds35qn)
- [QuickStart-文本分类](https://www.yuque.com/easyx/easynlp/rxne07)
- [QuickStart-PAI DSW](https://www.yuque.com/easyx/easynlp/gvat1o)
- [QuickStart-MaxCompute/ODPS数据](https://www.yuque.com/easyx/easynlp/vdt5ze)
- [AppZoo-文本向量化](https://www.yuque.com/easyx/easynlp/ts4czl)
- [AppZoo-文本分类/匹配](https://www.yuque.com/easyx/easynlp/vgbopy)
- [AppZoo-序列标注](https://www.yuque.com/easyx/easynlp/qkwqmb)
- [AppZoo-GEEP文本分类](https://www.yuque.com/easyx/easynlp/lepm0q)
- [基础预训练实践](https://www.yuque.com/easyx/easynlp/lm1a5t)
- [知识预训练实践](https://www.yuque.com/easyx/easynlp/za7ywp)
- [知识蒸馏实践](https://www.yuque.com/easyx/easynlp/ffu6p9)
- [跨任务知识蒸馏实践](https://www.yuque.com/easyx/easynlp/izbfqt)
- [小样本学习实践](https://www.yuque.com/easyx/easynlp/ochmnf)
- [Rapidformer模型训练加速实践](https://www.yuque.com/easyx/easynlp/bi6nzc)
- API docs: [http://atp-modelzoo-sh.oss-cn-shanghai.aliyuncs.com/release/easynlp/easynlp_docs/html/index.html](http://atp-modelzoo-sh.oss-cn-shanghai.aliyuncs.com/release/easynlp/easynlp_docs/html/index.html)

# License
This project is licensed under the [Apache License (Version 2.0)](https://github.com/alibaba/EasyNLP/blob/master/LICENSE). This toolkit also contains some code modified from other repos under other open-source licenses. See the [NOTICE](https://github.com/alibaba/EasyNLP/blob/master/NOTICE) file for more information.

# ChangeLog

- EasyNLP v0.0.3 was released in 01/04/2022. Please refer to [tag_v0.0.3](https://github.com/alibaba/EasyNLP/releases/tag/v0.0.3) for more details and history.


# Contact Us

Scan the following QR codes to join Dingtalk discussion group. The group discussions are mostly in Chinese, but English is also welcomed.

<img src="https://cdn.nlark.com/yuque/0/2022/png/2480469/1649324662278-fe178523-6b14-4eff-8f50-7abbf468f751.png?x-oss-process=image%2Fresize%2Cw_357%2Climit_0" width="300"/>

# Reference

- EasyTransfer: https://github.com/alibaba/EasyTransfer
- DKPLM: https://arxiv.org/abs/2112.01047
- MetaKD: https://arxiv.org/abs/2012.01266
- CP-Tuning: https://arxiv.org/abs/2204.00166
- FashionBERT: https://arxiv.org/abs/2005.09801
