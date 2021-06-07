---
layout: post
title: Missing data mean imputation
date: 2019-01-14 15:32:24.000000000 +09:00
author: Shan J.
tags:
    - Statistics
---

## Missing-data Techniques | 缺失值处理，可以用均值填补么？

### 1. 背景:	Overview

> 看到社科院社会学系博士招生题里面的一道问题下图简答题第三问，我觉得虽然看起来很基础，但是细细思考，可以说是考察了统计的一些核心概念，实际操作性很强，因此我花了一些时间系统整理了缺失值处理的主流方法，最后用了断断续续将近一周完成这篇文章，希望能够有所启发。

![社科院phd](/assets/images/screenshot.jpg)

Missing data arise in almost all serious statistical analyses. We discuss a variety of methods to handle missing data, including some relatively simple approaches that can often yield reasonable results. We use as a running example the Social Indicators Survey[^1], a telephone survey of New York City families conducted every two years by the Columbia University School of Social Work. Nonresponse in this survey is a distraction to our main goal of studying trends in attitudes and economic conditions, and we would like to simply clean the dataset so it could be analyzed as if there were no missingness.

缺失值处理在统计分析中十分重要，在这篇文章中, 几种常见的缺失值处理办法将会被清晰展示，我们在本文中将会使用社会指标调查，这项调查是由哥大社会工作学院所进行的一项针对纽约市居民家庭的电话调查，每两年举行一次，调查中的无应答数据将会成为我们研究经济地位和行为态度的阻碍，因此我们需要进行合理的数据清理。

[^1]: 压缩文件夹解压后请选择 [SIS](http://www.stat.columbia.edu/~gelman/arm/examples/ARM_Data.zip)

#### *Missing data in R*

In R, missing values are indicated by NA’s. For example, to see some of the data from five respondents in the data file for the Social Indicators Survey (arbitrarily picking rows 91–95), we type

在R中，缺失数据是以NA形式储存的，注意R里面还有**NaN** ，代表的含义是Not a Number，即不是数字形式。我们从数据中取出前10行，观察数据的缺失值情况：

`head(sis_df,10)`

![R output](/Users/shan/Desktop/Screen Shot 2019-01-05 at 17.54.40.png)

数据中出现了大量的**NA**，需要我们根据实际含义来进行选择和插值。

In classical regression (as well as most other models), R automatically excludesall cases in which any of the inputs are missing; this can limit the amount of information available in the analysis, especially if the model includes many inputs with potential missingness. This approach is called a **complete-case analysis**, and
we discuss some of its weaknesses below.

通常情况下，在传统回归分析以及很多其他的模型中，R会自动排除那些含有缺失值的案例，我们整体的信息利用效率会大打折扣，特别是如果在包含很多变量但缺失严重的数据中，会造成非常大的information bias，这样的分析方法被称为完整案例分析，它的缺点显而易见。

Things become more difficult when predictors have missing values. For example, if we wanted to model attitudes toward the police, given earnings and demographic predictors, then the model would not automatically account for the missing values of earnings. We would have to remove the missing values, impute them, or model them.

事实上，在因变量中包含缺失值的情况下，处理就变得更加棘手了。举例而言，如果我们通过观察人们对于警察的态度，来研究和人们收入以及其它一些人口学特征的关系，建模过程自动就会排除那些缺失了收入情况的案例。我们必须要想办法：移除缺失值，或者插值，或是寻找合适的模型。

### 2. 缺失值分类：Missing-data mechanisms

To decide how to handle missing data, it is helpful to know why they are missing. We consider four general “missingness mechanisms,” moving from the simplest to the most general.

首先我们需要了解为什么数据里面会产生缺失值，系统性的还是随机的？

#### 1	完全随机的缺失值：Completely at random

Missingness completely at random. A variable is missing completely at random if the probability of missingness is the same for all units, for example, if each survey respondent decides whether to answer the “earnings” question by rolling a die and refusing to answer if a “6” shows up. If data are missing completely at random, then throwing out cases with missing data does not bias your inferences.

完全随机的缺失值。当一个变量的所有抽样单位缺失概率相同时，即为完全随机缺失。比如说：如果每个被访者都采取掷骰子的方式来决定是否回答关于收入的问题，如果这枚骰子朝上的数字为6，概率为$\frac{1}{6}$, 那么如果收集过程中缺失产生是完全随机的，我们就可以直接采用完整案例分析，这样做不会产生偏倚，从而我们可以进行统计推断。

#### 2	部分随机的缺失值：Missingness at random

Missingness at random. Most missingness is not completely at random, as can be seen from the data themselves. For example, the different nonresponse rates for whites and blacks indicate that the “earnings” question in the Social Indicators Survey is not missing completely at random.

部分随机的缺失值。大多数的缺失值的产生不是完全随机的，我们可以从数据本身的观察看出来。举例而言，SIS数据中黑人和白人两个种族的不同的非应答率，就是系统性的差异，整体而言缺失值的产生：90%归功于系统性差异，10%归因于随机误。

A more general assumption, missing at random, is that the probability a variable is missing depends only on available information. Thus, if sex, race, education, and age are recorded for all the people in the survey, then “earnings” is missing at random if the probability of nonresponse to this question depends only on these other, fully recorded variables. It is often reasonable to model this process as a logistic regression, where the outcome variable equals 1 for observed cases and 0 for missing.

一个更加普遍的假设，随机缺失，就是说一个变量只和手头现有信息中的这些因素相关. 因此，如果在调查中的性别，种族，教育和年龄都被完整地记录下来了，那么如果**收入**只与以上这些完整记录的因素相关，那么这道问题非应答率的缺失是随机的。这个过程中我们经常采用的是**逻辑回归**(Logistic Regression),  因为我们将会采用0-1编码的方式，1 = 观察值存在，0 = 观察值缺失。

When an outcome variable is missing at random, it is acceptable to exclude the missing cases (that is, to treat them as NA’s), as long as the regression controls for all the variables that affect the probability of missingness. Thus, any model for earnings would have to include predictors for ethnicity, to avoid nonresponse bias.

当因变量中的数据随机缺失，只要回归建模中，我们对于影响因变量缺失的自变量加以控制，我们可以直接排除那些缺失的记录(one row/entry in data)，直接将他们当作缺失值处理。 因此，任意收入模型都需要纳入**种族**这一变量，避免非应答偏倚。

This missing-at-random assumption (a more formal version of which is some- times called the ignorability assumption) in the missing-data framework is the basically same sort of assumption as ignorability in the causal framework. Both require that sufficient information has been collected that we can “ignore” the assignment mechanism (assignment to treatment, assignment to nonresponse).

这种随机缺失的假设(更加正式的说法可能是：“可忽视”假设)在缺失数据分析框架中，基本上与因果推断中的“可忽视”假设一致。两种假设都要求数据的完整性，才能让我们能够“忽视”随机分配机制的影响(随机分配到实验组，随机分配到非应答)

#### 3 	受未观测变量影响的缺失值：Missingness depending on unobserved predictors

Missingness that depends on unobserved predictors. Missingness is no longer “at random” if it depends on information that has not been recorded and this information also predicts the missing values. For example, suppose that “surly” people are less likely to respond to the earnings question, surliness is predictive of earnings, and “surliness” is unobserved. Or, suppose that people with college degrees are less likely to reveal their earnings, having a college degree is predictive of earnings, and there is also some nonresponse to the education question. Then, once again, earnings are not missing at random.

缺失值受未被观测到的自变量影响。如果缺失受那些未被观测或记录下的潜在因素的影响，那些未被观测或记录的数据便会产生缺失值，**缺失**便不再是随机的。举例而言，假设说一部分“**坏脾气**”的人回答收入问题的可能性更低，那么坏脾气就可以作为影响收入的一项预测指标，但实际上**坏脾气**这个变量却并没有被收集到数据集中。或者说，换个例子，有**大学学位**的人，向访问员披露收入的可能性更低，大学学位也成为影响收入预测准确性的因素，但是在数据中有不少人没有回答教育程度这一变量的相关问题。于是，我们可以看到在这个例子中，收入变量也**不**再是随机缺失的了。

A familiar example from medical studies is that if a particular treatment causes discomfort, a patient is more likely to drop out of the study. This missingness is not at random (unless “discomfort” is measured and observed for all patients). If missingness is not at random, it must be explicitly modeled, or else you must accept some bias in your inferences.

一个大家比较熟悉的例子来自医学研究。如果某一项治疗导致了不适，病人可能会更可能退出研究，这种缺失并不是完全随机的(除非我们可以收集所有病人的舒适程度这一变量的数据)，如果缺失并不是随机出现的，建模过程中我们必须清楚地将这一影响展示出来，要不然我们只能坐等推断中的偏倚出现了。

#### 4 	受自身影响的缺失值：Missingness depending on the missing value itself

Missingness that depends on the missing value itself. Finally, a particularly difficult situation arises when the probability of missingness depends on the (potentially missing) variable itself. For example, suppose that people with higher earnings are less likely to reveal them. In the extreme case (for example, all per- sons earning more than $100,000 refuse to respond), this is called **censoring**, but even the probabilistic case causes difficulty.

最后一部分，我们将谈到受自身缺失影响的缺失值。当某一变量缺失(可能缺失)的概率依赖于其自身，就会出现这种比较难处理的情况。举例而言，假设**高收入**的人群更倾向于不向采访者披露他们的收入。在极端情况下(比方说：所有收入超过10万美元的都拒绝回答)，这就叫做**删失**，即便不是一定发生，是有可能发生，都会造成极大的困难。

Censoring and related missing-data mechanisms can be modeled or else mitigated by including more predictors in the missing-data model and thus bringing it closer to missing at random. For example, whites
and persons with college degrees tend to have higher-than-average incomes, so controlling for these predictors will somewhat—but probably only somewhat—correct for the higher rate of nonresponse among higher-income people. More generally, while it can be possible to predict missing values based on the other variables in your dataset, just as with other missing-data mechanisms, this situation can be more complicated in that the nature of the missing-data mechanism may force these predictive models to extrapolate beyond the range of the observed data.

删失和其它一些可能导致数据缺失的机制可以通过建模或其它一些办法(包括向缺失数据模型中增加自变量等办法) 来使得结果更加逼近随机缺失情况下的结果。举例而言，白人和拥有大学学位的人相对于其他人而言，收入有可能更高，因此控制这些变量影响将会修正-但只是有限度地修正-高收入人群中的高缺失率的影响。 更加普遍的情况是，虽然有可能通过数据中的其他变量来预测缺失值，但正如其他缺失值情形一样，这种情况可能更为复杂，因为缺失数据机制可能会迫使那些预测模型，做出超过观测值本身的推断。

####*General impossibility of proving that data are missing at random*

As discussed above, missingness at random is relatively easy to handle—simply include as regression inputs all variables that affect the probability of missingness. Unfortunately, we generally cannot be sure whether data really are missing at random, or whether the missingness depends on unobserved predictors or the missing data themselves. The fundamental difficulty is that these potential “lurking variables” [^2]are unobserved—by definition—and so we can never rule them out. We generally must make assumptions, or check with reference to other studies (for example, surveys in which extensive follow-ups are done in order to ascertain the earnings of nonrespondents).

正如之前提到的，随机缺失相对容易处理，只要在回归分析中将所有影响缺失的变量纳入回归模型中即可。不幸的是，一般而言，我们无法确定数据是完全随机缺失的，还是部分随机缺失的(受某些因素，如种族的系统性影响)，还是受到未观测变量影响的缺失，抑或是受自身影响的缺失。我们必须面对的一项基本的困难是我们并没有收集那些**潜变量**的相关数据，因此我们永远没有办法将它们排除在模型外。 通常，我们必须做出假设，或者查找相关文献(比方说，通过大量的追踪研究中的调查数据来确定影响那些收入缺失值的因素)。

In practice, we typically try to include as many predictors as possible in a model so that the “missing at random” assumption is reasonable. For example, it may be a strong assumption that nonresponse to the earnings question depends only on sex, race, and education—but this is a lot more plausible than assuming that the probability of nonresponse is constant, or that it depends only on one of these predictors.

实际生活中，我们经常会通过尽可能地增加自变量的方式来满足**随机缺失**这一假设。比方说，拒绝回答收入问题仅仅受性别，种族和教育的影响就很有可能是一个很强有力的假设，但是如果我们将拒绝回答收入问题看作一个**变量**而非**常量**，或者相对于只观察这三个变量中的其中某一个而言，我们的研究假设可能比他们的更合理。

[^2]: Lurking variable: if a variable is associated with X and has an effect on Y, but not included in the study, it will cause confounding and become a lurking variable.  
### 3.缺失值处理

### (1)直接剔除法：*Missing-data methods that discard data*

#### 1.完整案例分析：*Complete-case analysis*

A direct approach to missing data is to exclude them. In the regression context, this usually means complete-case analysis: excluding all units for which the outcome or any of the inputs are missing. In R, this is done automatically for classical regressions (data points with any missingness in the predictors or outcome are ignored by the regression).

一种直接的方法就是直接将它们从数据集中拿掉。在回归分析中，最常见的就是**完整案例分析**：排除所有自变量/因变量含有缺失值的记录。在R中，这一步骤是自动完成的，任意含有缺失值的数据点，无论是自变量还是因变量都会被自动排除。

Two problems arise with complete-case analysis:

**完整案例分析**会造成的两类问题：

##### 	问题一：Probelm 1.  

If the units with missing values differ systematically from the completely observed cases, this could bias the complete-case analysis.

如果含有缺失值的数据和那些排除缺失值的完整数据存在系统性的差异(如种族)，那么就会造成偏倚。

##### 	问题二：Probelm 2.  

If many variables are included in a model, there may be very few complete cases, so that most of the data would be discarded for the sake of a simple analysis.

如果模型中包含很多变量，那么数据中全部完整的案例就会非常少，因此为了实现回归分析，大多数的数据可能就会在处理过程中被自动丢弃。

#### 2.现存案例分析：*Available-case analysis*

Another simple approach is available-case analysis, where different aspects of a problem are studied with different subsets of the data. For example, in the 2001 Social Indicators Survey, all 1501 respondents stated their education level, but 16% refused to state their earnings. We could thus summarize the distribution of education levels of New Yorkers using all the responses and the distribution of earnings using the 84% of respondents who answered that question. This approach has the problem that different analyses will be based on different subsets of the data and thus will not necessarily be consistent with each other.

In addition, as with complete-case analysis, if the nonrespondents differ systematically from the respondents, this will bias the available-case summaries. For example in the Social Indicators Survey, 90% of African Americans but only 81% of whites report their earnings, so the “earnings” summary represents a different population than the “education” summary.

另一种简单办法就是**现存案例分析**，通过利用数据的不同子集来研究不同的问题，各个击破。在2001年的社会指标调查(SIS)中，总共1501名被访者告知了他们的受教育程度，但是仍有16%拒绝回答他们的收入情况。因此我们在分析时，可分析样本的受教育程度的分布情况和收入情况，但就收入情况而言，我们只能使用84%的样本数据。

This approach has the problem that different analyses will be based on different subsets of the data and thus will not necessarily be consistent with each other. In addition, as with complete-case analysis, if the nonrespondents differ systematically from the respondents, this will bias the available-case summaries. For example in the Social Indicators Survey, 90% of African Americans but only 81% of whites report their earnings, so the “earnings” summary represents a different population than the “education” summary.

这一方法的问题在于：操作上我们可以使用数据的不同子集进行不同问题的分析，但最终得到的结果却不一定彼此一致。此外，与完整案例分析相似，如果拒绝回答者和回答者存在系统性的差异，那么统计分析将会产生偏倚。比方说在 社会指标调查(SIS)中，**90%**的非裔美国人和**81%**的白人报告了他们的收入情况，所以在研究收入情况采用的样本和我们的研究总体并不一致。

Available-case analysis also arises when a researcher simply excludes a variable or set of variables from the analysis because of their missing-data rates (sometimes called “complete-variables analyses”). In a causal inference context (as with many prediction contexts), this may lead to omission of a variable that is necessary to satisfy the assumptions necessary for desired (causal) interpretations.

当一位研究者通过分析，简单地通过缺失率的高低来排除一个或者多个变量时(这种方式有时也被叫做**完整变量**分析)，采用现存案例分析比较合适。在因果推断(和一些预测)中，这样的处理办法可能会造成：遗漏某些对于满足整体因果关系分析假设十分必要的变量，没有这些变量我们无法做出预期的因果解释。

#### 3. 赋予缺失值样本权重：*Nonresponse weighting*

As discussed previously, complete-case analysis can yield biased estimates because the sample of observations that have no missing data might not be representative of the full sample. Is there a way of reweighting this sample so that representativeness is restored?

正如之前讨论过的，由于排除了缺失值的样本观测值无法代表我们的研究样本，完整案例分析会导致有偏估计。有什么办法可以**重新赋予样本权重**，以增强样本的代表性么？

Suppose, for instance, that only one variable has missing data. We could build a model to predict the nonresponse in that variable using all the other variables. The inverse of predicted probabilities of response from this model could then be used as survey weights to make the complete-case sample representative (along the dimensions measured by the other predictors) of the full sample. This method becomes more complicated when there is more than one variable with missing data. Moreover, as with any weighting scheme, there is the potential that standard errors will become erratic if predicted probabilities are close to 0 or 1.

假设，在数据集中只有一个变量含有缺失值。我们可以利用其他剩余的所有变量来建模估计这个含有缺失值的变量的非应答。从这一模型中估计出的**概率的倒数**之后就可以被用作调整调查的权重，使得我们最终从完整案例分析得到的样本分析更加具有代表性。当数据中存在不止一个变量含有缺失值时，这种方法变得更加复杂。此外，和很多赋权重的方法类似，如果预测的概率接近0或者1，标准误可能会变得很不稳定。

### (2) 单一插补法：*Simple missing-data approaches that retain all the data*

Rather than removing variables or observations with missing data, another approach is to fill in or “impute” missing values. A variety of imputation approaches can be used that range from extremely simple to rather complex. These methods keep the full sample size, which can be advantageous for bias and precision; however, they can yield different kinds of bias, as detailed in this section.

除了直接剔除那些含有缺失值的变量或是观测值的办法，另一种办法就是**填补**或是**插补**缺失值。在统计学中，一系列的插补方法，简单地，到相当复杂的都存在。这些方法会保留完整的样本容量，使得偏误缩小，精确性提高。然而，这些却会产生很多不同种类的偏误，以下会详细谈到。

Whenever a single imputation strategy is used, the standard errors of estimates tend to be too low. The intuition here is that we have substantial uncertainty about the missing values, but by choosing a single imputation we in essence pretend that we know the true value with certainty.

当某一种特定的插值方法被用到的时候，估计值的标准误会变得异乎寻常地**低**。我们可以这样理解：当我们对于缺失值有非常大的不确定性的时候，通过选择某一种单一的插值方法，我们实际上装作我们肯定地知道真实值。

####  1. 均值插补法：<u>*Mean imputation*</u>

Perhaps the easiest way to impute is to replace each missing value with the mean of the observed values for that variable. Unfortunately, this strategy can severely distort the distribution for this variable, leading to complications with summary measures including, notably, underestimates of the standard deviation. Moreover, mean imputation distorts relationships between variables by “pulling” estimates of the correlation toward zero.

均值插补法。可能最简单的办法就是用某一个变量的均值去插补缺失值。不巧的是，这种方法可能会严重地扭曲变量的分布情况，导致一系列“并发症”：此外，均值插补改变了变量之间的相关关系，导致线性相关的估计值(r)趋近于0.

It has the benefit of not changing the sample mean for that variable. However, mean imputation attenuates any correlations involving the variable(s) that are imputed. This is because, in cases with imputation, there is guaranteed to be no relationship between the imputed variable and any other measured variables. Thus, mean imputation has some attractive properties for univariate analysis but becomes problematic for multivariate analysis.

均值插补法的优点是，能够保证在插值之后，某一特定的变量的均值保持不变。因此，在统计分析中，均值插值比较适合**单变量分析**，但是对于在**多元分析**中，我们无法知晓是否被插值的变量与其他变量之间相关，均值插值会导致变量间的关系被低估。

#### 2. 末次观测值转结法：<u>*Last value carried forward*</u>

In evaluations of interventions where pre-treatment measures of the outcome variable are also recorded, a strategy that is sometimes used is to replace missing outcome values with the pre-treatment measure. This is often thought to be a conservative approach (that is, one that would lead to underestimates of the true treatment effect).

末次观测值转结法(LOCF)。这是临床试验中最常用的一种缺失数据的处理方法。它是利用对研究对象前进行干预前的最后一次的观测值来进行填补缺失值。这种方法经常被视作非常保守的一种方法(就是说，可能会导致对于真实干预影响的低估)。

However, there are situations in which this strategy can be anticonservative. For instance, consider a randomized evaluation of an intervention that targets couples at high risk of HIV infection. From the **regression-to-the-mean** phenomenon, we might expect a reduction in risky behavior even in the absence of the randomized experiment; therefore, carrying the last value forward will result in values that look worse than they truly are. Differential rates of missing data across the treatment and control groups will result in biased treatment effect estimates that are anticonservative.

然而，在某些情况下，这种方法可能并不是保守的。举例而言，当我们评价某一个HIV病毒感染风险的干预措施时，由于存在「回归到平均值」的现象的存在，我们可能会预期即便没有干预措施的存在，高风险行为也会降低。因此，末次观测值转结法会导致插补的值比真实值表现更差。在干预过程中，控制组和对照组二者数据中缺失比例的差别，将会产生有偏误的，并非保守的治疗效应估计值。

#### 3. 相似观察值插补法：<u>*Using information from related observations*</u>

Suppose we are missing data regarding the income of fathers of children in a dataset. Why not fill these values in with mother’s report of the values? This is a plausible strategy, although these imputations may propagate measurement error. Also we must consider whether there is any incentive for the reporting person to misrepresent the measurement for the person about whom he or she is providing information.

假设我们在数据中缺失了家庭中孩子的父亲的收入数据。为什么我们不可以用母亲的收入数据来填补呢？这是一个看上去比较合理的办法，虽然这些插补的值可能会增大观测误差。同样地，我们必须考虑是否有可能存在这样的情况：我们询问的被访者有着某种动机或是激励，促使他提供相关人的信息(可能会导致信息的歪曲)。

<u>Indicator variables for missingness of categorical predictors.</u> For unordered categorical predictors, a simple and often useful approach to imputation is to **add an extra category** for the variable indicating missingness.

在**分类变量**缺失情况下的**指示变量**。对于无序的分类变量，一种简单的，但是非常有效的方法就是利用插补去额外增加一个分类表示缺失。

<u>Indicator variables for missingness of continuous predictors.</u> A popular approach in the social sciences is to include for each continuous predictor variable with missingness an extra indicator identifying which observations on that variable have missing data. Then the missing values in the partially observed predictor are re- placed by zeroes or by the mean (this choice is essentially irrelevant). This strategy is prone to yield biased coefficient estimates for the other predictors included in the model because it forces the slope to be the same across both missing-data groups. Adding interactions between an indicator for response and these predictors can help to alleviate this bias (this leads to estimates similar to complete-case estimates).

在**连续变量**缺失情况下的指示变量。社会科学中，一种非常主流的办法就是通过。

增加交互项，

这种办法很有可能产生有偏的系数估计值，因为其他的因变量

Imputation based on logical rules. Sometimes we can impute using logical rules: for example, the Social Indicators Survey includes a question on “number of months worked in the previous year,” which all 1501 respondents answered. Of the persons who refused to answer the earnings question, 10 reported working zero months during the previous year, and thus we could impute zero earnings to them. This type of imputation strategy does not rely on particularly strong assumptions since, in effect, the missing-data mechanism is known.

按照逻辑来插补。有时我们需要利用逻辑来插值：比如说，社会指标调查中有一个问题“在过去的一年中，你工作了几个月？”，所有的被访谈者都有回答。在那些拒绝回答关于收入问题的被访者中，十个人在过去一年中工作的时间为0个月，意味着过去一年他们没有任何工作，因此我们可以为这10个人的收入插值0。这种插值的办法并不依赖于特定的假设，因为实际上我们知道缺失是怎么发生的。

### Random imputation of a single variable

When more than a trivial fraction of data are missing, however, we prefer to perform imputations more formally. In order to understand missing-data imputation, we start with the relatively simple setting in which missingness is confined to a single variable, y, with a set of variables X that are observed on all units. We shall consider the case of imputing missing earnings in the Social Indicators Survey.

然而，当数据集中的缺失值相对较多的时候，我们更倾向于使用正式的插值方法。为了理解插值方法，我们先通过一个简单的例子：仅在一个变量y中存在缺失值，x的所有变量都是完整的。我们希望研究社会指标调查的收入缺失值，对它插值。

#### R的实现：R code

The simplest approach is to impute missing values of earnings based on the observed data for this variable. 最简单的办法就是基于变量中已有数据插值。

```{r}
# function of random imputation
random.imp <- function (a){
	missing <- is.na(a)
	n.missing <- sum(missing)
	a.obs <- a[!missing]
	imputed <- a
	imputed[missing] <- sample (a.obs, n.missing, replace=TRUE)

    return (imputed)
}

# create a completed data vector of earnings
earnings.imp <- random.imp (earnings)

```

Zero coding and topcoding

```{r}
topcode <- function (a, top)
{ return (ifelse (a>top, top, a))
}
earnings.top <- topcode (earnings, 100) hist (earnings.top[earnings>0])
```

### Model-based imputation

where $y_i$ is the observed waiting time for case i, $z_i $ is the ultimate waiting time, and $t_i$ is the year of sentencing. We shall not analyze these data further here; we have introduced this example just to illustrate the complexities that arise in realistic censoring situations. The actual analysis for this problem is more complicated because death sentences have three stages of review, and cases can be waiting at any of these stages.

$y_i$：观察到的第i个样本的等候时间；$z_i$: 最终的等待时间；$t_i$: 死刑年份。因为死刑的判决需要经历三读，在三个阶段中都可能经历等待。在此我们不对数据做解读，

*Imputation in multilevel data structures*

Imputing becomes more complicated with clustered data. Suppose, for instance, that we have individual-level observations on children grouped within schools (for instance, test scores and demographics), and then measurements pertaining to the schools themselves (for instance, school policies and characteristics such as public versus private). We would not want to impute on a standard individual-level dataset where the school-level measurements are just repeated over each individual in the same school because, if a given school measurement is missing, such an approach would not be likely to impute the same value of this variable for each member of the group (as it should).

插值在聚类数据中非常复杂。比如说，我们手头上有个人层面的分组观测值，按照分数和人口地理学特征分类的学校中的孩子的状况。这些测量指标与学校本身有非常大的相关性(因为不同的学校政策和特征差别很大：公立学校和私立学校)。我们不太想

如果某一个给定的学校的测量值缺失了，那么这种办法就不太可能使我们针对群组中的每一个个人插同样的值。

Our general advice in this situation is to create two datasets, one with only individual-level data, and one with group-level data and do separate imputations within each dataset while using results from one in the other (perhaps iterating back and forth). For instance, one could first impute individual-level variables using individual-level data and observed group-level measurement. Then in the group-level dataset one could include aggregated forms of the individual-level measurements when imputing missingness at this level.

一般而言，在这种情况下，我们的建议是将数据分为两部分，一部分是个人层面的数据，另一部分是群组层面的数据，分别对两个数据集插值，并使用一个数据集中的**来回迭代**。比方说，我们可以首先利用个人层面的数据和观测到的群组层面的测量值，插入个人层面的变量。然后在群组层面的数据集中，我们可以插入个人层面的加总数据。



#### Combining inferences from multiple imputations

Rather than replacing each missing value in a dataset with one randomly imputed value, it may make sense to replace each with several imputed values that reflect our uncertainty about our imputation model. For example, if we impute using a regression model we may want our imputations to reflect not only sampling vari- ability (as random imputation should) but also our uncertainty about the regression coefficients in the model. If these coefficients themselves are modeled, we can draw a new set of missing value imputations for each draw from the distribution of the coefficients.

Multiple imputation does this by creating several (say, five) imputed values for each missing value, each of which is predicted from a slightly different model and each of which also reflects sampling variability. How do we analyze these data? The simple idea is to use each set of imputed values to form (along with the observed data) a completed dataset. Within each completed dataset a standard analysis can be run. Then inferences can be combined across datasets.



**Adpated From**

1. *Data Analysis Using Regression and Multilevel/Hierarchical Models*
