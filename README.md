# 史料RAG

本项目展示如何使用[向量数据库](https://zilliz.com.cn/)基于[RAG(检索增强生成)](https://zhuanlan.zhihu.com/p/643953182)方式搭建一个中国历史问答应用。这个应用接受用户的询问，从历史语料库中检索相关的历史资料片段，利用大语言模型给出较为可靠的回答。相比于直接询问大模型，这种方式具有回答准确率高，不容易产生大模型的“幻觉”问题等优点。

本项目实现了两种使用方式，“Milvus方案“在本地启动一个Milvus向量数据库的Docker服务，使用LlamaIndex框架实现RAG的业务逻辑。“云服务方案”使用云上的知识库检索服务Zilliz Cloud Pipelines，该服务包括了RAG中文档切片、向量化、向量检索等功能。两种方案均采使用OpenAI的GPT大语言模型。
## 依赖:
    OpenAI token
    Milvus==2.3.3
    LlamaIndex==0.9.22
    Docker
    python3

## 指令展示

| Command                   | Flag(s)          | Description                                      |
|---------------------------|------------------|--------------------------------------------------|
| build                   |                 | 将`目录`或者`文件`进行索引构建，Milvus模式下为本地文件，Pipeline下为Url                    |
| build                  | -overwrite       | 同上，但是进行覆盖构建，已有索引将被清空       |
| ask                     |                 | 进入问答模式，输入`quit`退出该模式             |
| ask                     | -d               | 同上，但是开启Debug模式，将返回出来搜索出来的语料信息    |
| quit                    |                 | 退出当前状态                                     |




## Milvus方案
    
### 步骤1: 获取OpenAI Token

在开始之前，确保你有有效的OpenAI token。如果没有，请参考OpenAI的文档获取。并在环境变量中添加
```bash
export OPENAI_API_KEY=sk-xxxxxxxx你的tokenxxxxxxxxx
```

### 步骤2: 安装milvus
启动向量数据库Milvus, 默认端口为19530
```bash
cd db
sudo docker compose up -d
cd ..
```

### 步骤3: 安装依赖
```bash
pip install -r requirements.txt
```

### 步骤4: 构建历史知识库
利用文本语料库构建方便进行RAG的向量索引。执行交互程序cli.py,选择`milvus`模式，然后输入要构建的语料，`build ./history24/`会将该目录下所有文件进行索引构建，会消耗大量算力抽取向量，针对大规模语料库建议使用Pipeline方案。
```bash
python cli.py
milvus
build ./history24/baihuasanguozhi.txt
```

### 步骤5: 进行问题查询
输入`ask`进入提问模式。输入我们感兴趣的问题。
```bash
ask
'问题:关公刮骨疗毒是真的吗'
```

## Zilliz Pipeline方案

### 步骤1: 获取OpenAI Token

在开始之前，确保你有有效的OpenAI token。如果没有，请参考OpenAI的文档获取。并在环境变量中添加
```bash
export OPENAI_API_KEY=sk-xxxxxxxx你的tokenxxxxxxxxx
```

### 步骤2: 获取Pipeline的配置信息

注册Zilliz Cloud账号，获取相应的配置，这个方案可以利用云端的算力进行大量文档的处理。更加详细的[用例](https://github.com/milvus-io/bootcamp/blob/master/bootcamp/RAG/zilliz_pipeline_rag.ipynb)在这里。
![Pipeline中所需要的两个配置信息](https://raw.githubusercontent.com/milvus-io/bootcamp/6706a04e45018312905ccb7ad34def031d6937f7/images/zilliz_api_key_cluster_id.jpeg)
同样在环境变量中添加
```bash
export ZILLIZ_TOKEN=左边红框的信息 
export ZILLIZ_CLUSTER_ID=右边红框的信息
```

### 步骤3: 安装依赖
```bash
pip install -r requirements.txt
```

### 步骤4: 构建历史知识库
利用文本语料库构建方便进行RAG的向量索引。执行交互程序cli.py,选择`pipeline`模式，然后输入要构建的语料，`build https://github.com/wxywb/history_rag/tree/master/data/history_24/`会将该目录下所有文件进行索引构建, Pipeline方案目前仅支持Url，不支持本地文件。
```bash
python cli.py
pipeline
build https://github.com/wxywb/history_rag/tree/master/data/history_24/baihuasanguozhi.txt 
```

### 步骤5: 进行问题查询
输入`ask`进入提问模式。输入我们感兴趣的问题。
```bash
ask
'问题:关公刮骨疗毒是真的吗'
```




