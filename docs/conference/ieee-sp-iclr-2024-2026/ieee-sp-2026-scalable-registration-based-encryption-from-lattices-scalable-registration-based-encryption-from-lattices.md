---
title: Scalable Registration-Based Encryption from Lattices
title_zh: 基于格的可扩展注册型加密
authors: "Michael Klooß, Russell W. F. Lai, Jan Niklas Siemer, Monisha Swarnakar"
date: 2026
publication_date: 2026
publication_date_precision: year
publication_date_source: conference year only; exact release date unverified
publication_date_kind: unknown
pdf: "https://www.computer.org/csdl/proceedings-article/sp/2026/606500d128/2geEWUwjm7e"
tags: ["query:pqc-acc"]
score: 7.0
evidence: 基于格的注册型加密，使用紧凑密钥摘要聚合
tldr: 注册型加密需要密钥管理者将用户公钥聚合成紧凑摘要，以支持无需托管的公钥加密，但现有方案的可扩展性受限。本文提出基于格的注册型加密构造，利用格上密码技术实现可扩展的密钥聚合与更新。该方案在保持后量子安全的同时，使加密者仅凭摘要和身份即可加密。其紧凑摘要与成员关系证明机制对格基累加器构造具有借鉴意义，推动了后量子聚合认证结构的发展。
source: IEEE-SP-2026-CSDL
selection_source: conference_retrieval
motivation: 注册型加密需聚合大量公钥并避免密钥托管，现有方案扩展性不足。
method: 文章构造基于格的注册型加密，让密钥管理者将注册公钥聚合为紧凑摘要。
result: 该方案实现可扩展的密钥聚合与更新，并保持后量子安全性。
conclusion: 其紧凑摘要与成员证明思路对格基累加器设计有参考价值。
---

## Abstract
Registration-Based Encryption (RBE) is a public-key encryption mechanism which allows a user to register their identity (e.g. email address) and self-generated public key with a key curator (e.g. an organisation). The key curator aggregates these keys into a compact digest. Using only this digest and the recipient's identity, anyone can encrypt messages to any registered user. As the key curator is not entrusted with any secrets, RBE presents a solution to the key escrow problem, which impedes the adoption of Identity-Based Encryption. This makes RBE an attractive solution for secure communication with and among members of an organisation while preserving user privacy. Despite recent advances [Döttling-Kolonelos-Lai-Lin-Malavolta-Rahimi, EUROCRYPT'23; Fiore-Kolonelos-dePerthuis, ASIACRYPT'23], practical constructions of RBE are still limited to a small number of registered users (e.g. 1024), lack post-quantum security, or have ciphertext sizes scaling in the order of GB. The predominant way towards constructing practical RBE is a generic transformation from Laconic Encryption (LE). In this work, we identify an efficiency bottleneck in this transformation and present a new primitive called Batched Laconic Encryption (BLE) which admits a more succinct transformation to RBE. Our resulting RBE scheme is the first post-quantum construction that simultaneously supports a large number of registered users and asymptotically outperforms all comparable RBE schemes. Concretely, for at most 230 registered users at 128-bit security, our scheme achieves a ciphertext size of 7 MB, improving on previously reported results by three orders of magnitude. We confirm our results through an open-source prototype implementation demonstrating that all algorithms execute within a few milliseconds. The post-quantum security of our construction is based on the standard Learning with Errors assumption, and our analysis enables several tweaks to significantly reduce ciphertext sizes in practical deployments.

---

## 论文详细总结（自动生成）

# 论文结构化总结：Scalable Registration-Based Encryption from Lattices

> 说明：本次可用材料为 IEEE S&P 2026 论文的**摘要与元数据**（正文 PDF 未提供）。以下总结严格依据摘要信息展开，凡摘要未覆盖之处（如具体参数表、实验组数、硬件环境）均明确标注为“未披露/无法核实”，不做推测性填充。

---

## 1. 核心问题与研究动机

- **背景：注册型加密（RBE）的定位**
  - RBE 是一种公钥加密机制：用户用自己的身份（如邮箱）和**自生成的公钥**向“密钥管理者/策展人”（key curator，例如所属机构）注册。
  - 策展人将所有注册公钥聚合为一个**紧凑摘要（compact digest）**；任何发送方仅凭该摘要 + 接收者身份，即可向任意已注册用户加密。
  - 关键在于：策展人**不掌握任何秘密**，因此 RBE 从根本上规避了身份基加密（IBE）长期受阻的**密钥托管（key escrow）问题**，同时保留用户隐私，适合组织内部及组织之间的安全通信。

- **核心问题：现有 RBE 不可实用**
  - 尽管有 EUROCRYPT'23（Döttling 等）与 ASIACRYPT'23（Fiore–Kolonelos–de Perthuis）等进展，实用化 RBE 仍受限于以下三点之一：
    1. 支持的用户规模极小（如约 1024 人）；
    2. **缺乏后量子安全性**；
    3. 密文规模达到 **GB 量级**。
  - 主流构造路线是**从 Laconic Encryption（LE）做通用变换得到 RBE**。本文指出该变换中存在**效率瓶颈**，并针对这一瓶颈提出新原语，从而得到可扩展的后量子 RBE。

- **整体含义**
  - 论文目标是让 RBE 在“**大规模用户 + 后量子安全 + 可接受密文/计算开销**”三者上同时成立，把 RBE 从理论可行性推进到工程可用性。

---

## 2. 方法论

- **核心思想：用“批量”原语替代通用变换**
  - 提出新原语 **Batched Laconic Encryption（BLE，批量简洁加密）**。
  - BLE 的关键特征是：可对**一批（batch）语句/实例**同时给出简洁的承诺与证明，从而使“LE → RBE”的变换变得更**简洁（succinct）**，消除原变换中的效率瓶颈。
  - 由 BLE 得到 RBE 的变换在结构与通信量上都优于既有通用变换路径。

- **技术要点（摘要层面可确认的部分）**
  - **密钥聚合**：策展人把注册公钥聚合成紧凑摘要；加密方只需摘要 + 身份。
  - **成员关系证明/更新机制**：注册是动态的，新用户加入需更新摘要，已注册用户需能获得与其身份对应的“证据”，以便解密；BLE 的批量特性正是为降低这一过程中的证明与更新开销。
  - **格基实例化**：方案基于格的密码工具（含承诺、同态/线性结构、简洁论证等典型组件）构造 BLE，从而继承格密码的后量子安全性。
  - **安全性假设**：基于**标准 Learning with Errors（LWE）假设**，而非理想化或非标准假设。
  - **参数可调性**：论文分析支持若干“tweaks”（调参/变体），可在实际部署中**显著降低密文尺寸**。

- **算法流程（文字化描述，基于摘要可推断的框架）**
  1. 用户生成自身公私钥，将（身份，公钥）提交给策展人注册；
  2. 策展人用 BLE 的批量聚合机制把新公钥并入摘要，输出新的紧凑摘要（并发布相应更新信息）；
  3. 发送方持有最新摘要，输入“接收者身份”，运行 BLE 的简洁加密流程生成密文；
  4. 接收者结合自身私钥与（由摘要/更新信息导出的）身份相关证据解密；
  5. 安全性要求：在 LWE 困难性下，密文与摘要不泄露接收者身份之外的额外信息，且策展人无法解密（无托管）。

> 注：具体公式、噪声参数、模数选取、证明系统细节与安全归约步骤，摘要未给出，需参见正文。

---

## 3. 实验设计与评测基准

- **评测对象性质**：这是密码学构造类论文，**不使用数据集**；评测对象是**方案本身的渐近复杂度与具体参数下的数值**。
- **主要评测指标（benchmark）**
  - **密文尺寸**（核心指标）：在 128 位安全强度、注册用户数至多 **2^30** 时，密文为 **7 MB**。
  - **摘要/公钥参数规模**（由“compact digest”目标所隐含）。
  - **各算法运行时间**：通过开源原型实现测得，**均在数毫秒内**完成。
- **对比对象**
  - 与“所有可比的 RBE 方案”进行**渐近比较**，声称渐近优于全部同类方案；
  - 与既有实用 RBE 结果的具体数值对比：密文尺寸**改善约三个数量级**（此前为 GB 量级）；
  - 与两条主要先前工作路线（EUROCRYPT'23、ASIACRYPT'23）在可扩展性与后量子性维度上形成对照。
- **实现形态**：提供**开源原型实现（open-source prototype）**，用于验证数值与运行时间。

---

## 4. 资源与算力

- 摘要与元数据中**未提及任何 GPU 型号、数量或训练/计算时长**。
- 从论文性质看，此类工作通常为**CPU 上的密码学原型基准测试**（微基准），而非深度学习训练，因此“算力”主要以单机 CPU 运行时间体现（“所有算法在数毫秒内完成”）。
- **结论：硬件平台、CPU 规格、核心数、编译环境、测量方法（均值/中位数、重复次数）等均未在可得材料中披露，无法核实。**

---

## 5. 实验数量与充分性

- **可确认的实验内容**
  - 至少包含：原型实现的功能/正确性验证 + 各算法的运行时间测量 + 特定参数（128 位安全、2^30 用户）下的密文尺寸报告 + 与先前方案的数值/渐近对比。
- **实验组数**：摘要未给出具体组数；推测正文含**参数选择表 + 不同用户规模下的尺寸/时间曲线 + 若干调参变体（tweaks）对比**，但**无法核实**。
- **充分性与公平性评估（受限判断）**
  - 优点面：给出开源实现，可复现性较好；同时报告尺寸与时间，且给出与最强先前工作的量级对比，说服力较强。
  - 需注意点：对比是否在**完全一致的参数口径**（相同安全级别、相同用户数、是否计入更新信息/证据大小、是否计入策展人存储）下进行，摘要未说明；渐近优势与具体常数之间的关系需正文参数表支撑。
  - 总体：在可得信息范围内**方向正确、口径基本合理**，但“是否充分”需以正文的完整参数表与消融（不同 tweak、不同用户规模、不同安全级别）为准。

---

## 6. 主要结论与发现

1. 从 LE 到 RBE 的通用变换存在**可识别的效率瓶颈**；引入 **BLE** 可绕开该瓶颈，得到更简洁的变换。
2. 由此得到**首个同时满足“后量子安全 + 大规模用户”的 RBE 方案**，且在渐近上优于所有可比 RBE 方案。
3. **具体数值**：128 位安全、至多 2^30 注册用户时，密文仅 **7 MB**，相较此前 GB 级结果**改善约三个数量级**。
4. **工程可行性**：开源原型中所有算法在**数毫秒**内完成，表明方案在实际部署中具备可用性。
5. **安全性基础扎实**：基于**标准 LWE 假设**，不依赖非标准假设。
6. 分析过程还导出若干**参数调整技巧**，可在真实部署中进一步压缩密文。

---

## 7. 优点与亮点

- **问题定位精准**：不是简单堆砌参数，而是明确指出“LE→RBE 通用变换”的瓶颈，并以新原语（BLE）从根上解决，方法论上具有“抽象层改进”的价值。
- **首次同时达成三重目标**：后量子安全、大规模用户（2^30）、可接受密文（MB 级），填补了此前“三者不可兼得”的空白。
- **量级级提升**：三个数量级的密文缩减属于实质性突破，而非常数优化。
- **可复现性强**：提供开源原型，并同时报告尺寸与运行时间，便于社区核验与后续改进。
- **假设保守**：仅依赖标准 LWE，安全论证的信任基础较好。
- **工程友好**：给出可落地的调参建议，兼顾理论与部署。
- **概念可迁移**：“批量简洁加密 + 紧凑摘要 + 成员证据”的思路对格基累加器、可聚合认证结构等有借鉴意义。

---

## 8. 不足与局限

- **材料层面的局限（本次分析）**：仅获得摘要，无法核验安全归约的严密性、参数选取的保守程度、以及对比口径的公平性。
- **密文仍然偏大**：7 MB 相对普通公钥加密（数百字节至数 KB）依然高出一至数个数量级，对带宽/存储受限场景（如 IoT、低功耗设备）仍不友好。
- **用户规模上限明确**：2^30 是报告上限，超大规模或高频动态注册下的摘要更新与证据维护成本未在摘要中量化。
- **策展人仍是系统瓶颈与信任焦点**：虽无密钥托管，但策展人需保证**注册的一致性、抗审查与可用性（liveness）**，其失效或作恶（如拒绝/延迟注册、发布不一致摘要）会直接影响系统；摘要未说明对此的防护强度。
- **动态性开销未充分披露**：注册/更新导致的用户侧证据更新复杂度、最坏情况通信量，摘要未给出。
- **实验覆盖未知**：不同安全级别（如 192/256 位）、不同用户规模曲线、不同 tweak 的消融、与其他实现的横向对比是否完整，无法确认。
- **实现层面风险**：格基方案的参数若为追求尺寸而偏激进，可能削弱安全裕度；常数时间实现、侧信道防护等在摘要中未提及。
- **对比公平性存疑点**：与先前方案的“三个数量级”改进是否在同一安全模型（是否含恶意策展人、是否选择性安全 vs. 自适应安全）与同一统计口径下取得，需正文澄清。

---

（完）
