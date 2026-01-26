### Abstract
[原文段落]
We present a unified multi-objective model for targeting both advertisements and promotions within the Spotify podcast ecosystem. Our approach addresses key challenges in personalization and cold-start initialization, particularly for new advertising objectives. By leveraging transfer learning from large-scale ad and content interactions within a multi-task learning (MTL) framework, a single joint model can be fine-tuned or directly applied to new or low-data targeting tasks, including in-app promotions. This multi-objective design jointly optimizes podcast outcomes such as streams, clicks, and follows for both ads and promotions using a shared representation over user, content, context, and creative features, effectively supporting diverse business goals while improving user experience.
Online A/B tests show up to a 22% reduction in effective Cost-Per-Stream (eCPS), particularly for less-streamed podcasts, and an 18–24% increase in podcast stream rates. Offline experiments and ablations highlight the contribution of ancillary objectives and feature groups to cold-start performance. Our experience shows that a unified modeling strategy improves maintainability, cold-start performance, and coverage, while breaking down historically siloed targeting pipelines. We discuss practical trade-offs of such joint models in a real-world advertising system.

[中文翻译]
本文提出了一种统一的多目标模型，用于在Spotify播客生态系统中同时实现广告投放和推广内容定向。该方法解决了个性化推荐和冷启动初始化中的关键挑战，尤其针对新的广告目标场景。通过在多任务学习（MTL）框架中利用来自大规模广告和内容交互数据的迁移学习，单个联合模型可经过微调或直接应用于新的或低数据量的定向任务，包括应用内推广。这种多目标设计通过对用户、内容、场景和创意特征构建共享表示，联合优化广告和推广的播客相关成果（如播放量、点击量和关注量），在有效支持多样化业务目标的同时提升用户体验。
线上A/B测试结果显示，有效每播放成本（eCPS）最高降低22%（尤其针对播放量较少的播客），播客播放率提升18%-24%。离线实验和消融分析突显了辅助目标和特征组对冷启动性能的积极作用。实践表明，统一建模策略提升了模型的可维护性、冷启动性能和覆盖范围，同时打破了历史上相互独立的定向流程。本文还探讨了此类联合模型在实际广告系统中的实际权衡问题。

### CCS Concepts
[原文段落]
• Information systems ￫Computational advertising; Recommender systems; Online advertising; • Computing methodologies ￫Multi-task learning.

[中文翻译]
• 信息系统 ￫计算广告；推荐系统；在线广告；• 计算方法 ￫多任务学习。

### Keywords
[原文段落]
Online advertising; Multi-task learning; Recommender systems

[中文翻译]
在线广告；多任务学习；推荐系统

### ACM Reference Format
[原文段落]
Shivam Verma, Hannes Karlbom, Yu Zhao, Nick Topping, Vivian Chen, Kieran Stanley, and Bharath Rengarajan. 2026. Cold-Starting Podcast Ads and Promotions with Multi-Task Learning on Spotify. In Proceedings of the Nineteenth ACM International Conference on Web Search and Data Mining (WSDM ’26), February 22–26, 2026, Boise, ID, USA. ACM, New York, NY, USA, 5 pages. https://doi.org/10.1145/3773966.3779388

[中文翻译]
Shivam Verma、Hannes Karlbom、Yu Zhao、Nick Topping、Vivian Chen、Kieran Stanley 和 Bharath Rengarajan. 2026. 基于多任务学习的Spotify播客广告与推广冷启动方案. 第十九届ACM国际网络搜索与数据挖掘会议论文集（WSDM ’26）, 2026年2月22日-26日, 美国爱达荷州博伊西. ACM出版社, 美国纽约州纽约市, 5页. https://doi.org/10.1145/3773966.3779388

### 1 Motivation
[原文段落]
Spotify, with its user base of over 700 million, identifies podcasts as a significant and rapidly growing content vertical, making effective personalization crucial for listener engagement, monetization (especially for over 400 million ad-supported users), and the discovery and growth of podcast creators. The platform supports diverse business objectives, from driving initial streams for new episodes to optimizing impression-to-stream rates (i2s) and clickthrough rates (CTR), with a particular focus on boosting visibility for less-streamed creators who suffer from cold-start issues due to data scarcity. Two primary mechanisms connect users with podcast content: advertisements (ads), such as in-stream audio or video placements (Fig. 1a, 1b), and promotions, which surface strategically important or relevant content like display promotions for Spotify Originals (Fig. 1c). Despite different immediate objectives (an ad click versus a direct stream from a promotion), both channels share the goal of matching users with relevant and engaging podcasts, driven by similar user signals (e.g., listening history, explicit follows) and content affinities (e.g., genre, topics). Positive interactions in one channel can therefore inform decisions in the other.

[中文翻译]
Spotify拥有超过7亿用户，将播客视为重要且快速增长的内容领域。有效的个性化推荐对于提升听众参与度、实现商业化（尤其针对4亿多广告支持用户）以及助力播客创作者的内容发现与成长至关重要。该平台支持多样化的业务目标，从为新剧集获取初始播放量，到优化曝光-播放转化率（i2s）和点击率（CTR），同时特别关注那些因数据稀缺而面临冷启动问题的低播放量创作者，致力于提升其内容曝光度。连接用户与播客内容的两大核心机制为：广告（如流内音频或视频广告投放，见图1a、1b）和推广（即展示具有战略重要性或相关性的内容，如Spotify原创播客的展示推广，见图1c）。尽管两者的直接目标不同（广告追求点击量，推广追求直接播放量），但两大渠道的核心目标一致——为用户匹配相关且有吸引力的播客，且均依赖相似的用户信号（如收听历史、主动关注）和内容偏好（如类型、主题）。因此，一个渠道中的正向交互数据可为另一个渠道的决策提供参考。

[原文图注]
Figure 1: Left to right: (a) An in-stream podcast audio ad. (b) An in-stream unmuted podcast video ad. (c) A display promotion for a Spotify Original podcast.

[中文翻译]
图1：从左至右：(a) 流内播客音频广告；(b) 流内非静音播客视频广告；(c) Spotify原创播客的展示推广。

[原文段落]
Historically, these objectives were handled by separate, specialized machine learning models. For example, a model optimizing i2s for a Home-page promotion would be distinct from a model optimizing clicks on an audio ad for a new podcast series. This siloed, task-specific approach created several challenges. First, slow innovation: introducing new business or ad objectives requires building new models from scratch, involving substantial engineering, data collection, and A/B testing, which can slow the rollout of tools that help podcasters reach relevant audiences. Second, the cold-start problem for optimization objectives: newly introduced ad or promotional products for specific audiences, such as the “likelihood to stream advertised content after an ad” for emerging or new creators, often lack sufficient interaction data to train high-performing specialized models, hampering the discoverability of these less-streamed creators. Third, inefficiency and missed synergies: separate pipelines made it difficult to exploit shared latent patterns and overlapping signals across podcast ads and promotions, and led to siloed teams building similar models for related products.

[中文翻译]
历史上，这些目标由相互独立的专用机器学习模型分别处理。例如，优化首页推广曝光-播放转化率（i2s）的模型，与优化新播客系列音频广告点击率的模型是完全分离的。这种独立的、面向特定任务的方法带来了诸多挑战：首先，创新速度缓慢——引入新的业务或广告目标需要从零构建新模型，涉及大量工程开发、数据收集和A/B测试工作，这会延缓助力播客创作者触达目标受众的工具落地速度；其次，优化目标的冷启动问题——针对特定受众的新广告或推广产品（如面向新兴创作者的“广告后播放广告内容的可能性”预测）往往缺乏足够的交互数据来训练高性能专用模型，从而阻碍了这些低播放量创作者的内容发现；第三，效率低下且错失协同效应——独立的流程难以挖掘播客广告和推广之间的共享潜在模式与重叠信号，同时导致独立团队为相关产品构建相似模型，造成资源冗余。

[原文段落]
These challenges motivated our exploration of a unified objective optimization approach via multi-task learning (MTL).

[中文翻译]
这些挑战促使我们探索基于多任务学习（MTL）的统一目标优化方法。

### 2 Related Work
[原文段落]
Multi-task learning improves performance by jointly learning related tasks [5–7, 11, 13, 14, 16, 17, 26], facilitating transfer from data-rich advertising tasks to data-scarce promotional ones (and vice versa), thereby addressing cold-start issues for newer and smaller creators. By modeling ads and promotions together, we aim to consolidate learning, reduce duplication across systems, and accelerate new capability deployment, ultimately improving personalization for listeners and growth for creators. Our contribution focuses on bridging organizational silos by grouping tasks based on business goal alignment in multi-stakeholder and multi-objective settings [8, 9, 15, 17, 19, 25, 29], which is crucial for balancing diverse business objectives.

[中文翻译]
多任务学习通过联合学习相关任务来提升性能[5-7, 11, 13, 14, 16, 17, 26]，能够促进从数据丰富的广告任务向数据稀缺的推广任务的知识迁移（反之亦然），从而解决新兴和小型创作者的冷启动问题。通过将广告和推广联合建模，我们旨在整合学习过程、减少系统间的重复开发、加速新功能部署，最终提升听众的个性化体验和创作者的成长空间。我们的贡献重点在于，在多利益相关者和多目标场景下，通过基于业务目标一致性对任务进行分组，打破组织层面的壁垒[8, 9, 15, 17, 19, 25, 29]，这对于平衡多样化的业务目标至关重要。

[原文段落]
Multi-Task Learning in Industry Recommenders. MTL improves generalization by jointly learning related objectives [3]. At industrial scale, platforms have adopted MTL to couple heterogeneous business goals such as engagement, satisfaction, and monetization [1, 7, 16, 20, 24, 26, 28], highlighting the value of shared representations while carefully managing interference. Our work follows this line but specifically targets the joint modeling of podcast ads and promotions within a single framework.

[中文翻译]
工业界推荐系统中的多任务学习。多任务学习通过联合学习相关目标提升泛化能力[3]。在工业级规模下，各大平台已采用多任务学习来整合不同的业务目标，如参与度、满意度和商业化收益[1, 7, 16, 20, 24, 26, 28]，这凸显了共享表示的价值，同时需要谨慎处理任务间的干扰。我们的研究遵循这一方向，但专注于在单一框架内实现播客广告和推广的联合建模。

[原文段落]
Joint Optimization and Task Relatedness. A central challenge in MTL is trading off objectives that may conflict. Viewing MTL as multi-objective optimization provides principled ways to navigate Pareto trade-offs [2, 8, 11, 17, 29]. Another line of work studies when tasks should be learned together, showing that task affinity or relatedness strongly affects transfer [19]. In our setting, we unify advertising and promotions within one model because they share user and content signals, while still needing to control cross-objective interference.

[中文翻译]
联合优化与任务相关性。多任务学习的核心挑战之一是对可能冲突的目标进行权衡。将多任务学习视为多目标优化问题，为处理帕累托权衡提供了原则性方法[2, 8, 11, 17, 29]。另一类研究探讨了任务应何时联合学习，结果表明任务间的关联性或相似性对知识迁移效果有显著影响[19]。在我们的场景中，由于广告和推广共享用户和内容信号，因此将其整合到同一模型中，但同时仍需控制跨目标的干扰。

[原文段落]
Mitigating Negative Transfer. Negative transfer arises when gradients from different objectives conflict. Industry-ready approaches include learning to weight task losses (e.g., uncertainty weighting) [10], gradient balancing/normalization [4], and gradient surgery to resolve conflicts (PCGrad) [27], as well as work on stabilizing large-scale multitask ranking models in production [23]. Architectural remedies such as MMoE share experts with task-specific gating to reduce interference at scale [14], and PLE introduces progressive shared/specific towers to further curb negative transfer in recommendation tasks [22]. Our approach combines unified modeling with imbalance-aware training and careful sharing to retain positive transfer while limiting interference.

[中文翻译]
负迁移缓解。当不同目标的梯度发生冲突时，会产生负迁移。工业界成熟的解决方案包括学习任务损失权重（如不确定性加权）[10]、梯度平衡/归一化[4]、通过梯度修正解决冲突（如PCGrad）[27]，以及针对生产环境中大规模多任务排序模型的稳定性优化研究[23]。架构层面的改进方案包括：MMoE通过任务特定的门控机制共享专家网络，以规模化减少干扰[14]；PLE引入渐进式共享/专用塔结构，进一步抑制推荐任务中的负迁移[22]。我们的方法将统一建模与不平衡感知训练相结合，并通过谨慎的参数共享策略，在保留正迁移的同时限制负迁移。

### 3 System Evolution and Architecture

[原文段落]
We evolved from specialized models to a unified multi-task learning (MTL) framework that jointly optimizes podcast-related ad and promotion objectives. We first summarize the baselines and then formalize the joint ads–promotions model, including task definitions and training setup.

[中文翻译]
我们的系统从专用模型逐步演进为统一的多任务学习（MTL）框架，该框架可联合优化与播客相关的广告和推广目标。本节首先概述基准模型，然后形式化定义广告-推广联合模型，包括任务定义和训练设置。

#### 3.1 Baseline Models and Initial Approaches
[原文段落]
Figure 2A shows our initial promotions-only multi-task model. Each training example is an impression of a podcast promotion shown to a user. A shared feature encoder (with post-batch norm application) feeds task-specific towers-stacked MLPs-that predict user–podcast interactions (e.g., stream, click, like, follow) for promotions.

[中文翻译]
图2A展示了我们最初的仅面向推广的多任务模型。每个训练样本对应一次向用户展示的播客推广曝光记录。共享特征编码器（应用批量归一化后处理）将特征输入任务专用塔结构（堆叠的多层感知器），该塔结构用于预测推广场景下的用户-播客交互行为（如播放、点击、点赞、关注）。

[原文段落]
The encoder consumes four feature groups: (1) user signals (historical listening, follows, search interactions, high-level profile attributes), (2) content signals (show and episode identifiers, learned embeddings, genres, topics), (3) context (time, surface, session state), and (4) promotion metadata (slot, layout, campaign). We also considered Mixture-of-Experts (MoE) [12, 14, 18, 21] variants, but the shared-bottom model served as the main production baseline.

[中文翻译]
编码器接收四类特征组：(1) 用户信号（历史收听记录、关注列表、搜索交互、高层用户画像属性）；(2) 内容信号（节目和剧集标识、学习得到的嵌入向量、类型、主题）；(3) 场景信息（时间、展示位置、会话状态）；(4) 推广元数据（广告位、布局、活动）。我们也考虑了混合专家模型（MoE）[12, 14, 18, 21]的变体，但共享底部模型仍是生产环境中的主要基准模型。

[原文段落]
Two intermediate approaches are shown in Figures 2A and 2B:
(1) Promotions model for ad cold-start. We reused the promotions model to score ad impressions. This enabled rapid launches for new ad objectives but ignored ad-specific features (e.g., creative type, campaign) and user–ad interaction patterns.
(2) Single-task ads model. We built an ads-only model trained across all podcast ad surfaces and creatives (audio, video, display). It used similar user, content, and context features, plus ad-specific metadata (creative ID, format, campaign, slot). Despite rich ad logs, this single-task approach struggled to balance diverse business objectives effectively and support future goals requiring learning from all on-platform podcast interactions.

[中文翻译]
图2A和图2B展示了两种过渡性方法：
(1) 用于广告冷启动的推广模型。我们复用推广模型对广告曝光进行评分。这种方法能够快速上线新的广告目标，但忽略了广告专用特征（如创意类型、活动）和用户-广告交互模式。
(2) 单任务广告模型。我们构建了仅面向广告的模型，训练数据覆盖所有播客广告展示位置和创意形式（音频、视频、展示类）。该模型使用与推广模型相似的用户、内容和场景特征，并补充了广告专用元数据（创意ID、格式、活动、广告位）。尽管拥有丰富的广告日志，但这种单任务方法难以有效平衡多样化的业务目标，也无法支持需要从平台内所有播客交互中学习的未来目标。

[原文段落]
Maintaining separate data pipelines and models increased engineering overhead and limited our ability to exploit shared structure across tasks, motivating a unified solution.

[中文翻译]
维护独立的数据管道和模型增加了工程开销，且限制了我们挖掘任务间共享结构的能力，这促使我们寻求统一的解决方案。

[原文图注]
Figure 2: (A) A promotions-only podcast model, used to serve ad stream predictions in the cold-start phase for the Ads objective. (B) Single-task p(ad stream) model incorporating both promotions and ads data. (C) Multi-task joint model for promotions and ads, serving both businesses.

[中文翻译]
图2：(A) 仅面向推广的播客模型，用于广告目标冷启动阶段的广告播放量预测；(B) 单任务广告播放量预测模型，整合了推广和广告数据；(C) 广告与推广联合多任务模型，同时服务于两大业务场景。

#### 3.2 Problem Formulation for Joint Ads–Promotions Modeling

[原文段落]
We treat targeting as predicting multiple per-impression outcomes for a user–podcast pair \((u, c)\) in context \(###\) (e.g., surface, time, device). Let T be the set of binary prediction tasks, including:
• PromotionStream: Stream after a promotion impression;
• AdStream: Stream after an ad impression;
• Click: Click on a promotion or ad;
• Like or Follow: Like / follow of a promoted podcast.

[中文翻译]
我们将定向问题视为：在给定场景 ###（如展示位置、时间、设备）下，预测用户-播客对$(u, c)$的多个单次曝光结果。设T为二分类预测任务集合，包括：
• PromotionStream（推广播放）：推广曝光后产生播放行为；
• AdStream（广告播放）：广告曝光后产生播放行为；
• Click（点击）：点击推广内容或广告；
• Like or Follow（点赞或关注）：对推广播客进行点赞或关注。

[原文段落]
For each task \(t \in T\) , we observe a binary label \(y_{t} \in\{0,1\}\) . Given input features \(###\) , the model produces task-specific probabilities \(p_{t}(x)=f_{\theta, t}(x)\) , with shared and task-specific parameters θ.
[中文翻译]
对于每个任务\(t \in T\)，我们观察到二分类标签\(y_{t} \in\{0,1\}\)。给定输入特征\(###\)，模型输出任务特定的概率\(p_{t}(x)=f_{\theta, t}(x)\)，其中θ包含共享参数和任务专用参数。

[原文段落]
The unified model (Figure 2C) consists of:
• a shared encoder \(h_{\phi}(x)\) that maps user, content, context, and creative features into a joint representation \(z=h_{\phi}(x)\) ;
• task-specific towers \(g_{\psi_{t}}(z)\) that map 𝑧to logits for each task 𝑡.
[中文翻译]
统一模型（图2C）包括：
• 共享编码器\(h_{\phi}(x)\)：将用户、内容、场景和创意特征映射为联合表示\(z=h_{\phi}(x)\)；
• 任务专用塔结构\(g_{\psi_{t}}(z)\)：将联合表示𝑧映射为每个任务𝑡的对数几率（logits）。

[原文段落]
The predicted probability for task 𝑡is 
\[p_{t}(x)=\sigma\left(g_{\psi_{t}}\left(h_{\phi}(x)\right)\right),\]
where \(\sigma(\cdot)\) is the sigmoid function. Architecturally, the shared encoder mirrors the promotions baseline but incorporates ads-specific features and includes both ads and promotions tasks in τ , enabling joint learning over all podcast-related interactions while retaining task-specific capacity.
[中文翻译]
任务𝑡的预测概率为：
\[p_{t}(x)=\sigma\left(g_{\psi_{t}}\left(h_{\phi}(x)\right)\right),\]
其中\(\sigma(\cdot)\)为sigmoid函数。在架构上，共享编码器与推广基准模型一致，但整合了广告专用特征，并将广告和推广任务均纳入任务集合τ中，从而能够在所有播客相关交互数据上进行联合学习，同时保留任务专用的建模能力。

#### 3.3 Optimization and Loss Balancing
[原文段落]
We optimize binary cross-entropy losses over all tasks in τ , but with two design choices to control transfer between channels: (1) adaptive loss masking from ads to promotions, and (2) source-balanced sampling between promotions and ads.
[中文翻译]
我们对τ中的所有任务优化二元交叉熵损失，但通过两项设计来控制渠道间的知识迁移：(1) 从广告到推广的自适应损失掩码；(2) 推广和广告数据的来源平衡采样。

[原文段落]
Let \(T^{P}\) and \(T^{A}\) denote the sets of promotion and ad tasks respectively, with \(T=T^{P} \cup T^{A}\) . We write \(D^{P}\) and \(D^{A}\) for the corresponding sets of promotion and ad impressions, and \(D=D^{P} \cup D^{A}\) Each impression \(x \in D\) has a source label \(s(x) \in\{P, A\}\)
[中文翻译]
设\(T^{P}\)和\(T^{A}\)分别表示推广任务集和广告任务集，满足\(T=T^{P} \cup T^{A}\)。\(D^{P}\)和\(D^{A}\)分别表示对应的推广曝光数据集和广告曝光数据集，且\(D=D^{P} \cup D^{A}\)。每个曝光样本\(x \in D\)都有一个来源标签\(s(x) \in\{P, A\}\)（P代表推广，A代表广告）。

[原文段落]
We define a binary mask \(m_{s, t}\) that dictates whether task 𝑡should incur loss on an impression from source 𝑠: 
\[m_{s, t}= \begin{cases}0, & if s=A and t \in \mathcal{T}^{P}, \\ 1, & otherwise. \end{cases}\]
[中文翻译]
我们定义二元掩码\(m_{s, t}\)，用于指定任务𝑡是否需要对来源为𝑠的曝光样本计算损失：
\[m_{s, t}= \begin{cases}0, & 若s=A且t \in \mathcal{T}^{P}, \\ 1, & 其他情况. \end{cases}\]

[原文段落]
This implements directional transfer: promotion impressions update both promotion and ad towers, while ad impressions update only ad towers. The overall training objective is 
\[\mathcal{L}=\sum_{t \in \mathcal{T}} \lambda_{t} \mathbb{E}_{\left(x, y_{t}\right) \sim \mathcal{D}}\left[m_{s(x), t} \ell_{BCE}\left(y_{t}, p_{t}(x)\right)\right],\]
where \(\lambda_{t}\) is a non-negative weight for task 𝑡(set to 1 in our deployment) and \(f_{BCE}\) is the binary cross-entropy loss. In practice, the mask prevents ad-specific signals from directly shaping promotion towers, while still allowing promotion signals to aid ads, which is valuable given the relative data sparsity on some ad objectives.
[中文翻译]
这一设计实现了定向迁移：推广曝光样本同时更新推广塔和广告塔的参数，而广告曝光样本仅更新广告塔的参数。整体训练目标为：
\[\mathcal{L}=\sum_{t \in \mathcal{T}} \lambda_{t} \mathbb{E}_{\left(x, y_{t}\right) \sim \mathcal{D}}\left[m_{s(x), t} \ell_{BCE}\left(y_{t}, p_{t}(x)\right)\right],\]
其中\(\lambda_{t}\)为任务𝑡的非负权重（我们的部署中设为1），\(\ell_{BCE}\)为二元交叉熵损失。在实际应用中，该掩码可防止广告专用信号直接影响推广塔的参数学习，同时保留推广信号对广告任务的辅助作用——这对于部分广告目标存在数据相对稀缺的情况尤为重要。

[原文段落]
To ensure parity between channels, we use source-balanced sampling: each mini-batch is constructed so that roughly 50% of impressions come from \(D^{P}\) and 50% from \(D^{A}\) . This keeps gradients from promotions and ads at comparable scales and avoids the joint model collapsing toward the higher-volume source.
[中文翻译]
为确保两个渠道的平衡，我们采用来源平衡采样策略：每个迷你批次（mini-batch）中，约50%的曝光样本来自\(D^{P}\)，50%来自\(D^{A}\)。这一策略使推广和广告数据产生的梯度处于相近规模，避免联合模型向数据量更大的来源倾斜。

### 4 Experiments and Results
[原文段落]
We compare the joint model with the promotions-only and ads-only baselines from Section 3.1. We outline the setup, then present offline and online results and summarize ablations.
[中文翻译]
我们将联合模型与3.1节中的仅推广基准模型和仅广告基准模型进行对比。本节首先概述实验设置，然后呈现离线和在线实验结果，并总结消融分析结论。

#### 4.1 Experimental Setup
[原文段落]
Data and splits. We train on production logs from Spotify’s podcast ads and promotions systems over a multi-month period. Impressions are temporally split into training, validation, and test sets: earlier days for training, intermediate days for validation, and the most recent days for testing. Ads and promotions impressions are pooled but retain channel labels and task-specific outcomes.
[中文翻译]
数据与划分。我们使用Spotify播客广告和推广系统的数月生产日志进行训练。曝光数据按时间顺序划分为训练集、验证集和测试集：早期数据用于训练，中期数据用于验证，最新数据用于测试。广告和推广曝光数据被合并使用，但保留了渠道标签和任务特定结果标签。

[原文段落]
Evaluation metrics. Offline, we use Average Precision (AP), which summarizes the precision–recall curve and is more informative than AUC-ROC under heavy class imbalance. Online, we focus on:
Effective Cost-Per-Stream (eCPS): ad spend divided by resulting podcast streams;
• Stream rate (i2s): impression-to-stream rate;
• Click-through rate (CTR).
Metrics are reported for all podcasts and for less-streamed creators (shows with fewer than 5,000 streams), a segment strongly affected by cold-start.
[中文翻译]
评估指标。离线评估采用平均精度（AP），该指标可概括精确率-召回率曲线，在严重类别不平衡场景下比AUC-ROC更具信息量。在线评估重点关注以下指标：
• 有效每播放成本（eCPS）：广告支出除以产生的播客播放量；
• 播放率（i2s）：曝光-播放转化率；
• 点击率（CTR）：点击量与曝光量的比值。
评估结果分别针对所有播客和低播放量创作者（播放量少于5000次的节目）报告——后者是受冷启动问题影响最严重的群体。

[原文段落]
Training details. All models share the same optimizer (Adam) and learning-rate schedule. Hyperparameters are tuned using validation AP on stream tasks.
[中文翻译]
训练细节。所有模型使用相同的优化器（Adam）和学习率调度策略。超参数通过播放任务的验证集平均精度（AP）进行调优。

#### 4.2 Offline Evaluation Results
[原文段落]
Table 1 compares the multi-objective promo–ads model with the production baseline and alternative task groupings. The unified “Promo + Ads 5-task MTL” model provides the strongest performance.
[中文翻译]
表1对比了多目标推广-广告联合模型与生产环境基准模型及其他任务分组方案的性能。结果显示，统一的“推广+广告5任务多任务学习（MTL）”模型表现最优。

[原文表格标题]
Table 1: Average Precision (AP) comparison across configurations. Relative change to the baseline promotions model (Figure 2A).
[中文翻译]
表1：不同配置的平均精度（AP）对比。结果为相对于基准推广模型（图2A）的相对变化率。

[原文表格内容]
| Task Setup | Promotions AP | Ads AP |
| --- | --- | --- |
| Promo Stream head-only | − 7 . 9% | − 8 . 8% |
| Ads Stream head-only | − 65 . 2% | + 27 . 0% |
| Ads Stream + ANC heads | − 64 . 8% | + 46 . 5% |
| Promo + Ads 5-task MTL | + 4 . 5% | + 50 . 2% |
[中文翻译]
| 任务配置 | 推广任务平均精度（AP） | 广告任务平均精度（AP） |
| --- | --- | --- |
| 仅推广播放头 | −7.9% | −8.8% |
| 仅广告播放头 | −65.2% | +27.0% |
| 广告播放头+辅助任务头 | −64.8% | +46.5% |
| 推广+广告5任务多任务学习 | +4.5% | +50.2% |

[原文段落]
Relative to the promotions-only baseline, the joint model improves Promotions AP by +4.5% and Ads AP by +50.2%. Ads-only configurations, even with ancillary heads, remain much weaker on promotions and still fall short of the joint model on ads, indicating that cross-channel transfer between promotions and ads is critical.
[中文翻译]
相较于仅推广基准模型，联合模型的推广任务平均精度（AP）提升4.5%，广告任务平均精度（AP）提升50.2%。仅广告配置（即使包含辅助任务头）在推广任务上的表现仍显著较弱，且在广告任务上也不及联合模型，这表明广告与推广之间的跨渠道知识迁移至关重要。

#### 4.3 Effect of Ancillary Heads
[原文段落]
The joint model includes ancillary heads for clicks, likes, and follows (ANC). Table 1 shows that adding ANC heads to the ads-only model increases Ads AP from +27% to +46.5% relative to baseline, confirming that modeling intermediate engagement signals benefits stream prediction. However, this ads-only configuration severely degrades Promotions AP (around −65%), indicating that ancillary heads alone are insufficient without promotions data.
[中文翻译]
联合模型包含点击、点赞和关注等辅助任务头（ANC）。表1显示，在仅广告模型中添加辅助任务头后，广告任务平均精度（AP）相对于基准模型从+27%提升至+46.5%，这证实了建模中间参与信号对播放量预测的积极作用。然而，这种仅广告配置会导致推广任务平均精度（AP）大幅下降（约−65%），表明仅依靠辅助任务头而缺乏推广数据是不够的。

[原文段落]
In the unified MTL setting, ANC heads over both ads and promotions improve AP for both channels. Ancillary labels are most useful when combined with cross-channel training, allowing the shared encoder to learn richer user and content representations.
[中文翻译]
在统一的多任务学习（MTL）场景中，同时覆盖广告和推广的辅助任务头可提升两个渠道的平均精度（AP）。辅助标签在与跨渠道训练结合时发挥最大作用，使共享编码器能够学习到更丰富的用户和内容表示。

#### 4.4 Online A/B Test Results
[原文段落]
We ran a budget-split A/B test across 180+ markets, comparing the 5-task joint model with the baseline that uses the promotions model for ad cold-start (Figure 2A).
[中文翻译]
我们在180多个市场开展了预算拆分A/B测试，将5任务联合模型与使用推广模型进行广告冷启动的基准模型（图2A）进行对比。

[原文段落]
The joint model improves impression-to-stream rate, click-through rate, and cost-efficiency simultaneously. Gains are largest for less-streamed creators, with a 22% eCPS reduction and 27% more streams, proving this approach particularly effective for cold-start content.
[中文翻译]
联合模型同时提升了曝光-播放转化率、点击率和成本效益。低播放量创作者的收益最为显著：有效每播放成本（eCPS）降低22%，播放量增加27%，这证明该方法对冷启动内容尤为有效。

##### 4.4.1 Cold-Start Performance
[原文段落]
To better understand how the joint model behaves across podcasts of different popularity levels, we further segment results by Spotify’s stream tiers. Podcasts are grouped into eight tiers based on the number of listening hours (longer than 60 seconds) accumulated over a rolling 30-day window. For our purposes, Tiers 0–2 correspond to high-stream podcasts, while Tiers 3–5 capture low-stream shows, aligned with less-streamed creator segment.
[中文翻译]
为了更深入了解联合模型在不同热度播客中的表现，我们根据Spotify的播放量等级对结果进行进一步细分。播客根据过去30天的累计收听时长（超过60秒）分为8个等级。在本研究中，0-2级对应高播放量播客，3-5级对应低播放量节目，与低播放量创作者群体一致。

[原文段落]
When we re-evaluate the A/B test by tiers, we observe markedly large improvements for lower-streamed podcasts. For high-stream tiers, the relative improvement in i2s grows from approximately +7% (Tier 0) to +20% (Tier 2), while mean CPS decreases by 4–17%. In contrast, low-stream tiers see substantially larger effects: i2s improves by roughly +27% (Tier 3), +33% (Tier 4), and up to +60% for Tier 5, with corresponding CPS reductions of about 20%, 24%, and 38%, respectively. This monotonic pattern-larger relative gains as we move from Tier 0 to Tier 5-provides strong evidence that the unified model is particularly effective in cold-start and low-stream regimes, where data is sparse and traditional siloed models struggle.
[中文翻译]
按等级重新评估A/B测试结果后，我们发现低播放量播客的提升效果尤为显著。对于高播放量等级，曝光-播放转化率（i2s）的相对提升从约+7%（0级）增长至+20%（2级），平均每播放成本（CPS）降低4%-17%。相比之下，低播放量等级的提升效果更为突出：3级的曝光-播放转化率（i2s）提升约+27%，4级提升+33%，5级最高提升+60%；对应的平均每播放成本（CPS）分别降低约20%、24%和38%。这种从0级到5级相对收益逐步增大的单调模式，有力地证明了统一模型在冷启动和低播放量场景下的有效性——这些场景中数据稀缺，传统的独立模型往往表现不佳。

### 5 Conclusion
[原文段落]
This paper presents the successful development and deployment of a unified multi-task model for podcast ad and promotion targeting at Spotify. Our joint optimization approach markedly improves upon traditional siloed models by effectively leveraging transfer learning; pre-training on extensive advertising data enables strong performance across diverse tasks, including promotions, particularly in cold-start scenarios.
[中文翻译]
本文介绍了Spotify成功开发并部署的播客广告与推广定向统一多任务模型。我们的联合优化方法通过有效利用迁移学习，显著优于传统的独立模型；基于大规模广告数据的预训练使模型在包括推广在内的多种任务中均表现出色，尤其在冷启动场景下效果显著。

[原文段落]
Key lessons from this initiative highlight the power of unifying disparate yet related recommendation tasks, which not only unlocks significant performance gains but also fosters crucial organizational synergies, such as improved cross-team collaboration and strategic alignment by breaking down previously siloed efforts. Furthermore, leveraging transfer learning within such a joint model effectively mitigates cold-start issues for new content and objectives. The model’s capacity to simultaneously enhance diverse business objectives-spanning ad streams, ad clicks, and promotional streams-with substantial gains suggests operation nearer to a Pareto optimal frontier [11]. While our study focuses on podcasts, the approach naturally extends to other verticals (e.g., music, audiobooks, video) where ads and organic promotions share user and content representations.
[中文翻译]
该项目带来的关键经验表明，整合不同但相关的推荐任务具有巨大价值——这不仅能带来显著的性能提升，还能促进重要的组织协同效应，例如通过打破以往独立的工作模式，改善跨团队协作和战略一致性。此外，在这种联合模型中利用迁移学习，可有效缓解新内容和新目标的冷启动问题。该模型能够同时提升包括广告播放量、广告点击量和推广播放量在内的多种业务目标，且均取得了显著收益，这表明其运行状态更接近帕累托最优前沿[11]。尽管本研究聚焦于播客领域，但该方法可自然扩展到其他领域（如音乐、有声书、视频）——这些领域中，广告和自然推广共享用户和内容表示，具备联合建模的基础。

[原文表格标题]
Table 2: A/B test results for all and less-streamed podcast creators \((p-value <0.05)\) ). Less-streamed podcasts have fewer than 5,000 streams. Relative change to the baseline (Figure 2A).
[中文翻译]
表2：所有播客与低播放量播客创作者的A/B测试结果（\(p值<0.05\)）。低播放量播客指播放量少于5000次的节目。结果为相对于基准模型（图2A）的相对变化率。

[原文表格内容]

| Segment | i2s | eCPS | CTR | # streams |
| --- | --- | --- | --- | --- |
| Less-streamed creators | +24% | −22% | +10% | +27% |
| All podcasts | +18% | −20% | +9% | +18% |

[中文翻译]

| 群体 | 曝光-播放转化率（i2s） | 有效每播放成本（eCPS） | 点击率（CTR） | 播放量（# streams） |
| --- | --- | --- | --- | --- |
| 低播放量创作者 | +24% | −22% | +10% | +27% |
| 所有播客 | +18% | −20% | +9% | +18% |