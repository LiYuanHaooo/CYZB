# 创源智本

## 智慧之本、创新之源——一个知心、知技、知行的知识产权信息助手

## 💟 模型链接
感谢书生·浦原大模型比赛，模型文件请 [点击此处](https://openxlab.org.cn/models/detail/ShangZhuanShuZhi/CYZB)！


## 📕 项目背景

<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;知识产权行业作为近年来愈发火爆的产业，已经受到越来越多的高等院校、科研机构等科创主体的重视；
知识产权工作往往面临着庞大的数据体量的查询与分析，使得从业者饱受困扰，如何进一步加强知识产权的源头保护，
在知识产权工作中有效的简化流程、提高效率，也变得越来越重要了。</p>
<p>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;创源智本是一款应用于知识产权行业的大模型，通过对知识产权信息进行聚合分析，从而对知识产权运作中各个角色的工作进行一定的辅助。
</p>

## 💉 实现目标

<procedure title="实现目标" id="ambition">
    <step>
        <p>微调一个基于知识产权信息的人工智能模型。</p>
    </step>
    <step>
        <p>通过模型提供条件下的知识产权信息的收集以及获取。</p>
    </step>
    <step>
        <p>建立交互功能，进行知识产权专业的问答。</p>
    </step>
    <step>
        <p>为用户提供专业的知识产权分析，例如提取关键词、专利摘要速读等。</p>
    </step>
    <step>
        <p>在分析基础上提供专业的知识产权建议与评估，为科创主体提供科研创新战略指导。</p>
    </step>
</procedure>

## ✒️项目阶段
- 大模型微调，纯文字问答对话交互实现
- 文件上传提取信息并进行交互对话（敬请期待）
- 海量数据分析，提供专业工作辅助交互助手（敬请期待）
- AI专业建议，生成定制化评估建议报告（敬请期待）

## 📜微调步骤
### 数据收集

📇获取整理了近期公开的专利信息数据，整理了上万条数据集，数据来源于日常的业务数据。

### 部署XTunner进行微调
- XTuner是一个高效、灵活、全能的轻量化大模型微调工具库！支持QLoRA、LoRA、全量参数微调等多种微调算法！我们采用了XTuner来对我们的模型进行了微调

#### 安装XTuner
```text
conda create --name xtuner python=3.10 -y
conda activate xtuner
```

#### 预处理微调数据集

- 数据格式
```text
[
    {
        "conversation":
            [
                {
                    "output":"摘要：本实用新型涉及一种工厂生产用的坚果炒制加工装置，旨在解决现有技术中坚果加工装置生产效率低下和包装不便的问题。...",
                    "input":"说明书：本实用新型涉及坚果炒制技术领域，尤其涉及一种工厂生产用坚果炒制加工装置。坚果，是闭果的一个分类，果皮坚硬，内含1粒或者多粒种子。...",
                    "system":"根据专利说明书生成专利摘要。"
                }
            ]
    },
    {
        "conversation":
            [
                {
                    "output":"摘要：本实用新型涉及一种用于巴旦木加工的旋转炒锅支撑旋转座。该装置旨在解决现有技术中人工,...",
                    "input": "使得整个滚筒内壁都具有很高的温度，而坚果一直在滚筒内随滚筒转动，而滚筒的转速又要保持均匀平缓的转动...",
                    "system":"根据专利说明书生成专利摘要。"
                }
            ]
    }
]
```

#### 拷贝大模型文件
```text
xtuner list-cfg
xtuner copy-cfg internlm2_chat_7b_oasst1_e3 . 
```

- 拷贝后对配置文件进行路径调整

#### 开始微调
```text
xtuner train internlm2_chat_7b_oasst1_e3_copy.py
```
#### PTH模型转换
```text
mkdir /root/data/HF
export MKL_SERVICE_FORCE_INTEL=1 
export MKL_THREADING_LAYER=GNU
xtuner convert pth_to_hf internlm2_chat_7b_oasst1_e3_copy.py work_dirs/internlm2_chat_7b_oasst1_e3_copy/iter_1500.pth HF
```
#### 转换后的文件合并到大语言模型
```text
xtuner convert merge /root/model/internlm2-chat-7b /root/data/HF /root/model/merged --max-shard-size 2GB
```
#### 与合并后模型对话
```text
cd /root/model
xtuner chat ./merged --prompt-template internlm2_chat
xtuner chat ./merged --bits 4 --prompt-template internlm_chat
```
#### 💻WebDemo
##### 使用LMDeploy进行部署推理
```shell
pip install lmdeploy[serve]
lmdeploy serve gradio --model-name internlm2-chat-7b /train/merged
```
