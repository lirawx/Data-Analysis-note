# 分类



常见分类器

* 决策树
* 朴素贝叶斯
* 罗辑回归作为通用分类器
* 支持向量机用作分类引擎
* 随机森林预测订阅者
* 神经网络分类

### 分类



分类器的主要作用是为新的观测数据分配类别标签



### 选择合适的分类器



| 问题                                               | 参考方法           |
| -------------------------------------------------- | ------------------ |
| 除了分类标签外，分类的输出还应该包括分类的概率     | 逻辑回归、决策树   |
| 分析师们想了解变量是如何影响模型的                 | 逻辑回归、决策树   |
| 问题拥有很高维度                                   | 朴素贝叶斯分类器   |
| 一些输入变量可能是关联的                           | 逻辑回归、决策树   |
| 一些输入变量可能是不相关的                         | 决策树、朴素贝叶斯 |
| 数据包含的分类变量具有大量的层级                   | 决策树、朴素贝叶斯 |
| 数据b傲寒混合变量类型                              | 逻辑回归、决策树   |
| 输入比昂了中含有回影响输出的非线性数据或非连续数据 | 决策树             |





#### 决策树



决策树也称预测树

熵: 用来衡量属性的杂质

信息增益: 用来衡量属性的纯净度

$H_x$ 熵计算:
$$
H_x = -\sum_{\forall{x}\in{X}}P(x)log_2P(x)
$$
算法

* ID3
* C4.5
* CART

CART 使用基尼多样化指数
$$
Gini_x = 1 -\sum_{\forall{x}\in{X}}P(x)^2
$$

### 朴素贝叶斯

贝叶斯定理
$$
P(C|A) = \frac{P(A\bigcap{C})}{P(A)}
$$


也就是 


$$
P(C|A) = \frac{P(A|C)*P(C)}{P(A)}
$$
朴素贝叶斯分类器


$$
P(a_1,a_2,...,a_m|c_i) = P(a_1|c_1)P(a_2|c2)...P(a_m|c_i) = \prod_{j=1}^mP(aj|ci)
$$
简化之后

 
$$
P(ci|A)\propto{P(ci)\cdot{\prod_{j=1}^mP(aj|ci)}}
$$
 n = 1,2,3...n





平滑

拉普拉斯平滑,解决一个属性值没有在训练集中的一个类标签出现


$$
P**(x) = \frac{count(x)+\varepsilon}{\sum_x[count(x)+\varepsilon]}
$$






```R
# 集成

# 增强预测性能

# 通过构造一系列分类器，通常时小型决策树，然后为预测进行加权投票，以便对新的数据点分类

# 从本质上来讲，集成是一种监督学习工具， 将很多弱分类器组合在一起，力图生成一个强分类器
# 两种基本方法

# 1. 套袋法(bagging)
# 集成的每个模型投票权重都相同
# 例子 ，随机森林算法

# 2. 提升法 (boosting)
# 着重训练之前模型中误分类的训练实例
# 上一个模型计算出错误分类，下一个模型中重新分配权重

# 3. 交叉融合法
# 当复合模型更少更复杂时，比提升法和套袋法更合适

# 熟悉集成最好的方法，是用 randomForest 算法来实验分类和回归
```



### 其他算法



```R
# 梯度提升机 (GBM) -- 集成工具

# GBM 使用已经生长好的树的信息来生长，不需要bootstrap 取样，作为替代，每棵树使用
# 原始数据集的修改版本进行拟合

# 特点

# 可以和随机森林这样的高性能算法竞争
# 能够保持可靠的预测表现，预测结果比简单模型差的情况非常罕见，并且能避免无意义的预测
# 常常被 Kaggle 竞赛的获胜者使用, Heritage 健康奖
# 能明确的处理确实数据 例如 NA
# 无需进行特征缩放
# 能处理的因子水平比随机森林更高 (1024 vs. 32)
# 没有已知的对特征变量数目的限制


# data
data("iris")

n <- nrow(iris)
ntrain <- round(n*0.6)
set.seed(333)
tindex <- sample(n, ntrain)
train_iris <- iris[tindex,]
test_iris <- iris[-tindex,]
head(train_iris)


# gbm 参数

# 收缩参数 shrinkage 一个小的正数，控制提升学习速率
# 树的数量 n.trees 拟合树的梳理
# 每棵树的分叉数目 interaction.depth 控制提升集成的复杂程度

library(gbm)
# Species~. 使用剩下的 iris 数据集变量作为预测因子
# 
gbm1 <- gbm(Species~., distribution = "multinomial",
            data = train_iris, n.trees = 2000, shrinkage = 0.01)

gbm1

## predict.gbm进行预测
prediction <- predict.gbm(gbm1, test_iris, type = "response", n.trees = 1000)

## 使用 summary.gbm 展现每个预测因子的相对影响
summary.gbm(gbm1)

```



```R
# 分类树
# 考虑所有预测因子
data("iris")

n <- nrow(iris)
ntrain <- round(n*0.6)
set.seed(333)
tindex <- sample(n, ntrain)
train_iris <- iris[tindex,]
test_iris <- iris[-tindex,]
head(train_iris)

str(train_iris)
## 导入 tree 包
library(tree)
ct1 <- tree(Species~., data=train_iris)

plot(ct1)
text(ct1)

ct1

summary(ct1)

# 检验

prediction <- predict(ct1, newdata=test_iris, type="class")
prediction

table(prediction, test_iris$Species)

(20+19+17)/60 # test_iris总数60
```





