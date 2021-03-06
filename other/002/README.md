# Huffman Coding
- 用于无损数据压缩的熵算法(权算法)。

## 定义
- 在变字长编码中, 如果编码字节(码字)长度**严格按照**对应符号出现的概率大小**逆序排列**, 则其平均码字长度为最小。 如下图分析所示:

![len_pic][1]

------

## 具体方法
- 先按出现的概率大小排序, 把两个最小的概率相加, 作为新的概率和剩余的概率重新排队, 再把最小的两个概率相加, 再重新排队, 直到最后变成1。
- 每次相加时都将`"0"`和`"1"`赋予相加的两个概率, 读出时由该符号开始一直走到最后的`"1"`, 将路线上所遇到的`"0"`和`"1"`按最低位到最高位的顺序排好, 就是该符号的霍夫曼编码。

------

## 特点
- 编出来的码都是**异字头码**, 保证了码的唯一可读性;
- 由于编码长度可变。因此译码时间较长, 使得霍夫曼编码的压缩与还原相当费时;
- 编码长度不统一, 硬件实现有难度;
- 对于不同信号源的编码效率不同, 当信号源的符号概率为2的负幂次方时, 达到100%的编码效率; 若信号源符号的概率相等, 则编码效率最低;
- 由于0与1的指定是任意的, 故由上述过程编出的最佳码不是唯一的, 但其平均码长是相同的, 故不影响编码效率与数据压缩性能。

------

## 压缩原理
- 我们把文件中的一定位长的值看作是符号, 比如把8位长的256(2^8)种值, 也就是字节的256种值看作是符号。我们根据这些符号在文件中出现的频率, 对这些符号重新编码。对于出现次数非常多的, 我们用较少的位来表示, 对于出现次数非常少的, 我们用较多的位来表示。这样一来, 文件的一些部分位数变少了, 一些部分位数变多了, 由于变小的部分比变大的部分**多**,  所以这个文件的大小还是会减少, 所以文件得到了压缩。

------

## 压缩算法实现分析
- 课外题记: 哈夫曼算法诞生于1952年间, 迄今为止仍然是经久不衰, 广泛应用于各种数据压缩。

------

## 采用霍夫曼算法注意点
- 霍夫曼编码没有错误保护功能, 在译码时, 如果码串中没有错误, 那么就能一个接一个地正确译出代码。但是如果码串中有错误, 哪怕仅仅是一个1位(`1bit`)出现了错误, 不但这个码本身译错, 更糟糕的是一错一大串, 全乱了套, 这种现象称为**错误传播**(`error propagation`)。计算机对这种错误也无能为力, 说不出错误在哪里, 更谈不上去纠正它。
- 霍夫曼码是可变长度码, 因此很难随意查找或者调用压缩文件中间的内容, 然后再译码, 这就需要在存储代码之前加以考虑。尽管如此, 霍夫曼码还是在广泛地应用。

------

## 霍夫曼编码模型
- 思想是压缩数据出现概率高的用短编码, 出现频率低的用长编码, 且每个字符编码都不一样。
- 压缩数据单个字符出现的概率抽象为叶子节点的权值, huffman树叶子节点到根节点的编码(是父节点左子节点要么填0, 要么填1)作为字符的唯一编码。

------

## 实现时需要注意的规则
- 最左的放置在左边, 作为父节点的左节点。
- 每次都从没有设置父节点的所用节点中(叶子节点和分支节点一样对待), 从数组小下标到大下标优先顺序遍历。
- 当前搜寻的次数`i + n`作为新生成的分支节点的数组下标。

------

## 实现的过程和具体算法思想
### 两个数据结构
- huffman的树节点结构体;
- 从huffman树叶子节点编码的结构体。

### 两个处理过程
- 建立huffman树:
    - 基本思想是: 
        - 对于没有设置父节点的节点集中选出最小的两个, 最小的放置在左边, 次小的放置在右边。
        - 设置好父节点和左右子节点关系, 方便获得huffman编码。

- 从huffman树中得到叶子节点的huffman编码
    - 基本思想是:
        - 对于建立好的Huffman树的每个叶子节点, 由编码的数组的末端也是叶子节点的最底端, 往上遍历。
        - 如果是父节点的左节点那么用编码数组填1, 如果是父节点的右节点那么编码填0, 一直往上追溯到根节点。
        

  [1]: ./images/len.png "len.png"