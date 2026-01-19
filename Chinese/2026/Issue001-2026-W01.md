# 2026-01-01
https://papers.cool/arxiv/cs.IR?date=2026-01-01

## 1. [R1] MaRCA：大规模推荐系统中动态计算分配的多智能体强化学习

https://papers.cool/arxiv/2512.24325

Authors: Wan Jiang, Xinyi Zang, Yudong Zhao, Yusi Zou, Yunfei Lu, Junbo Tong, Yang Liu, Ming Li, Jiani Shi, Xin Yang

Summary: 现代推荐系统因模型复杂度和流量规模的增加而面临重大计算挑战，因此高效的计算分配对于最大化业务收入至关重要。现有方法通常简化多阶段计算资源分配，忽视阶段间依赖，从而限制了全局最优性。本文提出了MaRCA，一种用于大规模推荐系统中端到端计算资源分配的多智能体强化学习框架。MaRCA将推荐系统阶段建模为合作智能体，采用去中心化执行中心训练（CTDE）以优化计算资源限制下的收入。我们引入了AutoBucket测试平台，用于精确估算成本，以及基于模型预测控制（MPC）的收入-成本平衡器，用于主动预测流量负载并相应调整收入与成本权衡。自2024年11月MaRCA在一家领先的全球电子商务平台广告流程中端到端部署以来，日均持续处理数千亿次广告请求，利用现有计算资源实现了16.67%的收入增长。