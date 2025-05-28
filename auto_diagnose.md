## 无效实验自动化诊断

How A/B Tests Could Go Wrong: Automatic Diagnosis of Invalid Online Experiments

- 不是所有同学都能正确设计有效的ab实验，并能够正确理解数量众多的指标
- 本研究的目的是自动检测和诊断无效实验

「实验无效」可以分为两种：「内部无效」和「外部无效」

- 「内部无效」（internal invalidity）指除干预外还有别的东西影响指标。一个常见的问题是样本不可比（incomparable samples），可能的原因比如「样本选择偏差」或「混淆变量」的影响
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

触发分析可以节约实验所需的样本量。为什么呢？因为触发分析中我们关注的「人群」变化了。原来我们关注「大盘」人群，比如「开端用户」（即app活跃用户）。但如果我们使用「触发分析」而把**关注的人群**缩小至「触发人群」上（真正接受到策略的用户），就可以节约样本量。



## 内部无效性

「在线ab实验」评估结果的「内部无效性」一般来自两个原因：不满足SUTVA假设和不满足unconfoundness假设。



「在线ab实验」和「传统随机实验」一个很大的不同是，后者的样本事先准备好，而前者的样本依赖于动态收集（用户**随时间推移**陆续触发实验，从而收集样本）。

> samples in online experiments are usually collected sequentially over time as users trigger into the experiment by visiting the affected part of the site.
>
> ——**How A/B Tests Could Go Wrong: Automatic Diagnosis of Invalid Online Experiments**

