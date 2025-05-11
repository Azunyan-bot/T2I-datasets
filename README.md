# 文生图（Text-to-Image）主流数据集汇总（近三年）

本项目收集了近三年主流文生图（Text-to-Image, T2I）模型所用的开源数据集，包含数据集简介、规模、下载链接、格式样例、构建过程与评价指标等信息，方便研究者查阅与对比。

---

## 数据集一览表

| 数据集名称         | 规模（图片数） | 输入/输出           | 公开性  | 下载链接 |
|------------------|---------------|---------------------|--------|----------|
| **COCO**         | ~33万         | 图片+英文描述        | 公开   | [链接](https://cocodataset.org/#download) |
| **CC3M**         | ~300万        | 图片+英文描述        | 公开   | [链接](https://github.com/google-research-datasets/conceptual-captions) |
| **CC12M**        | ~1200万       | 图片+英文描述        | 公开   | [链接](https://github.com/google-research-datasets/conceptual-12m) |
| **LAION-400M**   | ~4亿          | 图片+英文描述        | 公开   | [链接](https://laion.ai/blog/laion-400-open-dataset/) |
| **LAION-5B**     | ~50亿         | 图片+英文描述        | 公开   | [链接](https://laion.ai/blog/laion-5b/) |
| **SBU Captions** | ~100万        | 图片+英文描述        | 公开   | [链接](https://www.cs.virginia.edu/~vicente/sbucaptions/) |
| **DiffusionDB**  | ~1400万       | 用户prompt+生成图片  | 公开   | [链接](https://poloclub.github.io/diffusiondb/) |
| **PartiPrompts** | ~1.6万        | 精细文本             | 公开   | [链接](https://github.com/google-research/parti-prompts) |

---

## 数据集详细介绍

### 1. MS-COCO

- **简介**：MS-COCO（Microsoft Common Objects in Context）是一个大规模的图像识别、分割和描述数据集，收集了日常生活场景中的图片，每张图片配有5条人工英文描述。
- **输入/输出**：英文描述（caption）⇄ 图片
- **规模**：约33万张图片，每张图片5条描述
- **数据格式样例**：
    ```
    {
        "info": info,
        "images": [image],
        "annotations": [annotation],
        "licenses": [license],
    }

    info{
        "year": int,
        "version": str,
        "description": str,
        "contributor": str,
        "url": str,
        "date_created": datetime,
    }

    image{
        "id": int,
        "width": int,
        "height": int,
        "file_name": str,
        "license": int,
        "flickr_url": str,
        "coco_url": str,
        "date_captured": datetime,
    }

    license{
        "id": int,
        "name": str,
        "url": str,
    }
    annotation{
        "id": int,
        "image_id": int,
        "caption": str,
    }
    ```
- **下载链接**：[COCO Download](https://cocodataset.org/#download)
- **数据来源与构造过程**：
  1. 图片主要通过在 Flickr 等公开图片库中，搜索80类常见物体与多种场景类型的组合关键词获得，确保图片内容丰富且多样。
  2. 所有图片经过人工筛选，优先保留包含多个物体、复杂背景或非典型视角（non-iconic）的图片。
  3. 每张图片由5位不同的人工标注员（通过 Amazon Mechanical Turk 等众包平台）独立用英文撰写自然语言描述，要求内容准确、流畅，能体现图片中的主要内容、动作及场景。
  4. 标注任务设置多重审核机制，确保数据的准确性和一致性。
- **选取理由**：相比仅包含“典型”物体的图片（iconic images），COCO 更注重收集包含丰富上下文和非典型视角（non-iconic）的图片，有助于提升模型的泛化能力和对真实场景的理解。
- **常用模型**：AttnGAN、DF-GAN、DALL·E 2等
- **评价指标**：
    - 量化：FID、IS、CLIP Score
    - 质量评定：人工打分（如A/B测试）、Turing Test

---

### 2. Conceptual Captions 3M (CC3M)

- **简介**：大规模网络图片和自动提取的alt文本对。
- **输入/输出**：英文alt文本 ⇄ 图片
- **规模**：约300万对
- **数据格式样例**：
    ```json
    {
      "image_url": "http://...",
      "caption": "A cat sitting on a windowsill looking outside."
    }
    ```
- **下载链接**：[CC3M Download](https://github.com/google-research-datasets/conceptual-captions)
- **数据来源与选择理由**：从网络自动抓取，弱标注，低成本扩展，适合大规模预训练。
- **构造过程**：网页爬取 → 自动提取alt文本 → 自动/人工过滤
- **常用模型**：DALL·E、CogView等
- **评价指标**：同上

---

### 3. Conceptual Captions 12M (CC12M)

- **简介**：CC3M的扩展版，数据量更大。
- **输入/输出**：英文alt文本 ⇄ 图片
- **规模**：约1200万对
- **数据格式样例**：同CC3M
- **下载链接**：[CC12M Download](https://github.com/google-research-datasets/conceptual-12m)
- **数据来源与选择理由**：更大规模，覆盖更丰富的语义空间。
- **构造过程**：同CC3M
- **常用模型**：CogView、DALL·E等
- **评价指标**：同上

---

### 4. LAION-400M / LAION-5B

- **简介**：目前规模最大的公开英文图文对数据集，采用自动网络爬取与CLIP模型过滤。
- **输入/输出**：  
  - 文本 → 图像  
  - 图像 → 文本/相似图像
- **规模**：400M（LAION-400M）/ 5B（LAION-5B）对
- **数据格式样例**：
    ```
    FeaturesDict({
        "caption": Text(shape=(), dtype=string),
        "image": Image(shape=(None, None, 3), dtype=uint8, description=image),
        "license": Text(shape=(), dtype=string),
        "nsfw": ClassLabel(shape=(), dtype=int64, num_classes=4),
        "original_height": Scalar(shape=(), dtype=int32, description=original height of the image),
        "original_width": Scalar(shape=(), dtype=int32, description=original width of the image),
        "similarity": Scalar(shape=(), dtype=float64, description=cosine similarity score between the text and image embedding. Missing values default to -1.0),
        "url": Text(shape=(), dtype=string)
    })
    ```
- **下载链接**：  
  - [LAION-400M](https://laion.ai/blog/laion-400-open-dataset/)  
  - [LAION-5B](https://laion.ai/blog/laion-5b/)
- **数据来源与构造过程**：  
  1. 从Common Crawl数据集中解析HTML IMG标签及alt-text属性，初步提取图像与文本配对。  
  2. 剔除不合格样本（如alt-text长度少于5字符或图像小于5KB）。  
  3. 使用CLIP模型计算图像与文本的余弦相似度，低于0.3的样本被剔除。  
  4. 通过CLIP嵌入进一步过滤非法或不相关内容。
- **选取理由**：弥补多模态大模型训练中公开大规模数据集的不足，内容多样、覆盖面广，适合预训练。
- **常用模型**：Stable Diffusion、Imagen、GLIDE等
- **评价指标**：FID、IS、CLIP Score等量化指标，以及人工主观评价等（同上）。

---

### 5. SBU Captions

- **简介**：早期网络图片+alt文本对，人工过滤。
- **输入/输出**：alt文本 ⇄ 图片
- **规模**：约100万对
- **数据格式样例**：
    ```json
    {
      "image_url": "http://...",
      "caption": "A dog playing in the grass."
    }
    ```
- **下载链接**：[SBU Captions Download](https://www.cs.virginia.edu/~vicente/sbucaptions/)
- **数据来源与选择理由**：早期大规模弱标注数据集，便于模型预训练。
- **构造过程**：网络抓取 → 自动提取 → 人工审核
- **常用模型**：AttnGAN等
- **评价指标**：同上

---

### 6. DiffusionDB

- **简介**：收集用户实际prompt和由Stable Diffusion生成的图片对。
- **输入/输出**：用户prompt ⇄ 生成图片
- **规模**：约1400万对
- **数据格式样例**：
    ```json
    {
      "prompt": "A futuristic city skyline at sunset, digital art",
      "image_url": "https://..."
    }
    ```
- **下载链接**：[DiffusionDB Download](https://poloclub.github.io/diffusiondb/)
- **数据来源与选择理由**：真实用户生成记录，便于分析实际生成效果和prompt工程。
- **构造过程**：收集用户生成记录 → 去重 → 公开
- **常用模型**：Stable Diffusion系列
- **评价指标**：同上，另可分析prompt多样性

---

### 7. PartiPrompts

- **简介**：多样化有挑战性的prompts
- **输入/输出**：精细描述 → 图片
- **规模**：1600 diverse English prompts
- **数据格式样例**：
    ```
    Prompt	Category	Challenge	Note
    bond	Abstract	Basic	Biology-inspired concepts with multiple meanings
    element	Abstract	Basic	Biology-inspired concepts with multiple meanings
    ```
- **下载链接**：[PartiPrompts Download](https://github.com/google-research/parti/blob/main/PartiPrompts.tsv)
- **数据来源与构造过程**：部分 prompts 由研究团队自主创新设计，部分则通过人工筛选和采样近期相关论文中的典型 prompts.
- **选择理由**：
  1. 现有基准如 MS-COCO 和 Localized Narratives 虽然对评测文本到图像合成系统很有帮助，但其描述多局限于日常场景和自然图像中的物体，难以覆盖更广泛的开放域任务。
  2. PartiPrompts 提供了1600条多样化英文描述，更全面地测试和挖掘文本到图像模型的极限能力，适用于细粒度和高难度评测。
- **常用模型**：Parti、Imagen等评测
- **评价指标**：主要采用人工主观评价 (Human Evaluation)

---

### 8. Localized Narratives

- **简介**  
  Localized Narratives 是一种全新的多模态图像标注方式，旨在更紧密地连接视觉与语言
- **输入/输出** : 文本-图像
- **规模** ：848,749图像标注 (包含COCO(123k), ADE20K(20k), Flickr30k(32k), Open Image(671k))

- **数据格式样例**:
    ```
     {
         dataset_id: 'mscoco_val2017',
         image_id: '137576',
         annotator_id: 93,
         caption: 'In this image there are group of cows standing and eating th...',
         timed_caption: [{'utterance': 'In this', 'start_time': 0.0, 'end_time': 0.4}, ...],
         traces: [[{'x': 0.2086, 'y': -0.0533, 't': 0.022}, ...], ...],
         voice_recording: 'coco_val/coco_val_137576_93.ogg'
     }
    ```
- **下载链接**  
  [Localized Narratives Download](https://google.github.io/localized-narratives/)

- **数据来源与构造过程**  
  - 标注者一边用语音描述图片内容，一边用鼠标同步指向所述对象或关系。
  - 语音流经过自动语音识别（ASR）和人工转录，确保文本准确，并通过序列对齐算法实现语音-文本-鼠标轨迹的精确同步。

- **选择理由**
  - 传统方法对视觉-语言的连接还不够细致，很多重要的词（特别是关系、动作等）在图片中并没有被具体定位和标注。
  - LN实现每个词语与图像区域的高密度一一对应，包括名词、动词、关系词等。
  - 能显著提升模型在复杂场景理解、多目标定位和细粒度描述等任务的能力。

- **常用模型**  
  BLIP、OFA、Flamingo 等多模态大模型

- **评价指标**  
  - 量化指标：METEOR、CIDEr、BLEU 等  
  - 质量评定：人工主观评价

---

## 评价指标说明

### 量化指标

- **FID (Fréchet Inception Distance)**：衡量生成图片与真实图片分布的距离，越低越好。
- **IS (Inception Score)**：衡量生成图片的多样性和质量，越高越好。
- **CLIP Score**：用CLIP模型计算生成图片与输入文本的相似度，越高越好。
- **Precision/Recall**：评估生成图片的多样性与真实性。

### 质量评定（主观）

- **人类评价**：人工打分（如A/B测试、Likert量表），评价图片与文本的相关性、图片质量、创新性等。
- **Turing Test**：让人判断图片是AI生成还是人类拍摄。

### 其他参考指标

- **Prompt Sensitivity**：模型对文本微小变化的响应能力。
- **Bias/Fairness**：生成内容的公平性、是否有偏见。
- **Diversity**：同一prompt下生成图片的多样性。
- **Usability**：生成图片在实际应用中的可用性（如插画、设计等）。

---

## 参考与致谢

- [LAION 官方博客](https://laion.ai/blog/)
- [COCO Dataset](https://cocodataset.org/)
- [DiffusionDB 官网](https://poloclub.github.io/diffusiondb/)
- [PartiPrompts Paper](https://arxiv.org/abs/2206.10789)
- 以及各数据集原论文/主页

---

欢迎补充、纠错与 PR！
