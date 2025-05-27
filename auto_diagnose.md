## 无效实验自动化诊断

How A/B Tests Could Go Wrong: Automatic Diagnosis of Invalid Online Experiments

- 不是所有同学都能正确设计有效的ab实验，并能够正确理解数量众多的指标
- 本研究的目的是自动检测和诊断无效实验

「实验无效」可以分为两种：「内部无效」和「外部无效」

- 「内部无效」（internal invalidity）指样本不可比（incomparable samples），可能的原因比如「样本选择偏差」或「混淆变量」的影响
  - 「内部无效性」将带来实验结果的偏差
  - 在分析大量的「有偏实验」后，本研究总结分类出了多种「内部无效性」的类型
- 「外部无效」



一个典型的错误说法：“如果实施策略，指标会在下个季度「增长」$\Delta$%”

1. 这种「陈述」隐含地做了这样一个假设：基于一周数据所验证的影响会持续一个季度
2. 但干预效果可能是「时间依赖」的。即干预效果可能「时序相关」，可能随时间增强或衰减（比如广告刚开始会吸引用户，但长期效果会减弱）
3. 结论：短期实验效果无法直接外推至长期效果

## 触发分析

什么是触发分析？仅针对真正接受到策略的用户（**users who could have experienced the change** introduced by the experiment）所做的分析。触发分析的优势在于提高实验的「灵敏度」（当然，这是相对于「大盘分析」all-up analysis而言）。关于“真正接受到策略的用户”，可以举一个例子

<div style="background: #E8F5E9; padding: 12px; border-left: 4px solid #81C784; border-radius: 4px; margin: 10px 0;">
<strong>例：</strong> 一个网站有20w的DAU，但只有2w用户访问过求职页面。我们只在求职页面做实验。
</div>

那些没有接触到实验的用户实际上是“噪音”。

触发实验有多种类型，比如「session- triggering」和「user- triggering」
