#### 写文章团队/该团队的研究方向及进展

Antoine Bordes, Jason Weston, and Sumit Chopra

#### 该文章的主要贡献/提出模型的特征及优势

#### 该模型结构框架/核心算法

#### 模型结果/实验标准/数据集简要说明



#### 心得体会



#### 笔记

abstract:

1. a system which learns to answer questions on  a broad range of topics from a knowledge base using handcrafted features
2. **low-dimensional embeddings of words and knowledge base constituents** ->used to score natural language questions against candidate answers

introduction

1. Problem: scale and the difficulty for machines to interpret natural language still makes this task a challenging problem

2. 现阶段在open QA中的技术主要可以分为两类：

   1. 基于信息检索(info retrieval):

      ​	基于信息检索的技术首先通过查询知识库的搜索API并将问题转换为有效查询来检索广泛的候选答案，然后使用细粒度的检测试探法来确定确切的答案

   2. 基于语义解析(semantic parsing)

      ​	基于语义解析的方法集中在语义解析系统对问题含义的正确解释上。正确的解释会将问题转换为返回正确答案的确切数据库查询

   **一个靠数量，一个靠质量**

​	**两者虽然都可以操控大型KBs，但都需要需要专家手工制作词典，语法和知识库架构才能工作。这种不可忽视的人工干预可能不够通用，无法方便地扩展到具有其他模式，更广泛的词汇表或英语以外的其他语言的新数据库。**

本文:

1. proposed a framework for open QA requiring almost no human annotation-->this method is outperformed by other competing methods

2. Introduced an embedding model which learns low-dimensional vector representations of words and symbols(such as KBs constituents)

   -->可以在比[6]的系统更少的监督下进行训练，同时能够实现更好的预测性能。
   但是，该方法仅与[6]进行了比较，后者在简化的环境下运行，尚未在更实际的条件下应用，也未针对最佳性能的方法进行评估。

   

3. 在本文中，我们通过提供回答更复杂问题的能力来改进[5]的模型。 
   本文的主要贡献是：（1）更复杂的推理过程既有效又可以考虑更长的路径（[5]仅考虑直接与图中问题相关的答案）； 
   （2）答案的更丰富的表示形式，用于对问题的答案路径和知识库的周围子图进行编码。
   我们的方法可以与最新基准 WebQuestions [1]上的最新技术相竞争，而无需像大多数其他系统那样在训练过程中使用任何词典，规则或其他系统来进行词性标记，句法或依存关系解析。

   

**Task Definition**

1. main motivation: **provide a system for open QA able to be trained** **as long as it has access to:** 

   **(1) a training set of questions paired with answers** 

   **(2) a KB providing a structure among answers.**

   *suppose that all potential answers are entities in the KB（每个回答都是知识库中的实体） and that questions are sequences of words（问题是包含一个已定义的知识库实体的文字序列） that include one identiﬁed KB entity. When this entity is not given, plain string matching is used to perform entity resolution(如果没有给出这样的KB实体，则用纯字符串匹配来执行实体解析). Smarter methods could be used but this is not our focus.*

   

2. 使用WEBQUESTIONS作为评测标准（这个数据集包含的训练样本很少因此无法单独学习）

   **WEBQUESTIONS**：

   该数据集使用Freebase作为知识库构建，包含**5,810**个问题-答案对。它是通过以下方式创建的：通过Google Suggest API抓取问题，然后使用Amazon Mechanical Turk获得答案。我们使用原始分割（3,778个示例用于训练，2,032个示例用于测试），并从训练集中分离出1000个问题进行验证。 
   **WEBQUESTIONS** 建立在Freebase上，因为所有答案都定义为Freebase实体。在每个问题中，我们使用问题单词与Freebase中实体名称之间的字符串匹配来标识一个Freebase实体。当同一字符串与多个实体匹配时，仅保留出现在最多三元组（即Freebase中最受欢迎的实体）的实体。
   数据集中的示例问题（答案）

   “埃德加·爱伦·坡死于何处？” 
   （巴尔的摩）

   “巴拉克·奥巴马（Barack Obama）获得了什么学位？” 
   （文学学士，法学博士）。

   