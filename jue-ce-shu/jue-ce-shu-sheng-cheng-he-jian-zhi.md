## 1. 决策树生成算法

### ID3算法

ID3算法的核心是在决策树各个节点上应用**信息增益**准则选择特征，递归地构建决策树。

具体方法是：从根节点开始，对节点计算所有可能的特征的信息增益，选择信息增益最大的特征作为节点的特征，由该节点的不同取值建立子节点。再对子节点递归使用同样的方法，构建决策树，直到所有特征的信息增益均很小或者没有特征可以选择为止。

输入：训练数据集$$D$$，特征集$$A$$，阈值$$\varepsilon$$；

输出：决策树$$T$$

1）若$$D$$中所有实例属于同一类$$C_k$$，则$$T$$为单节点树，并将类$$C_k$$作为该节点的类标记，返回$$T$$。

2）若$$A$$为空，则$$T$$为单节点树，并将$$D$$中实例树最大的类$$C_k$$作为该节点的类标记，返回$$T$$。

3）否则，计算$$A$$中各个特征对$$D$$的信息增益，选择信息增益最大的特征$$A_g$$

4）如果$$A_g$$的信息增益小于阈值$$\varepsilon$$，则置$$T$$为单节点树，并将$$D$$中实例数最大的类$$C_k$$作为该节点的类标记，返回$$T$$

5）否则，对$$A_g$$的每一个可能值$$\alpha_i$$，按照$$A_g$$的可能取值将$$D$$分割成若干个非空子集$$D_i$$，构造子节点，对于第$$i$$个子节点，以$$D_i$$为训练集，以$$A-\{A_g\}$$为特征集，递归得进行步骤1-5，返回$$T_i$$。

### C4.5算法

C4.5算法与ID3算法类似，只是C4.5在生成过程中，用**信息增益比**来选择特征。

## 2. 决策树的剪枝

决策树生成算法递归地产生决策树，一直到不能继续下去为止，这样产生的决策树对训练数据的分类很准确，但是对于未知的测试数据的分类却没有那么准确，即产生过拟合现象。

过拟合的原因在于学习时过多地考虑如何提高对训练数据的正确分类，从而构造出过于复杂的决策树。

在决策树学习中将生成的树进行简化的过程称为剪枝（prunning），具体地剪枝从已生成的树上减掉一些子树或叶节点，并将其根节点或父节点作为新的叶子节点，从而简化分类模型。

决策树的剪枝通过极小化决策树整体的损失函数（loss function）或代价函数（cost function）来实现。

设树$$T$$的叶节点的个数为$$|T|$$，$$t$$是树$$T$$的叶节点，该叶节点有$$N_t$$个样本点，其中$$k$$类的样本点有$$N_{tk}$$个，$$k=1,2,...,K$$，$$H_t(T)$$为叶节点上的经验熵，则决策树学习的损失函数可以定义为：


$$
C_\alpha(T)=\displaystyle\sum_{t=1}^{|T|}N_tH_t(T)+\alpha|T|
$$


其中$$\alpha    \geqslant0$$，经验熵$$H_t(T)$$为


$$
H_t(T)=-\displaystyle\sum_{k=1}^K    \dfrac{N_{tk}}{N_t}\mathrm{log}_2 {\dfrac{N_{tk}}{N_t}}
$$


于是可以将上面的式子转化为


$$
C_\alpha(T)=-\displaystyle\sum_{t=1}^{|T|}\displaystyle\sum_{k=1}^K    N_{tk}\mathrm{log}_2 {\dfrac{N_{tk}}{N_t}}+\alpha|T|=C(T)+\alpha |T|
$$


$$C(T)$$表示模型对训练数据的预测误差，即模型与训练数据的拟合程度，$$|T|$$表示模型复杂度，参数$$\alpha$$控制两者之间的影响，可以理解为正则化参数。较大的$$\alpha$$表示选择简单的模型，较小的$$\alpha$$表示选择较复杂的模型。

### 决策树的剪枝算法

输入：生成算法产生的整个树$$T$$，参数$$\alpha$$

输出：修剪后的子树$$T_{\alpha}$$

1）计算每个节点的经验熵

2）递归地从树的叶节点向上回溯，假定一组叶节点回溯到父节点之前与之后的整体树分别为$$T_B$$与$$T_A$$，其对应的损失函数分别为$$C_\alpha(T_B)$$与$$C_\alpha(T_A)$$，如果$$C_\alpha(T_A)\leqslant C_\alpha(T_B)$$，则进行剪枝，即其父节点变为新的叶子节点。

3）返回步骤2，一直到不能继续位置，最后得到剪枝后的损失函数最小的子树$$T_\alpha$$。

剪枝算法可以由一种动态规划的算法实现。

![](/assets/prunning.jpeg)

