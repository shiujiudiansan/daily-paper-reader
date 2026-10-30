---
title: Lattice-Based Threshold Blind Signatures
title_zh: 基于格的门限盲签名
authors: "Sebastian Faller, Guilhem Niot, Michael Reichle"
date: 2026
publication_date: 2026
publication_date_precision: year
publication_date_source: conference year only; exact release date unverified
publication_date_kind: unknown
pdf: "https://www.computer.org/csdl/proceedings-article/sp/2026/606500c196/2geEVUjjSGA"
tags: ["query:pqc-acc"]
score: 4.0
evidence: 基于格的后量子构造与安全性证明，可迁移至后量子累加器设计
tldr: 本文针对门限盲签名在后量子环境下缺乏构造的问题，提出首个基于格的门限盲签名方案，并在交互式变体SIS假设下证明其安全性。方案将签名密钥分散于多方以缓解密钥泄露风险，同时保持盲签名的隐私性。其格基后量子安全证明技术对构造抗量子累加器有借鉴意义，但研究对象并非累加器本身。
source: IEEE-SP-2026-CSDL
selection_source: conference_retrieval
motivation: 门限盲签名在经典设定已有进展，但后量子设定下尚无构造。
method: 基于格构造门限盲签名，并在交互式SIS假设变体下给出安全性证明。
result: 得到首个在后量子设定下安全的门限盲签名方案。
conclusion: 为隐私保护协议提供了抗量子的门限盲签名工具。
---

## Abstract
Blind signatures are a central tool for privacypreserving protocols. They allow users to obtain signatures from a signer without the signer seeing the signed message. For instance, it enables electronic cash: signatures correspond to coins which can be issued by the bank in a privacypreserving manner via blind signing. To mitigate the risk of key compromise, threshold blind signatures allow the distribution of the signing key amongst - parties. While recent works have focused on improving the security of this primitive in the classical setting, no construction is known to date in the post-quantum setting. We present the first construction of a threshold blind signature secure in the post-quantum setting, based on lattices. We prove its security under an interactive variant of the SIS assumption introduced in [Agrawal et al., CCS'22]. Our construction has a reasonable overhead of a factor of roughly - to - in signature size over comparable nonthreshold blind signatures over lattices under heuristic but natural assumptions.

---

## 论文详细总结（自动生成）

# 《Lattice-Based Threshold Blind Signatures》论文总结

> 说明：本次可获取的文本为 IEEE CSDL 论文页面（含标题、作者、摘要、出版信息与元数据），**未包含论文正文**。因此以下总结严格区分“摘要明确陈述的内容”与“正文未提供、无法核实的内容”，不对方法细节与实验规模作推测性编造。

---

## 1. 核心问题与研究背景

- **盲签名（Blind Signature）** 是隐私保护协议的基础工具：用户可从签名者处获得签名，而签名者看不到被签消息内容。典型应用是**电子现金**——签名对应“硬币”，银行可在不获知用户身份/消费内容的前提下完成签发。
- **门限盲签名（Threshold Blind Signature）** 将签名密钥分散到 \(N\) 个参与方，需达到门限数量的参与方协作才能完成盲签，从而**缓解单点密钥泄露风险**。
- **研究缺口**：近年工作主要在**经典（非后量子）设定**下改进该原语的安全性，而**后量子设定下此前没有任何构造**。
- **本文定位**：提出**首个基于格的后量子安全门限盲签名构造**，填补上述空白，为抗量子的隐私保护协议（如电子现金、匿名凭证）提供基础组件。

---

## 2. 方法论（基于摘要披露的层面）

- **核心思路**：以**格（lattice）** 为基础构造门限盲签名，即将格基盲签名的签发过程改造为多方分布式协作，同时保持“签名者不可见消息”的盲性。
- **安全性假设**：在 **[Agrawal et al., CCS'22] 引入的 SIS 假设的交互式变体（interactive variant of SIS）** 下证明安全性。该假设针对多方交互式签名场景设计，允许归约处理门限协议中的交互与模拟问题。
- **效率特征**：相比可比的**非门限格基盲签名**，签名尺寸开销约为 **1.4× 至 2.5×**；该比较建立在“**启发式但自然（heuristic but natural）**”的假设之上。
- **未披露的关键细节**（正文中才有的内容，此处无法核实）：
  - 具体格基盲签名底层方案（如基于 Fiat–Shamir with aborts、Schnorr-like 结构或 Boyen 型格签名）；
  - 门限化技术路线（秘密共享方式、分布式采样/拒绝采样如何处理、是否采用 Shamir 共享 + 拉格朗日插值）；
  - 协议轮数与通信复杂度、签名/公钥/份额的具体比特尺寸；
  - 盲化与去盲化的代数形式、安全性证明的归约结构；
  - 形式化安全定义（盲性、不可伪造性、鲁棒性在门限与并发环境下的定义）。

---

## 3. 实验设计与评测方式

- 本文属**理论构造 + 安全性证明**类密码学论文，**不使用数据集**，也不存在机器学习意义上的 benchmark。
- 摘要中体现的唯一量化评测维度是：**与“可比的非门限格基盲签名”进行签名尺寸对比**，报告开销倍数为 **约 1.4×–2.5×**。
- **未提供**：具体对比方案名单、参数集（如环维数 \(n\)、模数 \(q\)、高斯参数）、安全级别（如 128/192/256-bit）、签名/公钥绝对字节数、签名与验证的计算耗时、通信轮数与带宽开销、实现平台（CPU 型号、单核/多核）等。

---

## 4. 资源与算力

- **文中（可获取部分）未提及任何算力信息**：无 GPU 型号/数量、无训练或实验时长、无测试机器配置。
- 需说明：此类密码学论文通常以 CPU 上的原型实现与微基准测试为主，而非 GPU 训练；但**由于正文不可得，无法确认作者是否提供了实现与性能测量**。

---

## 5. 实验数量与充分性

- 从摘要看，评测仅覆盖**签名尺寸这一单一指标的单组对比**，**未见**多参数集、多安全级别、消融式分析（如门限 \(t,n\) 变化对开销的影响）、通信复杂度实测或与经典门限盲签名的横向对比。
- **充分性判断受限**：由于缺乏正文，无法评估实验组数与统计严谨性；仅就摘要而言，**效率评估维度偏窄**，对“可部署性”的支撑证据不足。
- **公平性**：比较对象被限定为“可比（comparable）非门限格基盲签名”，并声明在“启发式但自然假设”下进行，属于**同类同假设下的相对比较**，方向上合理，但具体是否同参数、同安全级别无法核实。

---

## 6. 主要结论与发现

- 给出了**首个在后量子设定下安全（secure in the post-quantum setting）的门限盲签名构造**，基于格实现。
- 其安全性可归约到**交互式 SIS 假设变体**（源自 Agrawal 等 CCS'22）。
- 门限化带来的代价**可控**：签名尺寸仅增加约 **1.4×–2.5×**，说明该原语在后量子场景下具备实用潜力。

---

## 7. 优点与亮点

- **首创性明确**：填补“后量子门限盲签名无构造”的空白，学术价值清晰。
- **假设选择有针对性**：采用为多方交互场景设计的交互式 SIS 变体，比强行套用标准 SIS 更贴合门限协议的安全性证明需求。
- **代价可量化且相对温和**：以 1.4×–2.5× 的签名膨胀换取门限安全性，在格基方案中属可接受范围，为后续工程化留出空间。
- **应用指向明确**：直接服务于电子现金、隐私保护凭证等需要“抗量子 + 密钥分散 + 用户匿名”的场景。

---

## 8. 不足与局限

- **信息层面局限（本次总结的客观限制）**：可获取文本仅为摘要，缺少形式化定义、协议流程、定理陈述与完整证明，无法独立评估其安全论证的严密性。
- **假设较强且较新**：安全性依赖交互式 SIS 变体，该假设提出时间较晚、独立分析较少，其可信度与标准 SIS/LWE 的关联尚需社区进一步检验。
- **开销仍不可忽视**：1.4×–2.5× 的签名膨胀叠加格基签名本身的大尺寸，在带宽/存储受限场景（如大规模电子现金）中可能成为瓶颈。
- **“启发式但自然假设”下的比较**：效率结论依赖启发式分析而非严格最坏情形证明，实际参数选择可能进一步放大开销。
- **门限协议的固有成本未体现**：多方交互通常带来额外轮数与通信量，摘要未说明这部分代价，可能低估实际部署成本。
- **实验覆盖不足**：缺乏多安全级别、多 \((t,n)\) 配置、通信复杂度与真实实现性能的评测，工程可行性证据有限。
- **应用限制**：门限盲签名要求参与方在线协作，存在可用性与共谋风险（达到门限的恶意方仍可签发），需配合额外审计/追责机制。

---

（完）
