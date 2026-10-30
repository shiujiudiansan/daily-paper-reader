---
title: MPC-in-the-Head Framework without Repetition and its Applications to the Lattice-based Cryptography
title_zh: 免重复的MPC-in-the-Head框架及其在基于格密码学中的应用
authors: "Weihao Bai, Long Chen, Qianwen Gao, Zhenfeng Zhang"
date: 2024
publication_date: 2024
publication_date_precision: year
publication_date_source: conference year only; exact release date unverified
publication_date_kind: unknown
pdf: "https://www.computer.org/csdl/pds/api/csdl/proceedings/download-article/1Ub24hrQXle/pdf"
tags: ["query:pqc-acc"]
score: 4.0
evidence: 面向格的NIZKAoK证明可用于累加器成员证明，但本身非累加器
tldr: 本文针对MPC-in-the-Head类零知识证明需重复多次MPC求值、证明规模至少为电路大小λ倍的问题，提出免重复的Diet框架，使任意NP关系的非交互零知识论证的证明规模与时间对电路规模仅为多对数级。该框架并应用于基于格的密码学，构造了更紧凑的格基证明。其证明技术可支撑后量子累加器中成员与非成员证明的构造，但论文本身并不直接设计累加器。
source: IEEE-SP-2024-CSDL
selection_source: conference_retrieval
motivation: 现有MPC-in-the-Head证明需多次重复MPC求值，证明规模随电路线性膨胀。
method: 提出免重复的Diet框架，构造任意NP关系的非交互零知识论证，并用于格基密码。
result: 证明规模与时间对电路规模仅达多对数级，显著优于需重复的方案。
conclusion: 为格基后量子场景提供了紧凑零知识证明工具，可服务于累加器证明构造。
---

## Abstract
The MPC-in-the-Head framework has been proposed as a solution for Non-Interactive Zero-Knowledge Arguments of Knowledge (NIZKAoK) due to its efficient proof generation. However, most existing NIZKAoK constructions using this approach require multiple MPC evaluations to achieve negligible soundness error, resulting in proof size and time that are asymptotically at least λ times the size of the circuit of the NP relation. In this paper, we propose a novel method to eliminate the need for repeated MPC evaluations, resulting in a NIZKAoK protocol for any NP relation that we call Diet. The proof size and time of Diet are asymptotically only polylogarithmic with respect to the size of the circuit C of the NP relation, but are independent of the security parameter λ. Hence, both the proof size and time can be significantly reduced.Moreover, Diet offers promising concrete efficiency for proving Learning With Errors (LWE) problems and its variants. Our solution provides significant advantages over other schemes in terms of both proof size and proof time, when considering both factors together. Specifically, Diet is a promising method for proving knowledge of secret keys for lattice-based key encapsulation mechanisms (KEMs) such as Frodo and Kyber, offering a practical solution to future post-quantum certificate management. For Kyber 512, our implementation achieves an online proof size of 83.65 kilobytes (KB) with a preprocessing overhead of 152.02KB. The implementation is highly efficient, with an online proof time of only 0.68 seconds and a preprocessing time of 0.81 seconds. Notably, our approach provides the first reported implementation of proving knowledge of secret keys for Kyber 512 using post-quantum primitives-based zero-knowledge proofs.

---

## 论文详细总结（自动生成）

# 论文结构化总结：Diet —— 免重复的 MPC-in-the-Head 框架

## 1. 核心问题与整体含义（研究动机与背景）

- **背景**：MPC-in-the-Head（MPCitH）是构造非交互零知识论证（NIZKAoK）的重要范式：证明者在"头脑中"模拟 n 方安全多方计算（MPC），只公开部分参与方的视图，验证者据此相信证明者知道 witness。该范式因证明生成高效而被广泛用于后量子签名与格基证明。
- **核心痛点**：现有 MPCitH 方案（如 Katz-Kolesnikov-Wang、Baum-Nof 等）基于**半诚实安全 MPC**，单次执行的 soundness error 约为 1/C(n,2) 量级，必须**重复 MPC 求值 O(λn²) 次**才能把错误率压到 2^(-λ)。其后果是证明规模与证明时间**至少为电路规模 |C| 的 λ 倍**（O(λ|C|)），在大电路上开销巨大。
- **理论上的出路与疑问**：Ishai 等人（STOC'07）指出可用**恶意安全、完美鲁棒 MPC** 避免重复求值，使复杂度降为 O(|C|)+poly(λ)。但这类协议依赖复杂的信息论 VSS 与 packed Shamir 共享，**具体效率从未被充分验证**，"避免重复能否带来具体效率提升"仍是开放问题。
- **论文定位**：提出名为 **Diet** 的 NIZKAoK 框架，在不使用重量级恶意安全 MPC 的前提下消除重复求值，使证明规模/时间对 |C| 仅为多对数级、且**与安全参数 λ 无关**；并将其落地到 LWE/MLWE 证明与格基 KEM（Frodo、Kyber）的**私钥知识证明（KOSK）**，服务于后量子 PKI 证书管理。

## 2. 方法论：核心思想与关键技术

### 2.1 两个核心观察

- **观察一（对 Shamir 份额做一致性检查）**：不必采用 Ishai 等人的"恶意安全 + 完美鲁棒 MPC"路线。只需在**半诚实安全的 BGW 协议**基础上，让验证者**额外检查最终输出门广播的所有秘密份额是否属于同一多项式**。若恶意证明者想篡改 MPC 结果，必须改动至少 n−d 个广播份额，否则插值一致性检查会失败；soundness error 由"被选中子集 S 是否包含被篡改方"决定，界为 C(d,t)/C(n,t)，参数合适时可忽略。由此可用**简单 Shamir 共享替代复杂 VSS**，以低代价去掉 O(λ) 次重复。
- **观察二（承诺中间结果的随机份额）**：在 MPCitH 中"验证每个门"比"计算每个门"更便宜。对乘法门 x⋆y=z，传统做法需将 2d 次多项式 r(x) 降阶回 d（重共享需 O(n²) 通信，或需 Beaver 三元组/双随机共享并做 cut-and-choose 验证）。Diet 改为：证明者**额外承诺一个新的 d 次随机共享 [z]_d**，并公开 [x]_d·[y]_d − [z]_d = [0]_{2d} 的份额，验证者检查其插值一致性且重构为 0。只需多承诺一个多项式，显著省去预处理与通信开销。

### 2.2 基于 Packed Secret Sharing 的框架

- 采用 Franklin-Yung **打包秘密共享**：一次共享 l 维向量 x⃗，多项式次数 d，参与方 N，阈值 t ≤ d−l+1 且 2d ≤ N（保证乘法友好）。电路被转换为三类 **l-fold 门**：
  - **l-加法门**：本地计算 [z]_d = [x]_d + [y]_d，几乎零成本；
  - **l-乘法门**：计算 [z]_{2d} = [x]_d·[y]_d，再用上述"承诺新 d 次共享"方法降阶；
  - **线性变换门 T**：预处理生成随机对 ([r]_d, [T(r)]_d)；在线阶段揭示 x+r，计算并重共享 T(x+r)，本地相减得 [T(x)]_d。
- **随机变换对的生成与验证**：用 cut-and-choose + Schwartz-Zippel。生成 k+v+1 个随机向量对 (f_j, T(f_j))，用挑战 α 构造 β_j = Σ f_i α^i、γ_j = Σ T(f_i) α^i；前 k 组用于校验 T(β_j)=γ_j 及插值一致性（错误概率 ~ (k+v/|F|)^k），后 v 组用于实际门。**同一类变换的预处理可复用 v 次**，v=O(l) 时每门摊销成本 O(1)。
- **电路变换**：引用 Damgård-Ishai-Krogstrup 的 Beneš 网络方法，将任意 fan-in-2 算术电路转为 l-fold 门电路，规模 O((|C|log|C| + depth(C)²|C|log³|C|)/l)，深度 O(log²|C|·depth(C))。

### 2.3 安全性结论（Theorem 1、2）

- soundness error 界：C(2(t+l),t)/C(N,t) + ((k+v)/|F|)^k · [t/(2(t+l)+1)]·C(N−t, t+2l+1)；并给出知识提取器的期望调用次数上界 4/(ε̂−ε)·(1+ε̂·8(N−t)/(ε̂−ε))。
- 渐进复杂度：证明规模与计算代价为 O(|C|log|C| + depth(C)²|C|log³|C|/l + |ω|) + poly(λ)，**与 λ 无关**；对比传统 MPCitH 的 O(λ|C|)。

### 2.4 面向格问题的实例化

- **LWE（对应 Frodo）**：用线性变换门计算 AS，加法门得 AS+E=B；用乘法门证明每个系数落在 [−η,η]（如 s(s−1)=0 证二进制），成对相乘并重共享，最终公开 2d 次份额证明乘积为零。
- **MLWE（对应 Kyber）**：**不做 LWE 转换**（那会引入 n 倍乘法门），而是直接用 **NTT** 把环上多项式乘法化为系数逐点乘，用线性变换门处理 NTT 矩阵 T，再证明 b=As+e 与系数范围。

## 3. 实验设计

- **实现环境**：C++14，NTL 11.5.1、GMP 6.2.1、OpenSSL 的 SHA256。
- **基准场景与参数**：
  1. **批量乘法门 / 线性变换门微基准**：q=2^61、λ=128，l ∈ {128,256,512,1024,2048}，t ∈ {100,200}（共 2×5×2=20 组配置）。
  2. **LWE 证明**：m=n=2048、q=2^61、二进制 s,e（沿用 Baum-Nof 参数），(N,t,l)=(2140,200,512)、(k,v)=(23,4)。
  3. **Frodo640 KOSK**：q=2^15、|(s,e)|=10640、(N,t,l)=(2500,250,640)、(k,v)=(90,8)。
  4. **Kyber512 KOSK**：q=3329、k=2、η=2、(N,t,l)=(1454,150,256)、(k,v)=(70,4)。
  5. **批量摊销实验**：一次证明 {5,10,100,1000} 个 LWE 实例，测总/均摊证明规模与时间。
- **对比方法**（Table 1）：Stern（SIS）、Σ-protocol [24]、Ligero、Aurora、[29]（MLWE&MSIS）、**BN20 [13]（Baum-Nof，MPCitH）**；批量摊销图（Fig.3）另与 MPCitH、Ligero、Aurora 比较。
- **主要指标**：在线证明大小、预处理大小、在线证明时间、预处理时间、验证时间。

## 4. 资源与算力

- 文中**明确给出硬件**：14 英寸 Apple MacBook Pro，**Apple Silicon M1 Pro（3.2GHz，10 核），16GB RAM**；**纯 CPU 单机实验，未使用 GPU**。
- 未报告训练时长（本文非机器学习论文），但逐场景报告了证明/预处理/验证耗时；**未讨论多线程、并行化或大规模集群部署**，也未给出内存峰值等资源指标。

## 5. 实验数量与充分性

- **实验组数**：约 20+ 组参数化微基准（乘法门、线性变换门各 10 组），加上 LWE、Frodo640、Kyber512 三个端到端场景，以及 4 个批量规模的摊销实验；规模尚可。
- **充分性**：
  - 覆盖了从"单门微基准"到"完整 LWE/MLWE 实例"再到"真实 KEM KOSK"的层次，并验证了批量摊销趋势，结构较完整。
  - 但**缺少消融实验**（如去掉"输出份额一致性检查"或"承诺中间份额"后的对比），难以量化各技术贡献。
  - **对比公平性有限**：Table 1 中 Stern、Ligero、Aurora、[24]、[29] 等多为引用文献的估计值（表中"×"表示原文献无对应参数），并非同一机器/同一实现下的实测；真正同口径直接对比主要是 BN20。
  - 未与 Feneuil-Rivain [16] 等同样基于 Shamir 共享的 MPCitH 工作做实现级对比；也未测 SIS 等其他格问题变体（尽管声称适用）。
  - 参数选择（如 t、N、k、v）有说明（保证 soundness < 2^-128），但未系统扫描安全参数与效率的权衡。

## 6. 主要结论与发现

- **理论**：Diet 对任意 NP 关系实现证明规模与时间对 |C| 多对数、**独立于 λ**；每 l 个并行门摊销成本 O(1)，而传统 MPCitH 为 O(λ)。
- **具体效率**：
  - LWE：在线 245.88KB（+350KB 预处理），在线 0.528s（+5.596s 预处理）；相比 BN20 的 4.1MB/2.4s，**证明规模降低 ≥80%**，时间仍有竞争力。
  - Frodo640：473.51KB（+123.36KB），12.81s（+6.5s 预处理，验证 7.3s）；为**首个 Frodo640 的 NIZKAoK 实现**。
  - Kyber512：83.65KB（+152.02KB），0.68s（+0.81s 预处理，验证 0.84s）；为**首个基于后量子原语的 Kyber512 私钥知识证明实现**。
- **应用结论**：可用于后量子 PKI 中 KEM 证书的 KOS

K 证明，从而在不暴露私钥的前提下绑定公钥与私钥，抵御公钥替换、密钥重用等针对 KEM 证书的攻击；其证明规模（Frodo640 约 0.47 MB、Kyber512 约 84 KB）适合嵌入证书或随证书分发，验证时间（分别约 7.3 s 与 0.84 s）在证书校验场景中虽偏重，但已具备可行性。

- **批量摊销**：一次证明多个 LWE 实例时，预处理与承诺开销可显著摊薄，均摊证明规模与时间随批量增大而下降，验证了"避免重复求值 + 批量复用"的设计在批量场景下的收益；这与传统 MPCitH 必须按 λ 重复求值的刚性开销形成对比。

## 7. 局限、开放问题与未来工作

- **预处理成本突出**：在线阶段虽快（LWE 0.528 s、Kyber512 0.68 s），但预处理耗时分别为 5.596 s、0.81 s，Frodo640 的预处理与验证也分别达 6.5 s、7.3 s。论文的"轻在线、重离线"特性适合预处理可提前完成或复用的场景，但对一次性证明不友好；如何降低线性变换门随机对生成与 cut-and-choose 校验的成本，是实际部署的关键。
- **常数与参数规模**：理论复杂度对 |C| 为多对数级且与 λ 无关，但实际参数中参与方数 N 较大（LWE 2140、Frodo640 2500、Kyber512 1454），阈值为 t（100–250），导致证明中的份额数量与验证方重构开销不可忽略；安全参数与效率的权衡未被系统扫描，t、N、l、k、v 的选择仍偏经验性。
- **对比口径受限**：与 Ligero、Aurora、Stern、Σ-protocol 等的比较多引自原文献估计值，并非同机同实现实测；与同样基于 Shamir/打包共享的 MPCitH 工作（如 Feneuil-Rivain 系列）缺少实现级对比，因而"相对同类方案的绝对优势"尚不完全清楚。
- **模型与功能边界**：安全性在 Fiat-Shamir 随机预言机模型下成立，证明为 NIZKAoK；论文主要覆盖 LWE/MLWE 关系与 KEM 的 KOSK，未系统评估其他 NP 关系、布尔电路、深度很大的电路，或需要大量非线性变换的负载。对零知识性、证明者隐私泄漏（如视图分布）的讨论也相对简略。
- **工程优化空间**：实验为单机 CPU（Apple M1 Pro）、未使用 GPU、未报告多线程/并行化与内存峰值；NTT、承诺、哈希、域运算均有进一步优化余地。作者也将"更高效的打包共享实现""更紧的 soundness 界""扩展到更多后量子方案与协议"列为自然方向。

## 8. 总体评价与启示

- **理论贡献**：论文的核心价值在于用两个简洁观察——(i) 对 BGW 输出门广播份额做插值一致性检查，(ii) 承诺中间结果的随机 d 次共享以替代重共享——**绕开了重量级恶意安全 MPC 与完美鲁棒 VSS**，在保持半诚实 BGW 简洁性的同时消除 O(λ) 次重复。这把 Ishai 等人"理论可行但效率未验证"的路线转化为可实现的框架，并给出与 λ 无关的渐进复杂度与明确的 soundness 界，属于对 MPCitH 范式的实质性推进。
- **实验价值**：从门级微基准到 LWE、Frodo640、Kyber512 的端到端 KOSK，再到批量摊销，层次完整；Frodo640 与 Kyber512 的 KOSK 实现具有"首个"意义，说明该框架能落地到真实后量子 KEM 参数。LWE 场景相对 BN20 证明规模降低 ≥80%，是最有说服力的效率证据。
- **主要不足**：预处理开销大、参数未系统调优、对比公平性与消融不足，使"全面优于现有方案"的结论仍需更严格的同口径实验支撑；此外，验证方在 N 较大时的计算负担、证书场景中的延迟要求，也需进一步工程优化。
- **启示**：Diet 表明，MPCitH 的效率瓶颈未必只能靠"更强的 MPC 安全模型"解决，**针对具体检查点做轻量一致性验证 + 用承诺换通信**同样能去掉重复；这一思路可迁移到其他基于秘密共享的零知识论证与签名构造中。对后量子 PKI 而言，KEM 的 KOSK 证明已从"理论可行"走向"规模可接受"，但距离低延迟、大规模部署仍有优化空间。

（完）
