# Patent-LLaMA（中文专利大模型）
## 项目简介：
<span style="font-size:16px;"> ChatGPT等大模型的出现彻底改变了人工智能的格局，为各种应用领域带来了新的机会和挑战，医疗、金融、法律等垂直领域大模型得到快速的发展，然而专利领域仍缺乏相关研究。
    为了推动大模型在专利领域的开放研究，本项目开源了中文专利领域基础大模型Patent-LLaMA-base和中文专利领域对话模型Patent-LLaMA-chat。
    专利大模型以目前开源的linly-7B为模型底座，在大量专利文本上进行二次预训练得到中文专利领域基础大模型Patent-LLaMA-base。在专利基础大模型Patent-LLaMA-base的基础上综合使用通用领域指令微调数据和专利特定任务指令数据微调模型，得到专利问答模型Patent-LLaMA-chat。</span>
## 训练数据：
<span style="font-size:16px;"> 专利大模型的训练包括二次预训练和指令微调两个阶段。预训练阶段使用专利文本对linly模型进行二次预训练，提升模型的专利领域化能力。指令微调阶段分为两个阶段，首先利用多样化的通用指令数据使模型提升指令遵循和自然语言理解能力，然后利用专利指令数据提升模型在专利领域特定任务上的能力。<br></span>
- 预训练数据：我们从网上收集整理的二十多万篇已授权专利和六十多万篇未授权专利进行筛选，去重和数据清洗后得到，共19G（token数：9.96*10^9）。<br>
- 指令微调数据：由于专利领域缺乏多样化的指令微调数据，Patent-LLaMA的指令微调数据综合使用通用领域指令微调数据和针对特定任务的专利领域指令微调数据。通过引入通用领域指令数据，模型可以更好地提升指令遵循能力，并提高对各种任务的处理能力。<br>
### 通用指令数据
本项目收集了多样化的中文通用指令微调数据集。<br></span>
       
| 数据集 | 描述 | 数量 |
| --- | --- | --- |
| pCLUE | 包含多种自然语言处理任务的指令微调数据集 | 1.2M |
| Firefly | 收集了23个常见的中文数据集，对于每个任务，由人工书写若干种指令模板 | 1.1M |
| belle | 通过self-instruct方法构造多种类型的中文指令数据 | 4.3M |
| Safety-Prompts | 中文安全prompts，提升大模型的安全性，将模型的输出与人类的价值观对齐 | 100k |



### 专利指令数据
<span style="font-size:16px;"> 
本项目构建了专利关键信息抽取任务，专利标题生成任务，专利翻译任务的指令微调数据。<br>

| 任务类型 | 描述 | 数量 |
| --- | --- | --- |
| 专利关键信息抽取 | 包含解决问题，技术手段，实施效果三类短语 | 2k |
| 专利标题生成 | 通过专利说明书摘要生成标题 | 2k |
| 专利翻译 | 将专利权力要求书从一种语言到另一种语言（包含中->英，英->中两种类型） | 各5k |


- 专利关键信息抽取任务：该任务旨在从专利文本中抽取该专利所解决的技术问题、使用的核心技术以及实施效果三类关键实体或短语。训练数据格式如下。</span><br>
        
        {
            "input": "本发明公开了一种酸奶冰淇淋的制作工艺，首创性地对酸奶冰淇淋的全部原料进行混料，对全部原料进行美拉德反应并且进行发酵。",
            "instruction": "请抽取出输入句子的解决问题，技术手段和实施效果实体",
            "output": "解决问题:酸奶冰淇淋;技术手段:美拉德反应,发酵;实施效果:;"
        }
        

- 专利标题生成任务：该任务旨在通过专利说明书摘要来生成专利的题目。

        {
            "instruction": "请生成下面摘要文本的标题",
            "input": "一种仿造品检测系统可以包括存储包括计算机可读指令的模块的存储器，该计算机可读指令用于确定与产品相关联的X‑标识(XID)。XID可以包括与产品相关联的参数相关联的非加密组成部分和加密组成部分。机器可读指令还可以包括从多个验证规则中选择一个或多个验证规则以从多个用来确定产品的真实性的验证技术中选择一个或多个验证技术。机器可读指令还可以包括使用选择的一个或多个验证技术来基于与产品相关联的XID确定产品的真实性。仿造品检测系统可以包括用于实现该模块的处理器。",
            "answer": "基于规则选择仿造品检测技术"
        }


- 专利翻译任务：该任务旨在将专利权力要求书由英文翻译成中文（或中文翻译成英文）。</span><br>
        
         {
            "instruction": "请将以下句子翻译成英文",
            "input": "如权利要求1所述的数据处理系统，其特征在于，所述批量日期比所述联机日期少一天",
            "output": "A data processing system according to claim 1, characterized in, on-line date than the batch date at least one day"
        }
        {
            "instruction": "请将以下英文句子翻译成中文",
            "input": "An ink-jet printing control apparatus according to claim 6, wherein, in the perpendicular direction of the 1st direction and said 2nd",
            "output": "如权利要求6所述的喷墨打印控制装置，其中，该第一方向与该第二方向互相垂直"
        }
## 模型下载
### Patent-LLaMA-base
模型特点：在经过中文预训练的linly模型上进一步使用专利数据预训练，模型仍在迭代中，将定期更新模型权重。
| 模型下载 | 分类 | 训练数据 | 更新时间 |
| --- | --- | --- | --- |
| Patent-LLaMA-base-7B | 基础模型 | 19G专利数据 | 2023.11.13 |
### Patent-LLaMA-chat
| 模型下载 | 分类 | 训练数据 | 更新时间 |
| --- | --- | --- | --- |
| Patent-LLaMA-chat-7B | 对话模型 | 通用指令数据，专利任务数据 | 2023.11.13 |

## 评估：


### 专利特定任务：
针对专利特定任务，我们开源了专利关键信息抽取任务，专利标题生成任务，专利翻译任务的相关评测集，用于衡量模型在专利相关任务的性能。
| 任务类型 | 描述 | 数量 |
| --- | --- | --- |
| 专利关键信息抽取 | 包含解决问题，技术手段，实施效果三类短语 | 0.2k |
| 专利标题生成 | 通过专利说明书摘要生成标题 | 1k |
| 专利翻译 | 将专利权力要求书从一种语言到另一种语言（包含中->英，英->中两种类型） | 各2k |

专利翻译：
| 模型 | translation | bleu | translation | bleu |
| --- | --- | --- | --- | --- |
| Patent-LLaMA-chat | en-zh | 0.5424329937228364 | zh-en | 0.4004108199901941 |

专利标题生成：
| 模型 | Bleu | Rouge-L-Recall | Rouge-L-Precision | Rouge-L-F1 |
| --- | --- | --- | --- | --- |
| Patent-LLaMA-chat | 0.7150994772810256 | 0.562 | 0.562 | 0.561999997 |

专利关键信息抽取：
| 模型 | recall (解决问题) | precision (解决问题) | F1 (解决问题) | recall (技术手段) | precision (技术手段) | F1 (技术手段) | recall (实施效果) | precision (实施效果) | F1 (实施效果) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Patent-LLaMA-chat | 0.628 | 0.627 | 0.628 | 0.455 | 0.463 | 0.459 | 0.412 | 0.444 | 0.428 |

### 公开benmark：
C-Eval 数据集: 是一个全面的中文基础模型评测数据集，涵盖了 52 个学科和四个难度的级别。我们将模型在C-eval上进行测试。
| 模型  | STEM  | Social Sciences | Humanities  | Others | Average  | Average（Hard） |
| --- | --- | --- | --- | --- | --- | --- |
| Patent-LLaMA-chat | 30.4 | 35.4 | 31.2 | 31.6 | 31.8 | 26.5 |
## 使用说明
由于LLaMA权重的许可限制，该模型不能用于商业用途，考虑到LLaMA权重的许可证限制，我们无法直接发布完整的模型权重。<br>
步骤1：获取LLaMA原始权重并转成Hugging Face Transformers模型格式（简称为hf格式），可参考转换脚本[convert_llama_weights_to_hf.py](https://github.com/wangjiabin928/Patent-LLaMA/blob/main/convert_llama_weights_to_hf.py)，将该代码复制粘贴保存到本地。<br>
进入到[convert_llama_weights_to_hf.py](https://github.com/wangjiabin928/Patent-LLaMA/blob/main/convert_llama_weights_to_hf.py)的同级目录，打开命令行执行：<br>
```
python convert_llama_weights_to_hf.py --input_dir {原始 llama-7b 权重路径} --model_size 7B --output_dir 保存路径
```
例：<br>
```
python convert_llama_weights_to_hf.py --input_dir ./llama-7b --model_size 7B --output_dir ./llama-7b-hf
```
步骤2：下载Patent-LLaMA的 delta 权重，使用如下脚本合并Patent-LLaMA的 delta 权重与 hf格式的LLaMA权重，得到完整版的Patent-LLaMA-7B模型权重。<br>
同理，将[apply_delta.py](https://github.com/wangjiabin928/Patent-LLaMA/blob/main/apply_delta.py)中的代码复制粘贴到本地
<br>
进入到[apply_delta.py](https://github.com/wangjiabin928/Patent-LLaMA/blob/main/apply_delta.py)的同级目录，打开命令行执行：<br>
``` 
python apply_delta.py --base {hf格式的LLaMA权重路径} --target {保存路径} --delta {Patent-LLaMA-7B 的 delta 权重路径}
```
例：<br>
```
python apply_delta.py --base ./llama-7b-hf --target ./Patent-LLaMA-chat-7B --delta ./Patent-LLaMA-chat-7B-delta
```
## 项目研发：
哈尔滨工业大学计算机科学与技术学院机器智能与翻译研究室团队王家彬，卢昊，刘欣鹏，张烜合作研发，实验室教师团队指导。哈尔滨市阳光惠远知识产权代理有限公司、佛科院-哈工大人工智能基础模型及应用联合实验室提供支持。
## 局限性、使用限制与免责声明：
本模型可能存在以下问题：
1. 对于有危害性的指令不具备充足的辨别能力，可能产生有害的言论。
2. 采用了参数量较小的模型，在任务的表现上存在不足。
3. 在专利领域的微调较为局限，在种类较少的任务上进行了微调以及测试，在其他任务上的能力没有充分探究。
4. 该模型并非真正的专利专家，仅仅能作为工具在一些任务上提供参考。<br>

考虑到其能力的局限，用户应当仅将其作为参考工具，在实际使用过程中需要谨慎。对于开发者，本项目应当仅用于后续研究，不得商用，以免对社会造成危害。<br>
本项目相关资源仅供学术研究之用，严禁用于商业用途，项目开发者不承担任何因为使用本项目相关内容（包括代码、模型、数据等）导致的损失与危害。<br>
## 未来工作：
1. 构造更加多样化的专利领域指令数据：由于专利领域相关研究相对较少，我们意识到数据集的多样性对于提高模型的性能至关重要。因此我们接下来尝试构建更多样化的专利领域指令微调数据集以进一步优化模型的性能。
2. 增加训练数据量和模型规模：在专利大模型开发过程中，我们发现7B的模型仍然难以满足复杂的专利领域文本。因此我们计划增加专利领域训练数据的规模，以提高模型的泛化能力和准确性。探索更大规模的模型，以应对复杂的专利领域文本的需求。<br>

本项目只是开始，我们将持续更新专利大模型的研究进展。

## 引用：
@misc{Patent-llama,<br>
  author={Jiabin Wang, Hao Lu, Xinpeng Liu, Xuan Zhang},<br>
  title = {Patent-LLaMA},<br>
  year = {2023},<br>
  publisher = {GitHub},<br>
  journal = {GitHub repository},<br>
  howpublished = {\url{https://github.com/wangjiabin928/Patent-LLaMA}},<br>
}
