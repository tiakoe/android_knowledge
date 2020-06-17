



### 泛型擦除

- 修饰成员变量等结构相关的泛型不会被擦除
- 容器类泛型会被擦除



**桶排序 (Bucket sort)**或所谓的**箱排序**，是一个[排序算法](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95/5399605)，工作的原理是将数组分到有限数量的桶子里。每个桶子再个别排序（有可能再使用别的[排序算法](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95/5399605)或是以递归方式继续使用桶排序进行排序）。桶排序是[鸽巢排序](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%E9%B8%BD%E5%B7%A2%E6%8E%92%E5%BA%8F/8010555)的一种[归纳](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%E5%BD%92%E7%BA%B3/7118703)结果。当要被排序的数组内的数值是均匀分配的时候，桶排序使用线性时间（[Θ](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%CE%98)（*n*））。但桶排序并不是 比较排序，他不受到 O(n*log n) [下限](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/%E4%B8%8B%E9%99%90/10215216)的影响。

桶排序是计数排序的升级版，可以不用局限于整数。思路大致相似。首先要确定桶的区间范围。计算方式是（Max - Min）/ （length -1）。length表示桶的数量，然后最后一个桶表示最大值，其他的桶就分范围来存储数据。