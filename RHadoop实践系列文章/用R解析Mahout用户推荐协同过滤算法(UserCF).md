用R解析Mahout用户推荐协同过滤算法(UserCF)
==================

首发于我的blog:  http://blog.fens.me/r-mahout-usercf/

**转载请注明**
+ Weibo: @Conan_Z
+ Email: bsspirit@gmail.com
+ Blog: http://www.fens.me/blog

由于时间仓促，欢迎大家一起讨论。
****************

**用R全面解析Mahout的基于用户推荐协同过滤算法(UserCF),改进的采用欧氏距离，并用R语言
实现，与Mahout的结果进行对比。**

**Mahout是Hahoop家族用于机器学习的一个框架，包括三个主要部分，推荐，聚类，分类！
我在这里做的是推荐部分。推荐系统在现在的互联网应用中很常见，比如，亚马逊会推荐
你买书，豆瓣会给你一个书评，影评。**

1. Mahout的模型介绍
2. R语言模型实现
3. 算法实现的原理–矩阵变换
4. 算法总结
5. 参考资料

![用R解析Mahout用户推荐协同过滤算法(UserCF)] 

Mahout版本

 
 ``  {     org.apache.mahout
        mahout-core
        0.5}
``       





