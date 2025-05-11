# 文生图（Text-to-Image）主流数据集汇总（近三年）

本项目收集了近三年主流文生图（Text-to-Image, T2I）模型所用的开源数据集，包含数据集简介、规模、下载链接、格式样例、构建过程与评价指标等信息，方便研究者查阅与对比。

---

## 数据集一览表

| 数据集名称         | 规模（图片数） | 输入/输出           | 公开性  | 下载链接 |
|------------------|---------------|---------------------|--------|----------|
| **COCO**         | ~12.5万       | 图片+英文描述        | 公开   | [链接](https://cocodataset.org/#download) |
| **CC3M**         | ~300万        | 图片+英文描述        | 公开   | [链接](https://github.com/google-research-datasets/conceptual-captions) |
| **CC12M**        | ~1200万       | 图片+英文描述        | 公开   | [链接](https://github.com/google-research-datasets/conceptual-12m) |
| **LAION-400M**   | ~4亿          | 图片+英文描述        | 公开   | [链接](https://laion.ai/blog/laion-400-open-dataset/) |
| **LAION-5B**     | ~50亿         | 图片+英文描述        | 公开   | [链接](https://laion.ai/blog/laion-5b/) |
| **SBU Captions** | ~100万        | 图片+英文描述        | 公开   | [链接](https://www.cs.virginia.edu/~vicente/sbucaptions/) |
| **DiffusionDB**  | ~1400万       | 用户prompt+生成图片  | 公开   | [链接](https://poloclub.github.io/diffusiondb/) |
| **PartiPrompts** | ~1.6万        | 精细文本+图片        | 公开   | [链接](https://github.com/google-research/parti-prompts) |

---

## 数据集详细介绍

### 1. COCO

- **简介**：收集了日常生活场景图片，每张图片配有5条人工英文描述。
- **输入/输出**：英文描述（caption）⇄ 图片
- **规模**：约12.5万张图片，62.5万条描述
- **数据格式样例**：
    ```json
    {
      "image_id": "000000391895",
      "caption": "A man riding a wave on top of a surfboard."
    }
    ```
- **下载链接**：[COCO Download](https://cocodataset.org/#download)
- **数据来源与选择理由**：人工采集+众包标注，保证高质量和多样性，是视觉基准测试的黄金标准。
- **构造过程**：图片采集 → 众包写描述 → 多轮审核
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

- **简介**：目前规模最大的公开英文图文对数据集，自动抓取+CLIP过滤。
- **输入/输出**：文本-图像，图像-文本/相似图像
- **规模**：400M/5B对
- **数据格式样例**：
    ```json
    FeaturesDict({
        'caption': Text(shape=(), dtype=string),
        'image': Image(shape=(None, None, 3), dtype=uint8, description=image),
        'license': Text(shape=(), dtype=string),
        'nsfw': ClassLabel(shape=(), dtype=int64, num_classes=4),
        'original_height': Scalar(shape=(), dtype=int32, description=original height of the image),
        'original_width': Scalar(shape=(), dtype=int32, description=original width of the image),
        'similarity': Scalar(shape=(), dtype=float64, description=cosine similarity score between the text and image embedding. Missing values default to -1.0),
        'url': Text(shape=(), dtype=string),})
    ```
- **下载链接**：[LAION-400M](https://laion.ai/blog/laion-400-open-dataset/)、[LAION-5B](https://laion.ai/blog/laion-5b/)
- **数据来源与构造过程**：1. 从Common Crawl数据集中解析HTML IMG标签及alt-text属性，提取图像与文本配对。
2. 基于以下条件剔除不适合的样本：
Alt-text长度少于5字符或图像大小小于5KB。
使用CLIP计算图像与文本的余弦相似度，低于0.3的样本被剔除。
通过CLIP嵌入过滤非法内容。
- **选取理由**：弥补当前多模态模型训练中公开大规模数据集的不足
- **常用模型**：Stable Diffusion、Imagen、GLIDE等
- **评价指标**：同上

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

- **简介**：高质量、复杂描述配高质量图片，用于细粒度评测。
- **输入/输出**：精细描述 ⇄ 图片
- **规模**：约1.6万对
- **数据格式样例**：
    ```json
    {
      "prompt": "A detailed painting of a medieval city with a river running through it, bustling markets, and stone bridges.",
      "image_url": "https://..."
    }
    ```
- **下载链接**：[PartiPrompts Download](https://github.com/google-research/parti-prompts)
- **数据来源与选择理由**：人工精心设计，适合高质量评测。
- **构造过程**：专家设计prompt → 匹配高质量图片
- **常用模型**：Parti、Imagen等评测
- **评价指标**：主观质量评定为主

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
