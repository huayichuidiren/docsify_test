# SRM检验

<div style="background: #E8F5E9; padding: 12px; border-left: 4px solid #81C784; border-radius: 4px; margin: 10px 0;">
<strong>参考文章：</strong> Detecting and avoiding bucket imbalance in A/B tests
</div>

该篇文章给出了一个关于不平衡样本（？）的自动化检测方案。

把用户分为两组很简单：只需要把id映射为整数（比如hash后再取余数），再为每个桶分配不同的整数。但问题在于，绝大部分实验都是只在一个只有部分用户能看到的地方做改动。比如我们想看推特用户在ios端上编辑照片的体验，但事实是：不是所有推特用户都位于ios端，也不是所有ios推特用户都使用照片编辑功能。

在效果评估时，如果考虑全部用户而非只触发了策略的用户，将导致「稀释」。原因在于策略最多只能改变「**看到**策略改变」的用户（即「触发用户」）。理想情况下，我们应关注「触发用户」的指标变动情况。但实际这样会存在风险：**有条件地将用户纳入实验可能带来偏差**，触发时的微小差异会带来「样本不可比」的问题。

## 三种触发代码的对比

### 第一种

```javascript
// 将用户分桶并记录用户是否在实验中  
bucket = getExperimentAndLogBucket(user, experiment);  
if (user.country == "US" && bucket == "treatment") {  
    /** 执行实验处理逻辑 **/  
} else {  
    /** 执行正常逻辑 **/  
}
```

这种触发代码将导致「实验效果稀释」。注意到所有用户均被执行了一种逻辑，但t组只有US用户执行了实验逻辑，c组全部用户都执行了实验逻辑。

### 第二种

```javascript
// 将用户分桶，但不记录是否触发实验  
bucket = peekAtExperimentBucket(user, experiment);  
if (user.country == "US" && bucket == "treatment") {  
    recordExperimentTrigger(user, experiment, "treatment");  
    /** 执行实验处理逻辑 **/  
} else {  
    if (bucket == "control") {  
        recordExperimentTrigger(user, experiment, "control");  
    }  
    /** 执行正常逻辑 **/  
}
```

这种触发代码将导致「实验效果偏差」。注意到不是所有用户都被执行了一种逻辑：t组非US的用户没有被执行逻辑。这造成「样本不可比」的问题。

### 第三种

```javascript
// 将用户分桶，但不记录是否触发实验  
bucket = peekAtExperimentBucket(user, experiment);  
if (user.country == "US" && bucket == "treatment") {  
    recordExperimentTrigger(user, experiment, "treatment");  
    /** 执行实验处理逻辑 **/  
} else {  
    // 即使在对照组，也不记录非美国用户的触发情况  
    if (user.country == "US" && bucket == "control") {  
        recordExperimentTrigger(user, experiment, "control");  
    }  
    /** 执行正常逻辑 **/  
}
```

这种触发代码是正确的。我们注意到不是所有用户都被执行了一种逻辑，但被执行逻辑的用户均为US，样本可比。

## SRM检验

SRM检验又称「样本有效性检验」（Internal Validity Test），目的是检验各组「实际触发」的去重用户数是否与设定的分流比例一致。用数学符号描述就是：
$$
\chi^2=\sum_{i=1}^{K}\dfrac{(O_i-E_i)^2}{E_i}
$$
其中$O_i$是第$i$组实际观测到的（实际触发的）去重用户数，$E_i$是根据分流比例预计的去重用户数，他们的偏差平方$\sum_{i=1}^{K}\dfrac{(O_i-E_i)^2}{E_i}$服从一个卡方分布。

如果拒绝了原假设，说明实际触发的用户比例和设定的用户比例不一致，此时「样本不可比」，「策略效果」的估计是有偏的。

## 为什么要做SRM检验？

如果我们的策略对「用户留存」有影响，极有可能导致「策略效果的估计」有偏。此时我们不能看by天的指标数据，只有看用户从进组累积到当前的数据是科学的。但业务做实验时一般考虑不到这点，所以为了看清当前实验的问题，我们最好需要做SRM检验。