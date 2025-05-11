# 文生图（Text-to-Image）主流数据集汇总（近三年）

本项目收集了近三年主流文生图（Text-to-Image, T2I）模型所用的数据集，包含数据集简介、规模、下载链接、格式样例、构建过程与评价指标等信息，方便研究者查阅与对比。

---

## 数据集一览表

| 数据集名称         | 规模（图片数） | 输入/输出           | 公开性  | 下载链接 |
|------------------|---------------|---------------------|--------|----------|
| **MS-COCO**      | ~33万         | 图片+英文描述        | 公开   | [链接](https://cocodataset.org/#download) |
| **CC3M**         | ~300万        | 图片+英文描述        | 公开   | [链接](https://github.com/google-research-datasets/conceptual-captions) |
| **CC12M**        | ~1200万       | 图片+英文描述        | 公开   | [链接](https://github.com/google-research-datasets/conceptual-12m) |
| **LAION-400M**   | ~4亿          | 图片+英文描述        | 公开   | [链接](https://laion.ai/blog/laion-400-open-dataset/) |
| **LAION-5B**     | ~50亿         | 图片+英文描述        | 公开   | [链接](https://laion.ai/blog/laion-5b/) |
| **DiffusionDB**  | ~1400万       | 图片+描述  | 公开   | [链接](https://github.com/poloclub/diffusiondb) |
| **PartiPrompts** | ~1.6万        | 精细文本             | 公开   | [链接](https://github.com/google-research/parti-prompts) |
| **Localized Narratives** | ~85万 | 图片+英文描述        | 公开   | [链接](https://google.github.io/localized-narratives/) |
| **WebLI** | ~100亿 | 图片+109种语言       | 未公开   | [链接](https://github.com/kyegomez/PALI) |
| **COYO-700M** | ~7.4亿 | 图片+英文描述       | 公开   | [链接](https://github.com/kakaobrain/coyo-dataset) |
| **Pick-a-Pic** | ~100万 | 图片+英文描述+偏好       | 公开   | [链接](https://github.com/yuvalkirstain/PickScore) |

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

---

### 2. Conceptual Captions 3M/12M (CC3M/CC12M)

- **简介**：由Google提出的大规模自动化图像-文本配对数据集
- **输入/输出**：文本-图片
- **规模**：约331.8万对/约1242万对
- **数据格式样例**：
    The Conceptual Captions training and validation sets are provided as TSV (tab-separated values) text files with the following columns:

<p align='center'>Table 2: Columns in Train/Validation TSV files.</p>

| Column   | Description                                                                      |
| -------- | -------------------------------------------------------------------------------- |
| 1        | Caption. The text has been tokenized and lowercased.                             |
| 2        | Image URL                                                                        |
- **下载链接**：[CC3M Download](https://github.com/google-research-datasets/conceptual-captions) [CC12M Download](https://github.com/google-research-datasets/conceptual-12m)
- **数据来源与构造过程**：
    - 自动抓取网页图片及其Alt-text描述；
    - 图片过滤（尺寸、比例、内容安全）；
    - 文本过滤（词性、情感、低频词、格式、与图片内容的相关性等）；
    - 语义抽象化（将专有名词替换为上位词，去除地点、时间等难泛化信息）；
    - 多轮过滤后，最终保留高质量、泛化性强的描述对。
- **选取理由**：
    - 数据规模大，覆盖类型丰富，有助于提升模型的泛化能力，特别适合训练能理解复杂场景和多样图片的自动图像描述模型；
    - 自动化构建流程，易于扩展和更新

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

---

### 6. DiffusionDB

整理后的 **DiffusionDB** 数据集简介如下：

---

### 13. DiffusionDB

- **简介**  
  DiffusionDB 收集了由 Stable Diffusion 用户社区真实生成的图片及其对应的prompt，反映了真实用户的多样化需求和生成习惯。

- **输入/输出** 文本-图片

- **规模**  
  - 第一版（v1）：约200万组 文本-图片对  
  - 最新版（v2）：超过1400万组 文本-图片对

- **数据格式样例**

| image_name                               | prompt                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |   part_id |       seed |   step |   cfg |   sampler |   width |   height | user_name                                                        | timestamp                 |   image_nsfw |   prompt_nsfw |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|-----------:|-------:|------:|----------:|--------:|---------:|:-----------------------------------------------------------------|:--------------------------|-------------:|--------------:|
| 0c46f719-1679-4c64-9ba9-f181e0eae811.png | a small liquid sculpture, corvette, viscous, reflective, digital art                                                                                                                                                                                                                                                                                                                                                                                                           |      1050 | 2026845913 |     50 |   7   |         8 |     512 |      512 | c2f288a2ba9df65c38386ffaaf7749106fed29311835b63d578405db9dbcafdb | 2022-08-11 09:05:00+00:00 |    0.0845108 |   0.00383462  |
| a00bdeaa-14eb-4f6c-a303-97732177eae9.png | human sculpture of lanky tall alien on a romantic date at italian restaurant with smiling woman, nice restaurant, photography, bokeh                                                                                                                                                                                                                                                                                                                                           |       905 | 1183522603 |     50 |  10   |         8 |     512 |      768 | df778e253e6d32168eb22279a9776b3cde107cc82da05517dd6d114724918651 | 2022-08-19 17:55:00+00:00 |    0.692934  |   0.109437    |
| 6e5024ce-65ed-47f3-b296-edb2813e3c5b.png | portrait of barbaric spanish conquistador, symmetrical, by yoichi hatakenaka, studio ghibli and dan mumford                                                                                                                                                                                                                                                                                                                                                                    |       286 | 1713292358 |     50 |   7   |         8 |     512 |      640 | 1c2e93cfb1430adbd956be9c690705fe295cbee7d9ac12de1953ce5e76d89906 | 2022-08-12 03:26:00+00:00 |    0.0773138 |   0.0249675   |


- **下载链接**  
  [DiffusionDB Download](https://huggingface.co/datasets/poloclub/diffusiondb)

- **数据来源与构造过程**  
  - 数据集通过抓取 Stable Diffusion 官方 Discord 服务器收集，包含 1400 万张图片、180 万条独特提示词及详细的生成超参数（如种子、步数、CFG scale、采样器、图片尺寸等）。
  - NSFW 检测：采用先进的文本与图片 NSFW 检测模型，为每对图片与提示词打分，便于后续研究过滤不安全内容。
  - 文件结构与分发：数据以灵活的文件夹结构组织，包含详细的元数据（如 Parquet 格式表），并以开源方式（CC0 1.0 协议）公开发布，用户可在线下载和筛选。

- **选择理由**  
  - 真实反映用户在实际生成任务中的文本描述和参数设置，覆盖风格、主题极为丰富，适合用于训练、微调和评测文本到图像生成模型，尤其适合人类行为建模和生成偏好分析。

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

---

### 11. COYO-700M

- **简介** : COYO-700M is a large-scale dataset that contains 747M image-text pairs as well as many other meta-attributes to increase the usability to train various models.

- **输入/输出**: 文本-图像

- **规模**： 747M

- **数据格式样例**
## Dataset Preview
| id            | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | text                                                                                                                                                                               | width | height | image_phash      | text_length | word_count | num_tokens_bert | num_tokens_gpt | num_faces | clip_similarity_vitb32 | clip_similarity_vitl14 | nsfw_score_opennsfw2 | nsfw_score_gantman | watermark_score | aesthetic_score_laion_v2 |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|--------|------------------|-------------|------------|-----------------|----------------|-----------|------------------------|------------------------|----------------------|--------------------|-----------------|--------------------------|
| 4896263451343 | <img src="https://cdn.shopify.com/s/files/1/0190/8574/products/Art_Riley-Monterey_Fishing_Fleet_1_grande.jpg?v=1479962684" width="400" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Fishing Fleet (Monterey), California art by Art Riley. HD giclee art prints for sale at CaliforniaWatercolor.com - original California paintings, & premium giclee prints for sale | 600   | 447    | bac58374982e0fc7 | 178         | 25         | 39              | 40             | 0         | 0.319336               | 0.248169               | 2.54512e-05          | 0.0293861          | 0.0406009       | 7.04812                  |
| 1425929344479 | <img src="https://www.ephotozine.com/resize/2018/07/xlrg/121543_1518912380.jpg?RTUdGk5cXyJFBQgJVANtcQlnYF8JERFaGwJRNQh6SlYUAEw1cmsCdg1hAWoxXFNGLSI=" width="400" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | The Gate by Pete2453                                                                                                                                                               | 600   | 347    | 8374726575bc0f8a | 20          | 4          | 6               | 6              | 0         | 0.24939                | 0.203735               | 6.97374e-06          | 0.00823276         | 0.0721415       | 6.98521                  |                                                                                                                                   

- **下载链接**  
  [COYO-700M Download & Info](https://github.com/kakaobrain/coyo-dataset)

- **数据来源与构造过程**  
  - 从 Common Crawl 等公开网页数据中自动爬取图片及其相关文本（如alt文本、标题、上下文）。
  - 经过多轮自动与人工过滤，剔除低质量、重复、非英文以及不适合公开和商用的内容。
  - 所有图片均保留原始链接，确保可溯源和合规使用。

- **选择理由**  
  - 规模极大，覆盖内容极其丰富，适合训练大规模多模态模型，提升模型对开放域和长尾内容的理解能力。
  - 数据合规（可商用、可溯源），适合企业级和学术研究。

---

### 2. Pick-a-Pic

- **简介**：Pick-a-Pic 是一个开放的文本到图像用户偏好数据集，基于真实用户在Web应用中对生成图像的选择与反馈，收集了大规模、真实、多样的文本提示与对应生成图片的偏好对比数据。
- **输入/输出**：文本⇄ 图片对及用户偏好标签（选A/B或平局）。
- **规模**：超100万条
- **数据格式样例**：
    暂无

- **下载链接**：[Pick-a-Pic v1](https://huggingface.co/datasets/yuvalkirstain/pickapic_v1) / [Pick-a-Pic v2](https://huggingface.co/datasets/yuvalkirstain/pickapic_v2) [目前不可用](https://github.com/yuvalkirstain/PickScore)
- **数据来源与构造过程**：选取多样化的英文 prompt，使用多个主流文本生成图像模型（如 Stable Diffusion、DALL-E 2、Imagen 等）生成对应图片。
通过众包平台招募标注员，对每组图片进行主观偏好选择，收集大规模人工对比标注数据。
- **选取理由**：相比于MS-COCO等数据集，Pick-a-Pic的prompt来源于真实用户需求，内容更具创造性和多样性，更能代表实际文本到图像生成的应用场景。偏好数据反映了真实用户的审美和意图，有助于模型更好地对齐人类偏好。
- **评价指标**：
    - 量化：PickScore（基于CLIP的偏好预测得分）、CLIP-H、Aesthetics Score、FID等
    - 质量评定：真实用户偏好（作为ground truth）、专家人工打分、Elo排序
- **衍生工具**：PickScore（基于Pick-a-Pic训练的评分函数，公开可用，能自动预测用户偏好并用于模型评测与生成优化）

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
