# 无效实验自动化诊断

How A/B Tests Could Go Wrong: Automatic Diagnosis of Invalid Online Experiments

- 不是所有同学都能正确设计有效的ab实验，并能够正确理解数量众多的指标
- 本研究的目的是自动检测和诊断无效实验

「实验无效」可以分为两种：「内部无效」和「外部无效」

- 「内部无效」（internal invalidity）指除干预外还有别的东西影响指标。一个常见的问题是样本不可比（incomparable samples），可能的原因比如「样本选择偏差」或「混淆变量」的影响
  - 「内部无效性」将带来实验结果的偏差
  - 在分析大量的「有偏实验」后，本研究总结分类出了多种「内部无效性」的类型

## 触发分析

什么是触发分析？仅针对真正接受到策略的用户（**users who could have experienced the change** introduced by the experiment）所做的分析。触发分析的优势在于提高实验的「灵敏度」（当然，这是相对于「大盘分析」all-up analysis而言）。

<div class="protected-content">

### 详细分析与实践指导

一个典型的错误说法："如果实施策略，指标会在下个季度「增长」$\Delta$%"

1. 这种「陈述」隐含地做了这样一个假设：基于一周数据所验证的影响会持续一个季度
2. 但干预效果可能是「时间依赖」的。即干预效果可能「时序相关」，可能随时间增强或衰减（比如广告刚开始会吸引用户，但长期效果会减弱）
3. 结论：短期实验效果无法直接外推至长期效果

关于"真正接受到策略的用户"，可以举一个例子：

<div style="background: #E8F5E9; padding: 12px; border-left: 4px solid #81C784; border-radius: 4px; margin: 10px 0;">
<strong>例：</strong> 一个网站有20w的DAU，但只有2w用户访问过求职页面。我们只在求职页面做实验。
</div>

那些没有接触到实验的用户实际上是"噪音"。

触发实验有多种类型，比如「session- triggering」和「user- triggering」

触发分析可以节约实验所需的样本量。为什么呢？因为触发分析中我们关注的「人群」变化了。原来我们关注「大盘」人群，比如「开端用户」（即app活跃用户）。但如果我们使用「触发分析」而把**关注的人群**缩小至「触发人群」上（真正接受到策略的用户），就可以节约样本量。

## 内部无效性

「在线ab实验」评估结果的「内部无效性」一般来自两个原因：不满足SUTVA假设和不满足unconfoundness假设。

「在线ab实验」和「传统随机实验」一个很大的不同是，后者的样本事先准备好，而前者的样本依赖于动态收集（用户**随时间推移**陆续触发实验，从而收集样本）。

> samples in online experiments are usually collected sequentially over time as users trigger into the experiment by visiting the affected part of the site.
>
> ——**How A/B Tests Could Go Wrong: Automatic Diagnosis of Invalid Online Experiments**

内部无效性通常由几种情况导致。

### 1、策略影响用户行为

什么是策略的残留效应？ab实验通常需要逐步扩大流量，多次迭代来找到最优策略。为了避免将已接触实验的用户切换回对照组，通常需要在同一策略的多次迭代中保持相同的用户分组。如果策略能改变用户「触发实验的概率」，那么实验组用户量变小，导致实验对照组「样本不可比」。（“策略影响用户留存”就是策略改变「用户触发实验概率」的一个体现）。

只要从实验一开始就统计独立用户数，就不会有SRM问题。然而，为了避免辛普森悖论（？），通常在逐步扩大实验流量时需要重新开始分析。在这种情况下，如果用户重新触发实验的比率发生变化（因为用户是从实验开始后的某个时间点才开始统计的），样本量就会与预期不符。具体来说有两个后果：

- SRM问题：早期入组的用户被遗漏
- 样本选择偏差：进入实验组的用户都是本来就是高活跃的

如果只有「策略影响留存」一个原因，残留效应很少会严重到SRM问题，所以我们需要思考是否在实验中会存在其他问题。当「策略影响留存」是唯一原因时，SRM问题的解决有两个方式：重随机和从实验一开始就统计用户数。（但我觉得「重随机」解决不了这个问题，因为我们认为「重随机」是“100个种子里选最优分流”的方法。他这里的意思可能是“打散”）

这告诉我们：**不从实验开始时统计用户数，就会有策略的残留效应，比如策略影响用户留存，从而导致SRM问题和样本选择偏差**。

<div style="background: #E8F5E9; padding: 12px; border-left: 4px solid #81C784; border-radius: 4px; margin: 10px 0;">
<strong>什么是SRM问题： </strong> 实验前预先设定分流比例r1,r2，在触发（ps：入组还是触发？）时t、c两组的实际样本量之比不是r1:r2。
</div>

### 2、触发时机错误

即使策略不影响用户行为比如留存，但实验的实施方式仍可能导致实验对照组用户的重新触发率不同。错误的触发时机会导致实验SRM问题。（没看懂）

### 3、人群条件依赖动态定向

「内部无效性」的另一个原因是「人群定向」（英文叫做targeting，也叫「人群命中」）

<div style="background: #E8F5E9; padding: 12px; border-left: 4px solid #81C784; border-radius: 4px; margin: 10px 0;">
<strong>人群定向： </strong> 就是指对人群的筛选。在「ab实验」的语境中，「人群定向」指根据人群的「属性」和「行为」去选择特定的用户集合做实验。用户的「属性」可以理解为「人群标签」。
  可以用于「人群定向」的属性包括：用户所属国家、行业、地区（这些是静态标签）；用户进组前的好友数量、求职状态、个人资料完整度（这些是动态标签）。
</div>

在ab实验中设置「人群定向」时，「静态人群标签」和「动态人群标签」有什么区别？

1. 如果使用静态标签作为实验「人群命中」的条件，不会有问题
2. 如果使用动态标签作为人群命中的条件，**那么策略可能影响命中实验的用户数**。这个影响链路是怎样的？策略影响用户行为 --> 用户行为影响动态标签的数值。
   - 例子：实验组用户曝光“你可能认识的人”的功能卡片，对照组不曝光。人群命中的条件是「好友数」<50。那么对于一个好友数=49的用户来说，刚开始会进入实验组，随后“你可能认识的人”这个策略增加了他的好友数量。当用户下次曝光该页面时，如果使用动态标签，他将进入对照组，造成「串组」问题。

</div>

<div class="login-notice" style="display: block; padding: 20px; background: #f8f8f8; border: 1px solid #e0e0e0; border-radius: 8px; margin: 20px 0; text-align: center; box-shadow: 0 2px 4px rgba(0,0,0,0.1); cursor: pointer;">
    <div style="font-size: 48px; margin-bottom: 10px;">🔒</div>
    <div style="font-size: 20px; color: #333; margin-bottom: 10px;">请登录</div>
</div>

