# 研究内容
## 大模型网站选择

> 以下两个大模型网站是本次研究预模型的下载网站。其中Hugging Face为主要下载网站，其中Hugging Face网站中具有更多的开源预模型并且预模型中含有参数，便于微调使用。在Hugging Face网站中对预模型含有对应的初始使用流程教程，可以更快的测试使用


### Hugging Face

官方地址：[https://huggingface.co/](https://huggingface.co/)
Hugging face 起初是一家总部位于纽约的聊天机器人初创服务商，他们本来打算创业做聊天机器人，然后在github上开源了一个Transformers库，虽然聊天机器人业务没搞起来，但是他们的这个库在机器学习社区迅速大火起来。目前已经共享了超100,000个预训练模型，10,000个数据集，变成了机器学习界的github。

其之所以能够获得如此巨大的成功，一方面是让我们这些甲方企业的小白，尤其是入门者也能快速用得上科研大牛们训练出的超牛模型。另一方面是，这种特别开放的文化和态度，以及利他利己的精神特别吸引人。huggingface上面很多业界大牛也在使用和提交新模型，这样我们就是站在大牛们的肩膀上工作，而不是从头开始，当然我们也没有大牛那么多的计算资源和数据集。

在国内huggingface也是应用非常广泛，一些开源框架本质上就是调用transfomer上的模型进行微调（当然也有很多大牛在默默提供模型和数据集）。很多nlp工程师招聘的条目上也明摆着要求熟悉huggingface transformer库的使用。简单介绍了他们多么牛逼之后，我们看看huggingface怎么玩吧。因为他既提供了数据集，又提供了模型让你随便调用下载，因此入门非常简单。你甚至不需要知道什么是GPT，BERT就可以用他的模型了

### 百度智能云千帆大模型平台

官方地址：[https://cloud.baidu.com/product/wenxinworkshop](https://cloud.baidu.com/product/wenxinworkshop)
千帆大模型平台是面向企业开发者的一站式大模型开发及服务运行平台。提供基于文心一言底层模型（Ernie Bot）的数据管理、自动化模型定制微调以及预测服务云端部署一站式大模型定制服务，并提供可快速调用的文心一言企业级服务API，助力各行业的生成式AI应用需求落地。

## 从Hugging Face下载预模型

在Hugging Face中根据其提示来完成模型下载，共有使用git下载、手动下载和python依赖库下载这三种方法。

### 使用git下载大模型

![](https://img-blog.csdnimg.cn/direct/71333972484a45e3ad3a9eb9364c189e.png#id=EGs4b&originHeight=450&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 点击Clone repository，会弹出如下对话框


![](https://img-blog.csdnimg.cn/direct/e75c08e1062944c281182d7f2ad70663.png#id=G9LMr&originHeight=388&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 1、根据提示，使用git先安装lfs，先输入git lfs install 来安装插件，用于大型文件下载
>  
> 2、 然后复制下方的git指令来下载大模型


### 手动下载大模型

![](https://img-blog.csdnimg.cn/direct/cf342b573755485db2f59cae4b791c29.png#id=Qj734&originHeight=476&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 点击Files and versions标签，跳转至如下页面


![](https://img-blog.csdnimg.cn/direct/120483e99ce547308d3a3fad864b8ec5.png#id=snDnv&originHeight=498&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 依次点击红框所在列的所有文件。
>  
> 可使用git和手动下载组合下载。Git下载大文件速度较慢，但会较快的下载下其中小的文件，大的文件可以手动下载。


### 通过Python代码从ZhipuAI平台下载

使用python代码建议使用魔塔依赖库来下载，Hugging Face提供的依赖库在下载模型的时候会提示连接建立失败，大概率是因为Hugging Face社区被中国屏蔽，使用翻墙软件也不可使用依赖库来建立连接下载文件，具体原因不清楚。

![](https://img-blog.csdnimg.cn/direct/b878bf9d957549f39f27215aa6cbb30c.png#id=gEkkm&originHeight=95&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

使用如上代码可进行预模型下载并指定下载位置。需注意若不指定下载位置会自动下载到C盘中（大模型文件较大容易造成C盘爆炸）。

## 预模型调用测试

![](https://img-blog.csdnimg.cn/direct/d1fa121de5034bee8165ed038da3e0eb.png#id=kGHUC&originHeight=125&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 使用如上代码加载模型参数以及加载模型，并输入问题测试是否可运行。上述代码是使用显卡运行模型的代码。若要使用CPU运行模型，需要将第二行后面的half().cuda()改为.float()。


## 数据清洗

数据清洗，也称为数据预处理，是数据分析和机器学习中的一个关键步骤。它指的是对原始数据进行检查、转换和修复，以确保数据的质量、准确性和一致性。

数据清洗的主要目标是消除和校正数据中的错误、噪声、缺失值、重复值、不一致性和其他不完善之处，使数据适合进一步的分析、建模和挖掘。

其中最关键的部分，是与模型任务相关度高、具备多样性和高质量的数据。直接收集的海量数据并不能直接用于大模型，需要经过清洗、标注等工序后，才能生成可供大模型使用的数据集。

### 使用传统方式进行过滤

> 目的：筛选有用数据，清除"脏"数据


- 选择高质量数据源
- 规范化文本
- 去除HTML和标记
- 过滤停用词
- 过滤噪声词
- 消除敏感信息
- 检测和处理异常值
- 数据去重
- 评估数据质量
- 记录清洗过程

上述方式主要用于处理用爬虫爬取的网页数据。通过上述步骤可以有效提高模型的准确性、稳定性和安全性，确保生成的输出更加可靠和高质量。

目前主要研究的为NKCorpus方法来清洗数据。下方为研究借鉴的NKCorpus中对数据清洗的流程。
![](https://img-blog.csdnimg.cn/direct/68051d9f2d89408bb800260d240f6018.png#id=U5qCk&originHeight=903&originWidth=450&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 数据下载

构建大型数据集需要海量的原始文本数据资源,本项目中需要下载Common Crawl提供的近PB的数据,这对网络带宽要求极高,如果仅在单台机器上下载,需要的时间让人无法忍受。因此,NKCorpus选择支持多设备节点并行下载并对下载任务进行统一调度管理。

Common Crawl将每月整理的数据分为多个WET文件进行存储,每个WET文件的大小在100MB至200MB,这样的数据存储格式为多节点并行工作提供了便利。对于单个数据下载进程来说,进程首先向数据库请求任务,得到一个未下载的WET文件的下载路径并进行下载工作,下载完成后更新数据库状态。

#### 中文提取

下载得到的Common Crawl数据中包含多种语言数据,为了有效提取出中文数据,NKCorpus采取高效的语言检测算法并设计了以行为检测单位的方案,能够尽量去除非中文文本的同时减少有效数据的损失。

通过综合评估三种文本检测方法langid、langdetect8(8 https://github.com/shuyo/language-detection)和fastText,NKCorpus选用了运行效率较高、文本检测准确率也较高的fastText算法进行文本检测。Buck等人指出网页文本中通常会包含多种不同语言,为了尽可能多地保存中文数据并避免有效数据损失,NKCorpus以文本中的行作为语言判断的单位。对于每行,定义中文占比为单行文本中中文字符（包括简体中文、繁体中文和中文标点）数量与单行文本中除特殊字符（空格符、占位符、缩进等）外的所有字符数量的比值。当单行文本长度较长时,采取较低的阈值可以保留较多有意义的中文句子;单行文本长度较短时,采取较高的阈值可以保留含有中文的短文本（如短标题等）且不保留过多其他语言的文本。NKCorpus采用表4展示的阈值进行中文数据提取,当中文占比大于阈值时,保留该行文本,否则删除。
Table 4  The Chinese threshold for each length of text

| 文本长度 | 阈值 |
| --- | --- |
| 1至70 | 80% |
| 71至230 | 70% |
| 大于230 | 60% |

#### 文本过滤及清洗

通常中文提取后的数据中仍存在较多问题,不能直接用于构建训练数据集。NKCorpus通过一系列步骤对数据进行过滤清洗,可以将冗余文本、不良文本以及过短文本去除。

#### 完整内容提取

完整内容提取是将不成句或与正文内容无关的文本去除。主要去除以下几个部分的内容：

- 导航栏、短链接等不成句子的内容;
- 网页提示信息、声明信息等网页模板类内容;
- 时间、访问量等与正文内容无关的内容。

针对于上述需要去除的信息,NKCorpus设置了两个过滤器进行内容的过滤：（1）网页开始和结尾截取;（2）完整句子检测。网页开始和结尾通常为导航栏或者提示声明等无关内容,一般不含标点。通过遍历整体内容,找到第一个标点符号和最后一个标点符号,将不与第一个标点相关（中间存在空格、换行符等）的内容以及最后一个标点之后的内容进行删除。此外,在网页中间通常包含一些导航、标题、链接、日期等与正文内容无关的信息。可以通过对句子的完整性进行检测的方案提取出连续且有意义的内容。由于网页中不同部分的标点符号密度不同,NKCorpus针对文本内容进行逐行检测,根据行内是否存在标点判断其是否为完整语句。

#### 控制符检测

网页中一些具有特殊含义的控制符例如\x01、\x02、\x08、\u3000等在文本中不具有实际意义,会对不良文本检测造成干扰。因此,NKCorpus利用正则匹配的方法将控制符删除。

#### 不良文本去除

在去除不良文本时,如果以行为单位对数据进行是否为不良数据的判定（包含过高比例或者过多数量的不良词汇）,会造成许多的误判,并且可能造成数据内容的不连续导致文本质量降低。因此,NKCorpus选择以篇章作为不良文本的检测单位。NKCorpus检测每篇文章中包含的不良词汇表中的词汇,并计算不良词汇的出现次数以及占全文的比重,以此来判断整段文本是否为不良文本。不良文本去除的关键是不良词汇表的构建,我们首先基于[公开项目9](https://github.com/fighting41love/funNLP/tree/master/data)提供的词库对文本进行预检测,然后通过检查未能成功检测出的不良文本对不良词汇表进行补充。最终构建得到的不良词汇表中统计信息如表5所示。此外,NKCorpus支持对不同类别的不良词汇进行不同力度的检测,通过调整不同类别对应的阈值实现不同严格程度的检测。
**表5   各类不良词汇统计信息**

**Table 5  Statistics of censored words**

| 类别 | 数量 |
| --- | --- |
| 色情 | 1690 |
| 反动 | 792 |
| 暴恐 | 254 |
| 民生 | 734 |
| 贪腐 | 97 |
| 其他 | 2559 |

#### 文本长度检测

通常,文本质量与长度呈正相关,因此对篇章级别的内容进行长度限定是有意义的。进行长度筛选时,既需要筛去一定数量的低质量、不完整数据,也需要避免过多地将数据中篇幅较短但有实际意义的内容删除。NKCorpus最终确定文本长度的筛选阈值为20,可以有效达到以上目的。

#### 数据去重

Common Crawl中存在较多的重复文本,因此需要设计合理的去重流程,保证数据集的多样性。NKCorpus针对文本进行篇章级去重并采用先局部再整体的设计实现方案,能够在不牺牲效果的情况下拥有较高的处理效率。

传统基于MD5等哈希函数的算法仅能检测出完全相同的文本,会导致文本相似度非常高但不完全相同的文本被保留下来。Jaccard算法基于长文本的n-gram计算两条数据间的相似度,Jaccard相似度越高表示原始文本越相似。通过计算数据间的Jaccard相似度,便能够较为方便地去除高相似度文本。但Jaccard算法需要将所有数据两两配对,并沿原始文本进行滑动窗口计算,时间复杂度非常高,难以应用于大规模数据。

NKCorpus选用局部敏感哈希（Locally Sensitive Hashing,LSH)算法结合Jaccard算法来进行大规模的数据去重处理。LSH基于MinHash算法构建,能高效处理海量高维数据的相似性问题,LSH能够使两个相似度很高的数据以较高的概率映射成同一个哈希值,两个相似度很低的数据以极低的概率映射成同一个哈希值。NKCorpus采用相同的哈希函数处理所有数据获取哈希值,并通过比较哈希值判断两条数据是否可能相似。如果相似性分数高于阈值,再比较两条原始数据的Jaccard相似度。结合使用两种算法可以保证较高的效率和较好的去重效果。

#### 高质量数据筛选

上述处理步骤可以筛去文本中的正文无关内容和重复数据,但表达混乱的文本并不能被很好地识别。NKCorpus使用当前颇受认可的困惑度筛选方式,从已有数据中筛选出语句通顺的文本,以此提高数据整体质量。

NKCorpus首先使用公开的新闻、百科类等高质量数据训练了一个小型GPT-3模型（下文简称：GPT-3S）。将待检测数据处理后逐条输入模型,并利用模型输出结果计算文本困惑度,困惑度公式为：

困惑度=∏Ni=11p(wi|w1w2…wi−1)−−−−−−−−−−−−−−−√N困惑度=∏i=1N1pwiw1w2…wi-1N

其中w1,w2,…,wiw1,w2,…,wi表示文本第1,2,…i1,2,…i个字出现的概率,NN表示文本长度。困惑度越低表示文本质量越高,因此NKCorpus保留困惑度低于阈值的数据来保证保留数据的质量。

### 借助AI模型过滤有害数据和筛选高质量数据

目前，主流的数据清洗方法之一是借助AI模型进行清洗。这涉及使用机器学习技术来培训智能体，使其能够自动识别和清洗数据，以优化人工与机器在数据清洗中的工作分配。通过这种方式，一些人工分类、筛选和标注工作可以被机器执行，其准确率通常较高。

贝叶斯分类算法也被用于数据清洗，这是一种利用概率统计知识进行分类的算法，具有高准确率和快速执行的特点，适用于数据整理和统计分析。同时，借助贝叶斯相关算法和技术，可有效区分良性数据和不良数据，成为数据清洗的重要手段之一。
还有一些尝试使用文本识别算法和识别技术等AI能力进行数据清洗。例如，决策树和随机森林算法具备根据特征判断不良数据的能力。这些算法可增强特定领域的数据分析能力，实现更快的应用部署。

值得一提的是，经典的ChatGPT训练数据通过OpenAI专门训练了一个过滤模型，用于识别有害内容，以确保对模型的输入和输出数据进行有效管控，以遵守使用政策。

### 人工审查

上述步骤完成后，可以借助人工审核的方式对数据进一步清洗。

如果训练数据来源是互联网，存在可能包含暴力、性别歧视和种族主义言论等问题，不能通过上述两种方式完全筛选出去，还需要人工来进一步审查。如果数据集来源质量很高几乎不存在不良言论等问题，可以减少人工审查的时间。

深度学习是以数据为引擎的工程，可以说数据本身在一定程度上就代表了生产力。大模型对数据的需求不仅仅涉及数量，更涵盖了数据质量。高质量数据不仅是模型发挥作用的关键，也构成了企业团队AI技术发展的壁垒。以大量的清洗过的网络数据预训练大模型，之后在精标数据上微调，将大模型数据适配到各个垂域是未来大模型应用和发展的趋势。

## 微调数据筛选

微调数据是相对专业和精标的数据，不需要想上述数据清洗那么多的步骤。目前主要筛选如下步骤来完成微调数据集的处理。

- 规范化文本
- 检测和处理异常值
- 数据去重

通过规范化文本生成统一格式后，对数据进行异常值和去重操作。最后将处理好的数据整理成大模型需要的数据样式完成微调数据集的处理。

## 预模型微调

在使用处理好的微调用数据集后，对预模型进行微调。通过加载数据集和模型并进行训练。其中相关依赖在后续展示。使用windows下的环境对预模型进行微调训练时会出现其中有的依赖库加载失败的问题，如果条件允许最好使用linux相关系统进行模型微调训练。

# 使用的插件

- 使用显卡训练或运行模型需要使用CUDA和cuDNN
- 使用CPU训练或运行模型需要使用TDM-GCC和openmp
- 使用anaconda来构建python虚拟环境(保证训练环境的干净)
- 使用pycharm或vscode作为IDE编辑器
- Windows下可使用Ubuntu子系统作为linux相关系统来训练模型（需给wsl足够的内存空间，否则会出现killed进程的情况）

# 环境搭建

> 建议使用[Ubuntu子系统](#ubuntu%E5%AD%90%E7%B3%BB%E7%BB%9F%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA)来完成模型训练以及模型部署，使用Windows环境进行训练或部署时有可能出现其中的依赖包不可调用的情况，具体问题需要具体分析才能解决。


## Windows环境搭建

### Anaconda安装与使用

#### Anaconda简介

Anaconda包括Conda、Python以及一大堆安装好的工具包，比如：numpy、pandas等

因此安装Anaconda的好处主要为以下几点：

1. 包含conda：conda是一个环境管理器，其功能依靠conda包来实现，该环境管理器与pip类似。
2. 安装大量工具包：Anaconda会自动安装一个基本的python，该python的版本Anaconda的版本有关。该python下已经装好了一大堆工具包，这对于科学分析计算是一大便利
3. 可以创建使用和管理多个不同的Python版本：比如想要新建一个新框架或者使用不同于Anoconda装的基本Python版本，Anoconda就可以实现同时多个python版本的管理

#### Anaconda下载

下载3-5.2.0版本的[anaconda](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.2.0-Windows-x86_64.exe)。

#### Anaconda安装

> 下载好的anaconda3后直接双击安装包即可，有几个地方需要注意
![](https://img-blog.csdnimg.cn/direct/08e5273e11044892b6250a895bb07146.png#id=gHiko&originHeight=608&originWidth=786&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)


![](https://img-blog.csdnimg.cn/direct/25c5a2d0da4f4e709739971b93dada00.png#id=OH9cC&originHeight=608&originWidth=786&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
)
![](https://img-blog.csdnimg.cn/direct/26b4480322074c71b4edc8f92742a69f.png#id=rKTqu&originHeight=608&originWidth=786&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/4b1f2d61635b419b89bc1076ccf50d90.png#id=GYTb0&originHeight=608&originWidth=786&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/da0efc0ca9f9410cbafbf44e4f6f4e90.png#id=Alkx0&originHeight=608&originWidth=786&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> Finish后安装完毕


#### 测试安装

```
cmd输入：conda –version
```

> 若出现像这样的conda版本号即安装成功


![](https://img-blog.csdnimg.cn/direct/b1d5589e52a940e0ba901359389125af.png#id=ojnto&originHeight=217&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 更换源

不更换源的话，下载依赖会很慢。

> 进入cmd界面，输入下列指令


```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

#### 更新包

更新时间较长，建议找个空余时间更新,不更新也可以，但为避免后续安装其他东西出错最好更一下

```
# 先更新conda
conda update conda
# 再更新第三方所有包
conda upgrade –all
```

完成上述步骤后，anaconda基本安装完成

#### Anaconda虚拟环境创建

```shell
conda create --name yourEnvName python=3.10 # 建议安装3.10，后面有的依赖包需要3.9及以上版本才可安装
```

在cmd页面输入如上指令，即可创建anaconda虚拟环境，其中--name后的指令是要创建的虚拟环境的名称。建议虚拟环境尽量好记和好输。

#### Anaconda虚拟环境继续

在cmd命令提示符中输入如下指令

```
conda activate yourEnvName
```

![](https://img-blog.csdnimg.cn/direct/1ae985b4094948e4ae1df9a56a6175be.png#id=v9UY6&originHeight=66&originWidth=376&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

出现如上图所示的（--name）即为激活成功。可以在其中安装需要使用的依赖模版。

### Pycharm安装与使用

Pycharm 随便从网上找个教程跟着安装就行，没有任何难度

#### 使用Pycharm创建虚拟环境

在Pycharm的设置的Python解释器中可以直接创建虚拟环境。

具体创建步骤如下：

1.  先在设置中找到Python解释器并打开
![](https://img-blog.csdnimg.cn/direct/b91d3191d0464f80a85101c123737800.png#id=wYFyB&originHeight=629&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 
2.  在Python解释器的右侧有一个添加解释器，选择添加本地解释器
![](https://img-blog.csdnimg.cn/direct/98fb563fc7554879a612d8905daa8619.png#id=dBHF4&originHeight=548&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 
3.  选择Conda环境并创建新环境
![](https://img-blog.csdnimg.cn/direct/03fe6318e5184dd68d9f997e78f54cc4.png#id=Q9ke3&originHeight=606&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=) 

记得Python版本选择3.10，这是我一开始创建的3.8版本后面在使用chatglm3的一些相关功能时提示需要3.9及以上版本
4. 其中输入环境名称和Python版本即可创建虚拟环境。

#### 在Pycharm中激活虚拟环境

可以在如下界面中直接输入下载指令或是运行脚本指令，不需要在cmd页面激活虚拟环境再使用。
![](https://img-blog.csdnimg.cn/direct/fccadaa93cd341b3a466e7ce72d23b7b.png#id=hNT9O&originHeight=251&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

在Pycharm的右下角中有一个终端，会自动激活并进入你选择的Python虚拟环境中

### TDM-GCC和opennp安装

此为CPU训练模型或加载模型使用的插件，若不使用cpu进行加载或训练可不安装。

> 切记：cpu训练和显卡训练不可同时选择.若不需要cpu训练就不要安装了，工具等太多不仅占用存储空间且有时候会冲突需要调整


#### TDM-GCC下载

[TDM-GCC下载地址](https://jmeubank.github.io/tdm-gcc/articles/2021-05/10.3.0-release)

> 若进去后下载不下来，需要开启科学上网模式


#### TDM-GCC安装

**特别注意：** 安装的时候再选项gcc选项下方，勾选openmp，这个很重要，大坑，直接安装的话后续使用会报错。

> openmp在gcc下


![](https://img-blog.csdnimg.cn/direct/edfff4cbfb7e44b9ae04a006c3b75ee2.png#id=GWd7t&originHeight=675&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/7ad78f59e67345e7b55cf8d62c4ccd18.png#id=ig1aj&originHeight=681&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### TDM-GCC测试

在cmd输入下方命令：

```
gcc -v
```

![](https://img-blog.csdnimg.cn/direct/b98a1c84d86a4ea88476b29307bed2f8.png#id=uHV6K&originHeight=252&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

安装gcc的目的是为了编译C++文件，quantization_kernels.c和quantization_kernels_parallel.c文件

![](https://img-blog.csdnimg.cn/direct/d27cd92456fe45e0ad9ad84d6886a508.png#id=EwYHh&originHeight=259&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### CUDA 和cuDNN下载和安装

[可参考此份博客](https://blog.csdn.net/jhsignal/article/details/111401628)

#### 查看本机的CUDA驱动适配版本

桌面右键打开英伟达控制面板，点击帮助->系统信息->组件

![](https://img-blog.csdnimg.cn/direct/410f381183f648c3955200a930a4c3ce.png#id=p8Bh5&originHeight=552&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以看到本机支持的是CUDA12.2版本，表示的是不支持更高版本的。如果你升级了驱动，可能会支持更高版本，也可能不会提升。所以就直接安装CUDA12.2及以下的就行。

此处安装12.1，因为在后续的安装pytorch时，pytorch官网上支持的是12.1版本。后续测试发现12.1版本的Pytorch为支持CUDA12.1及以上CUDA版本，故可下载12.2版本的CUDA

#### 下载CUDA

[CUDA下载地址](https://developer.nvidia.com/cuda-toolkit-archive)

![](https://img-blog.csdnimg.cn/direct/d6f9ff24f2a64505a9b4bb39d49e3430.png#id=dQnKw&originHeight=482&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择合适的版本后进入下图界面，进行具体的系统选择然后下载。

![](https://img-blog.csdnimg.cn/direct/6e027b50940748e9ad0f9a7afb2c17ee.png#id=NSYmR&originHeight=495&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择好操作系统、版本、安装程序类型就可以直接点击下载了。这里以Windows10下载12.1为例。

#### 安装CUDA

下载完之后，找到下载好的CUDA，如下图，双击安装。
![](https://img-blog.csdnimg.cn/direct/2a280d327d8244a38a27d1627b1b7dcf.png#id=r8pir&originHeight=39&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

如果不修改默认地址的话，直接ok就行。需要修改地址的话，则现在要修改的地方创建文件夹再修改选择地址。

![](https://img-blog.csdnimg.cn/direct/4123ba6b6e8f428c9937cd7988d63fbd.png#id=FxvhF&originHeight=644&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择同意并继续

![](https://img-blog.csdnimg.cn/direct/df6cae5b7dad41f295cb6751b2feb06c.png#id=yoA9J&originHeight=642&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择自定义安装

![](https://img-blog.csdnimg.cn/direct/8773e4962630452a881f99e942ba0c8d.png#id=CVFHG&originHeight=648&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

记得修改上述安装地址

![](https://img-blog.csdnimg.cn/direct/c9fd20a0184e4015916dbac20b4a6a37.png#id=BVaM1&originHeight=654&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

直接勾选上就好，然后安装就ok了

#### 测试是否安装成功

在cmd中输入如下命令查看是否显示版本信息

```
nvcc --version
```

![](https://img-blog.csdnimg.cn/direct/13109ec0918b43139f758a378867bbcb.png#id=czMnI&originHeight=223&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 使用命令set cuda，可以查看cuda设置的环境变量。


#### 下载cuDNN

[下载地址](https://developer.nvidia.com/rdp/cudnn-download)

![](https://img-blog.csdnimg.cn/direct/71c0f5219a62466093dbfbe1c38d3527.png#id=YgA3t&originHeight=144&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

下载cuDNN是需要登录英伟达开发者账户的，如果没有的话就注册一下并填写个问卷就行

![](https://img-blog.csdnimg.cn/direct/a84b03a53e8b47c0aa3e9626dd2ae34b.png#id=QGkFw&originHeight=308&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/e391653cc885414d85e791cbcd43285e.png#id=UEp0g&originHeight=770&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择合适的版本下载就行，注意：一定要选择和安装的CUDA匹配的版本。

> 如果是如下界面，12.1或12.2对应的为cuDNN9直接下载即可


![](https://img-blog.csdnimg.cn/direct/33e191cd82f14e578a23f005361d5218.png#id=Y60dj&originHeight=847&originWidth=1491&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### cuDNN安装

下载好之后，直接解压cuDNN压缩包，可以看到bin、include、lib目录，如下图：

![](https://img-blog.csdnimg.cn/direct/c595d48f9fa24dac875f8f6f8e905c58.png#id=m9L64&originHeight=164&originWidth=360&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

打开CUDA的安装目录，若未修改应为C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA，找到bin、include、lib目录，如下图：

![](https://img-blog.csdnimg.cn/direct/3e712843e9e349a28a230d42d33f7338.png#id=CtbMS&originHeight=483&originWidth=422&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

接下来将cuDNN压缩包内对应的bin、include、lib目录下的文件对应的复制到bin、include、lib目录下。

- **注意：** 是复制文件夹下的文件到bin、include、lib目录，不是复制目录。

### 大模型依赖库安装

#### Pytorch安装

[官网地址](https://pytorch.org)

![](https://img-blog.csdnimg.cn/direct/7c174c645b7e4a1288c6295195db2241.png#id=Rvq70&originHeight=469&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择对应的参数后复制Run this Command:中的命令语句，到Python虚拟环境中安装Pytorch。

若使用Pycharm可以在Pycharm的终端中安装，如下图所示：

![](https://img-blog.csdnimg.cn/direct/84e0d4bba3ec438c86f1f65f4d68c06e.png#id=CmOH8&originHeight=326&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

其中会询问是否安装，输入y，然后等待安装完成即可
![](https://img-blog.csdnimg.cn/direct/f550685f3ba14b69931d5c354fe24775.png#id=p3kiE&originHeight=475&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 其余依赖模版安装

![](https://img-blog.csdnimg.cn/direct/afaa9e3245074992808ef8328dce5f29.png#id=BYdvS&originHeight=1089&originWidth=455&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

分别在如下两个文件夹中输入指令：pip install -r requirement.txt用于安装所需要的依赖。

### 环境测试

![](https://img-blog.csdnimg.cn/direct/a813d8222dc24771bf959d7833a163df.png#id=cJUQJ&originHeight=313&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

若上述命令已经执行完成后，并且已经下载了对应的模型后（若没下载就将下载模型那句指令取消注解）运行脚本测试是否成功。

在控制台中出现如下提示则代表运行成功

![](https://img-blog.csdnimg.cn/direct/997c158019714741877e0abec5cbb2d9.png#id=zIhQe&originHeight=236&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## Ubuntu子系统环境搭建

还是推荐使用Ubuntu子系统搞大模型这块，因为很多的依赖库本就是为了Linux提供的，在Windows上调用会出现各种各样的调用问题。而且Ubuntu中安装各种环境较为方便，直接复制粘贴命令行即可

### WSL环境检测

现在的Windows基本都可以使用WSL。Windows机器需要支持虚拟化并且需要在BIOS中开启虚拟化技术因为WSL2基于hyper-V

#### 查看是否开启虚拟化

按住Windows+R输入cmd打开命令行输入

```
systeminfo
```

可以看到如下字样代表电脑已经支持虚拟化可继续安装
![](https://img-blog.csdnimg.cn/direct/975af128ba2942dca6d1a85111801530.png#id=NNsDg&originHeight=523&originWidth=961&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

无论是Windows10还是Windows11所使用的Windows是最新版的如果不是最新版请在设置-Windows更新中将系统更新到最新版本。

#### 安装WSL2

1. 以管理员身份运行powershell
2. 启用Windows10子系统功能
![](https://img-blog.csdnimg.cn/direct/980b798d904346b3b0071d114570fe72.png#id=kKtRg&originHeight=605&originWidth=601&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/ce1e59fb28f34398960ff510b7a24d61.png#id=f8zWZ&originHeight=575&originWidth=633&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/0a544bcf67cb45719e1473a7fbf1a395.png#id=g1kic&originHeight=617&originWidth=550&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/46cd03da9b0b406c8f501e1d90250504.png#id=RmQ3n&originHeight=599&originWidth=543&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/0fbeb3e082864d64a767a7eab5f3d3dd.png#id=eDjAX&originHeight=636&originWidth=800&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/2feb681708534c4a895350266540fff0.png#id=helzX&originHeight=361&originWidth=559&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/6062640e85724058a61e35af1a8b96b5.png#id=emOgv&originHeight=422&originWidth=419&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3. 打开的powershell窗口中输入如下命令：

> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart


4. 启用虚拟机平台功能，再打开的powershell窗口中输入如下命令：

> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart


5. 重启电脑

### Ubuntu子系统下载与安装

建议安装在非C盘中，因为后需要下载的东西很多几十个起步，会导致C盘内存爆炸。

#### 下载Ubuntu20.04安装包

首先现在想要保存子系统的盘创建一个文件夹，例如：D:\wsl_Ubuntu，因为这样即便是重装系统也不用重新装软件了

然后进入这个文件夹下载Ubuntu20.04

```
Invoke-WebRequest -Uri https://wsldownload.azureedge.net/Ubuntu_2004.2020.424.0_x64.appx -OutFile Ubuntu20.04.appx -UseBasicParsing
```

等他下载完即可，**文件有4G多，耐心等待**
![](https://img-blog.csdnimg.cn/direct/02ad71e96fa24699956ab1b5588e0f5d.png#id=scjgn&originHeight=495&originWidth=977&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

然后执行下列命令，如下图：

```
Rename-Item .\Ubuntu20.04.appx Ubuntu.zip
Expand-Archive .\Ubuntu.zip -Verbose
cd .\Ubuntu\
.\ubuntu2004.exe
```

![](https://img-blog.csdnimg.cn/direct/b6953e0412814890b95b28b2aee3758e.png#id=vppIl&originHeight=516&originWidth=720&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

这个图我是直接进了wsl，因为我已经安装过了。你们第一次安装的话会弹出一个黑框框等几分钟这样，然后输你想要的入用户名和密码就行

最后可以在powershell里面 , 看看自己安装的版本

```
wsl -l -v
```

![](https://img-blog.csdnimg.cn/direct/dc14577be6384da6866dc48b6c46ab68.png#id=Dx4Lj&originHeight=176&originWidth=486&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 更换源

因为我们国内访问外网比较慢，所以我一般是会换成清华源，另外请注意，wsl的Ubuntu证书是过期的，如果你想手动还源的话请记得先更新证书

不过我已经写好脚本了

直接在Linux里执行,即可换成清华源

```
wget https://gitee.com/lin-xi-269/tools/raw/master/os/QHubuntu20.04 && bash QHubuntu20.04
```

### Ubuntu子系统安装Anaconda

1. 打开子系统，复制下边命令过去：

```
wget https://repo.anaconda.com/archive/Anaconda3-2021.05-Linux-x86_64.sh
```

2. 下载完成后，输入安装命令：

```
bash Anaconda3-2021.05-Linux-x86_64.sh
```

（注意，记得进入到该安装包目录下再输入安装命令，或者输入安装包的绝对路径也行）
3.	安装过程中，刚开始要按回车键很多次（大概一直按住5秒这样），之后两三个地方会让你输入yes/no，全输入yes。有提示输入回车的，就按回车，然后等待1分钟左右即可安装完毕。
4.	安装完成之后输入添加到路径的命令：

```
vim ~/.bashrc
```

5. 按“i”键进入插入模式，在打开的文件中最后一行加上：

> PATH=/home/user/anaconda3/bin:$PATH


6. 输入后按esc键回到正常模式并输入:wq，然后回车，即可保存。然后输入

```
source /etc/profile回车。
```

7. 升级conda

```
conda update -n base -c defaults conda
```

8. 更换pip源

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

9. 更换conda源

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
# 安装pytorch还需要再添加如下channels：
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/

# 设置搜索时显示通道地址
conda config --set show_channel_urls yes
```

10. 创建虚拟环境

```
conda create --name yourEnvName python=3.10
```

### Ubuntu子系统安装CUDA和cuDNN

#### 查看nvidia配置

在终端面板中输入：

```
nvidia-smi
```

查看支持版本
![](https://img-blog.csdnimg.cn/direct/db60582349d34d738aeb13d030e0d568.png#id=CyhdZ&originHeight=355&originWidth=765&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 下载与安装配置CUDA-Toolkit

[CUDA下载地址](https://developer.nvidia.com/cuda-toolkit-archive)

选择对应版本，这里以12.2为例

![](https://img-blog.csdnimg.cn/direct/a6cfc73df9044090b227e927aa09cd30.png#id=x4y90&originHeight=897&originWidth=1394&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

其中特别注意要选择WSL-Ubuntu

执行Base Installer中的指令

```
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.2.1/local_installers/cuda-repo-wsl-ubuntu-12-2-local_12.2.1-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-2-local_12.2.1-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-2-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
```

安装完成后，设置环境变量

```
sudo vim ~/.bashrc
```

文末追加

```
export PATH=/usr/local/cuda-12.2/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-12.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

**注意：** 记得修改自己的CUDA版本信息

配置生效

```
source ~/.bashrc
```

#### 测试CUDA是否安装成功

```
nvcc -V
```

![](https://img-blog.csdnimg.cn/direct/b7d594dacedf4826be63345c4d391944.png#id=XbtXX&originHeight=124&originWidth=459&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 安装cuDNN

[下载地址](https://developer.nvidia.com/rdp/cudnn-download)

#### 版本信息选择

![](https://img-blog.csdnimg.cn/direct/bf488c86da4844a88beeb71ef867b7af.png#id=jc2oy&originHeight=809&originWidth=1394&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 执行指令安装

```
wget https://developer.download.nvidia.com/compute/cudnn/9.0.0/local_installers/cudnn-local-repo-ubuntu2004-9.0.0_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2004-9.0.0_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2004-9.0.0/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cudnn
```

### VSCode下载与安装

[参考博客](https://developer.aliyun.com/article/1174015)

### Ubuntu子系统调用Windows的VSCode来运行

在Ubuntu子系统中输入

```
code .
```

即可调用windows中的VSCode来进行编程。注意code后的点号。
下方实例：
![](https://img-blog.csdnimg.cn/direct/39cf9dd50660436ab51727bbbc303bf0.png#id=l9l5o&originHeight=470&originWidth=865&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以直接在VSCode页面中的终端来控制使用Ubuntu子系统。

### VSCode运行环境选择Anaconda

先在扩展中下载Python相关插件
![](https://img-blog.csdnimg.cn/direct/866d0ca89e4941b0adc82fe218c01f89.png#id=Y0Gf4&originHeight=489&originWidth=676&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

快捷键：`ctrl + shift + p`选择解释器
![](https://img-blog.csdnimg.cn/direct/2325ef1ab9464b359536ef41b2953ce2.png#id=dsBfm&originHeight=329&originWidth=382&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择你创建的虚拟环境
![](https://img-blog.csdnimg.cn/direct/d962a1b4269043798cf487ec61586725.png#id=lrFBy&originHeight=307&originWidth=683&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

然后在右上角的终端中选择新建终端，你会发现你的终端前头带有你虚拟环境的名称
![](https://img-blog.csdnimg.cn/direct/d777c7919040414d87ccf87147ab14e3.png#id=tmmiL&originHeight=111&originWidth=1369&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

![](https://img-blog.csdnimg.cn/direct/cb53bb9c74d648ac95ce6b3024ffb69e.png#id=Vt9TS&originHeight=364&originWidth=752&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 下载大模型和大模型案例main代码

#### 下载大模型

进入/deep_learn/haily_product/try_chatglm.py文件，其中有一块注释掉的为下载模型的代码=
![](https://img-blog.csdnimg.cn/direct/70ee9e279380468bb7a658e6e07c67be.png#id=BTVaP&originHeight=752&originWidth=1605&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

执行下载模型代码，模型十几个G，下载时间较长，请耐心等待

#### 下载大模型案例main代码

在终端中进入你要保存代码的文件中，执行git指令

```
git clone https://github.com/THUDM/ChatGLM3.git
```

### 安装依赖库

在安装依赖库之前，请确保你运行的终端已经了你创建的conda虚拟环境中

#### 下载Pytorch

[官网地址](https://pytorch.org)

![](https://img-blog.csdnimg.cn/direct/a9ca25f0d2da402dbf400baa0f48fc71.png#id=zutJc&originHeight=891&originWidth=1719&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

选择对应的参数后复制Run this Command:中的命令语句，到VSCode终端中完成下载。因为已经完成下载没法展示。其中有个地方需要输入`y`来确定安装。

#### 其余依赖库安装

进入下载的大模型案例main代码包中
执行如下指令：

```
pip install -r requriements.txt
```

会自动安装需要的依赖库，其中依赖库很多，请耐心等待。

### 环境测试

#### 普通测试

在终端执行

```
python /home/haily/deep_learn/haily_product/try_chatglm.py
```

记得注释掉下载模型代码

![](https://img-blog.csdnimg.cn/direct/a13c1a105c4e4cadbd7adf878a6bea9e.png#id=tiqjt&originHeight=149&originWidth=1145&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 正常情况下方还有加载模型并模拟提问你好的结果，这里因为在运行着模型，不再重新加载防止电脑卡死


#### 使用大模型案例demo测试

在终端输入

```
streamlit run /home/haily/deep_learn/ChatGLM3/composite_demo/main.py
```

![](https://img-blog.csdnimg.cn/direct/2b5b1a1c5ec34131970a49df1581e464.png#id=fTJF4&originHeight=175&originWidth=822&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

> 如上图显示地址，点击地址进去并提问，不报错即为正常。

