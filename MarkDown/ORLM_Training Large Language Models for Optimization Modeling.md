# ORLM:Training Large Language Models for Optimization Modeling
## ***摘要***
#### 研究背景
大型语言模型提供了自动化优化建模的能力，已成为处理复杂运筹学问题的有力工具。
#### 目前缺陷
目前的方法严重依赖于具有专有LLM的快速工程，这引起了工业应用中可能令人望而却步的**数据隐私问题**
#### 解决方法
训练了开源大语言模型进行优化建模，确定了大语言模型训练数据集的四个关键要求，设计并实现了OR-INSTRUCT（**一种*半自动化流程*，用于创建适合要求的训练数据**），介绍了industryyor（**用于解决现实世界OR问题的工业基准**）  
[代码链接](https://github.com/Cardinal-Operations/ORLM)
## ***简介***
#### 现有的研究
主要集中在应用预训练模型来指定优化问题的数学模型，先通过识别语义实体，然后在此基础上指定数学模型，**在执行的过程中会累计误差，并且参数规模小，泛化能力有限**  

随着ChatGPT等大语言模型的出现，研究人员开始通过直接提示这些模型来生成数学模型。为了扩展到包含使用成熟求解器的程序的完整解决方案，提示工程方法采用基于专有大语言模型的多智能体协作。这些智能体使用复杂的推理链进行协作，以完善数学模型和程序。然而，这些方法**严重依赖于要求用户提交敏感数据的专有大语言模型**。这在工业应用中可能是令人望而却步的，因为**数据隐私**始终是人们最关心的问题
#### 大语言模型训练数据集的四个关键要求
1. 数据集必须涵盖各种场景、问题类型和难度，这对我们的模型的鲁棒性至关重要
2. 由于业务目标、市场条件或资源可用性的变化，目标和约束可能会经常发生变化。数据集应该反映这些变化，为模型做好适应这种适应性的准备
3. 不同的客户可能会使用不同的术语来描述相同的问题，数据集应该适应这种语言的多样性
4. 对于同一个问题，通常会有多种建模技术  
#### OR-INSTRUCT
![OR-INSTRUCT概述](https://pic.imgdb.cn/item/66975bc9d9c307b7e9cfa830.png)
1. 扩展  
   利用GPT-4生成的数据涵盖更广泛的场景和问题类型，扩展到数千个场景和数十个问题类型（*虽然这一战略部分解决了全面覆盖问题，但我们观察到它不能满足其他要求，因为这些要求超出了GPT-4的能力*）
2. 增强
   增强包括改变目标和约束，改变问题的措辞，以及结合多种建模技术（*从本质上讲，我们将剩余的需求分解为子任务，允许GPT-4在其限制范围内生成具有更高问题解决方案多样性的示例*）
## *背景和需求*
#### 优化建模的需求
   ![](https://pic.imgdb.cn/item/66975ee1d9c307b7e9d772c8.png)
#### 解决方案的多样性
   **增强环境适应性**  
   改变目标和约束:第一个扩展包括在问题中添加、删除或替换目标和约束，以及对数学模型和程序进行必要的调整。具体来说，我们首先向GPT-4提供原始示例，并要求它列出目标和约束的五个潜在变化。这些建议更改然后反馈给GPT-4，使用准备好的几次提示来修改问题、模型和相应的程序
   ![](https://pic.imgdb.cn/item/669761f4d9c307b7e9dee50d.png)
**改写问题**  
   第二次增强对问题的提法进行修改，模拟不同客户的表达习惯。这个过程包括指GPT-4重写OR问题，或简化或复杂，同时**确保核心逻辑与解决方案一致**，包括数学模型和程序
  ![](https://pic.imgdb.cn/item/66976329d9c307b7e9e174c0.png)
**结合多种建模技术**
  ![](https://pic.imgdb.cn/item/66976329d9c307b7e9e17526.png)
#### 后处理和过滤
运用启发式算法
1. 删除任何评估基准中问题相互重复或匹配问题的示例
2. 手动纠正程序中的小语法错误（有时是由GPT-4不熟悉COPT API引起的）
3. 放弃低质量数据
## *实验*
#### 数据生成
从686个行业案例作为种子数据开始，添加到训练数据池中，每次迭代对训练数据池应用20K次扩展，并对每个增强操作应用6K次，然后自动过滤掉明显低质量的条目。对这个过程进行2次迭代，最终得到30K个训练样例
![](https://pic.imgdb.cn/item/66976649d9c307b7e9e92ff1.png)
在这些数据中，57%是由扩展操作生成的，17.2%是由改变目标和约束的增强生成的，15.3%是由重新措辞问题的增强生成的，10.5%是由合并多种建模技术的增强生成的，扩展数据的正确性准确率为70%，增强数据的准确率为75%
#### 模型训练
使用了目前广泛应用的open-directive模型框架
#### 评估基准与指标
使用了**NL4OPT**和**MAMO**作为评估指标，**IndustryOR**作为评估基准
**NL4OPT**是运筹学中使用最广泛的基准，在其测试集中包括289个线性规划问题  
**MAMO**是一个并行项目，对数学建模进行评估大语言模型的能力。它包括652个简单和211个复杂的线性规划问题，每个问题都有相应的最优解，来自各种学术材料  
**IndustryOR**是第一个工业基准，由来自八个行业的100个现实世界的OR问题组成。它涵盖了5种类型的问题——线性规划、整数规划、混合整数规划、非线性规划等——跨越3个难度级别  
#### 实验结果
![](https://pic.imgdb.cn/item/66976929d9c307b7e9efb553.png)
在NL4OPT测试中，基于llm的方法明显优于基于plm的最佳方法，在LLaMA-3-8B上训练的表现最好的ORLM，在NL4OPT、MAMO(包括简单和复杂的线性规划)和工业基准上达到了**最先进的性能**，在微观平均上超过了基于GPT-4的标准提示42.2%，在宏观平均上超过了55.4%
## 分析与讨论
#### ORLM与GPT-4在工业领域的比较
![](https://pic.imgdb.cn/item/66976a5dd9c307b7e9f28a1c.png)
ORLM-LLaMA-3-8B在所有难度级别上都比Standard-GPT-4表现
优异，特别是在难度类别中。就不同的问题类型而言，ORLM-LLaMA-3-8B在线性规划、整数规划和混合整数规划方面都优于Standard-GPT-4。两种模型在非线性规划和其他罕见题型上的表现都不佳
#### OR指令增强器的消融研究
![](https://pic.imgdb.cn/item/66976a5dd9c307b7e9f28a5f.png)
研究了改变目标和约束、改变问题措辞和结合多种建模技术的效果，具有所有三种增强(表示为Full增强)的训练数据在微观平均上达到68.6%，在宏观平均上达到55.7%。从基础设置中删除三种增强中的任何一种都会导致所有基准的性能下降，无论是微观平均值还是宏观平均值。**改变问题的措辞似乎比其他两个更重要**。总体而言，结果表明，这三种增强方法都有助于提高总体表现，证明了它们在增强问题解决方案多样性方面的有效性。
## 结论
在本文中，提出训练开源大语言模型进行优化建模。确定了OR大语言模型训练数据集的四个关键要求，设计并实现了OR-INSTRUCT，这是一种半自动流程，用于创建适合特定要求的合成数据。还推出了IndustryOR，这是第一个工业基准。将OR-INSTRUCT数据应用于7b大小的开源大语言模型，从而显著提高了优化建模的能力。展望未来，我们预计将OR-INSTRUCT扩展到训练开源大语言模型将带来进一步的进步