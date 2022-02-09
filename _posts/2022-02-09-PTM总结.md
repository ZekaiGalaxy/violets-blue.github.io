**Title:  PTM总结**

**Description:  对于NLP预训练模型主要贡献和创新之处的归纳与总结**

**Author: zekai**

**Contact:  justinzzk2002@163.com**





------



> 本文档包含
>
> * BERT强化：
>
>   RoBERTa,ALBERT,SpanBERT,MacBERT,DeBERTa,BART,DistillBERT
>
> * 改进Transformer：
>
>   SparseTransformer,RealFormer
>
> * 另辟蹊径：
>
>   ERNIEv1.0,ERNIEv2.0,ELECTRA,GPT1,GPT2,GPT3,XLNet,T5,UniLM,MASS







------



##### 《RoBERTa: A Robust Optimized BERT Pretraining Approach》

【更强的BERT】

* 动态MASK
  * 不在预处理的时候MASK，而是在提供输入的时候动态MASK

* 去掉NSP
* BPE
  * word-level+char-level

------



##### 《ALBERT: A Lite BERT》

【轻量BERT】

* Embedding时矩阵分解
  * O(VH)->O(VE)+O(EH) E<<H

* 参数共享
  * 动机：实验表明不同层attention关注差不多的内容
  * attention,ffn参数共享

* SOP(sentence order prediction)取代NSP
  * 动机：原来任务太简单
  * 给定句子对，判别一个句子在另一个句子前还是后
  * 任务变难，dropout不再需要

------



##### 《ERNIEv1.0: Enhanced Representation through Knowledge Integration》

【百度中文BERT】

* Knowledge Masking
  * 动机：通过mask的方式来引导计算机学会知识
  * 三个阶段
    * basic-level masking
    * phrase-level masking
    * entity-level masking

* DLM task(Dialogue Language Model)
  * 多轮对话的形式
  * 响应的mask：QRQQR

------



##### 《ERNIEv2.0: A Continual Pre-Training Framework for Language Understanding》

【百度中文2.0】

* 连续多任务学习
  * 不满足于堆叠任务，而是希望连续学习，要避免忘记以前
  * Sequential MultiTask Learning
    * 第一轮训练任务1，没完就停止
    * 第二轮训练任务12
    * 第三轮训练任务123...

* 训练的任务：

  * 词法层级任务
    * 大小写预测：对NER用帮助
    * Knowledge Masking
    * 词频关系：预测是不是关键词

  * 语法层级任务
    * 句子排序
    * 句子距离预测：0相邻，1同一文章，2不同文章

  * 语义层级任务
    * 预测提问与标题是否相关
    * 信息检索任务
    * 篇章句任务

------



##### 《ELECTRA: Efficiently Learning on Encoder that Classifies Token Replacements Accurately》

【加了判别器的BERT】

* 通过判别器把MLM任务转换为二分类任务
  * Generator和Discriminator都是BERT
  * 判别器任务是Replaced Token Detection,判断哪个词被替换过
  * 训练方法
    * 第一步训练D，最大化正确性
    * 第二部固定D，训练G，最小化D的预测概率
    * 最后模型只有G，把D扔掉

------



##### 《GPT: Generative Pre-Training》

【半监督的Transformer范式】

* 半监督
  * 无监督pretrain+有监督finetune
  * 与ELMo有区别：先以无监督学到特征，然后喂给有监督模型

* Transformer结构
  * 相对于ELMo的双向LSTM更加鲁棒

------



##### 《GPT2: Generative Pre-Training2》

【GPT更大更强+微调】

* 博览群书
  * 不再根据任务分别finetune，而是让模型自动识别是什么任务
  * 更大数据集和网络参数

* 微调Transformer
  * 把LayerNorm放在前面
  * 动机：这样word embedding就有直接连到最后的残差连接

------



##### 《GPT3: Generative Pre-Training3》

【暴力出奇迹】

* In Context Learning
* Fewshot,Zeroshot 

------



##### 《SparseTransformer》

【稀疏注意力】

* Stride Attention(每隔k个来一次)+Local Attention(相邻2k+1个)
* 减少模型复杂度

------



##### 《RealFormer: Residual Attention Layer Transformer》

【残差attention】

* LayerNorm
  * PreLN:对梯度下降更友好，GPT2
  * PostLN:性能更好，Bert

* 只对attention用残差+PreLN结构
  $$
  A_{n}=\frac{Q_nK_n^t}{\sqrt{d_k}}+A_{n-1}
  $$
  

------



##### 《XLNet: Generalized Autoregressive Pretraining for Language Understanding》

【Permutation BERT】

* PLM(Permutation Language Model)
  * BERT用了双向，但是MASK造成训练和输出时的gap
  * GPT是AR自回归范式，BERT是AE自编码范式，PLM统一两者
  * Two Stream Self Attention
    * 把含有位置信息的state称为g，作为query
    * 把含有内容信息的state称为h，作为key和value
    * 不同的attention mask来实现permutation

* Transformer-XL的技巧

  * Segment Recurrence Mechanism
    * 段循环，把前一个段计算完的hidden state保存到memory，与当前的h拼接来获得更长的上下文信息

  * Relative Positional Encoding
    * 相对位置编码，其实是为了解决段循环中的embedding问题
    * 同样还有Relative Segment Encoding

------



##### 《SpanBERT: Improving Pre-training by Representing and Predicting Spans》

【Span MASK+SBO】

* Span MASK:从几何分布采样长度，每次掩盖掉一系列词而非单个单词

* SBO(Span Boundary Objective)

  * Span MASK怎么计算loss的补充

  * 预测被mask的相对位置i的词语为
    $$
    y_i=f(x_s,x_e,p_{i-s+1})
    $$
    并计算相应loss

------



##### 《MacBERT: MLM as correction BERT》

【中文同义词BERT】

* 用近义词来代替MASK来减少gap
* SOP(句子顺序预测)取代NSP

------



##### 《DeBERTa: Decoding-enhanced BERT with disentangled attention》

【分离注意力的BERT】

* Disentangled attention
  * 把基于内容和位置的注意力展开，发现是c2c,p2p,p2c,c2p(content&position)
  * 使用相对位置表示

* Enhanced Mask Decoder
  * 分离注意力只考虑了内容和相对位置
  * 在对MASK进行decode的时候加入绝对位置的信息

------



##### 《BART: Denoising Sequence-to-Sequence Pre-training》

【noisy BERT】

* 通过不同的方式来破坏输入
  * Token Masking
  * Sentence Permutation
  * Document Rotation
  * Token Deletion
  * Text Infilling

------



##### 《DistillBERT: a distilled version of BERT》

【蒸馏过的BERT】

* 把原始BERT作为teacher
* 减小编码器层数

------



##### 《T5: Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer》

【所有任务都是text2text】

* 用搜索的方式来得到最好模型

  * Transformer的模型架构

    * Encoder-Decoder
      * Encoder能看到全体，Decoder只能看到之前的

    * Decoder
      * 只能看到之前的信息

    * Prefix LM
      * Encoder和Decoder融合体

    * 注意：上述都用的是Transformer只改变attention MASK的形状

  * 预训练方法：BERT-style最好
    * 其他：LM(GPT),打乱还原

  * 破坏策略：小段替换最好
    * 其他：Mask，drop

  * 百分比和长度：15%，3

------



##### 《UniLM: Unified Language Model Pre-training》

【统一模型】

* 不同目标，但网络参数共享，可以避免过拟合
* 不同的模型：
  * 单项语言模型
  * 双向语言模型
  * 序列到序列(src和tgt都被替换成MASK)
  * 不同的模型只需要用不同的attention mask即可

* 因为囊括了所有预训练方式，所以在NLU和NLG上都有很好表现(因为还有seq2seq)

------



##### 《MASS: Masked Sequence to Sequence Pre-training for Language Generation》

【seq2seq pretraining】

* 在encoder的时候mask掉一些单词，在decoder的时候用seq2seq预测mask掉的单词且decoder不能看见没被mask掉的单词

------

