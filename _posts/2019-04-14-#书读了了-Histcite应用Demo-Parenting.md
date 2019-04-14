---
layout:     post
title:      父母教养实践与儿童认知（学业方面）发展
subtitle:   使用Histcite进行引文分析
date:       2019-04-13
author:     Jiawen Wu
header-img: img/post-bg-histcite.jpg
catalog: false
tags:
    - 书读了了
---
<script type="text/javascript">
// 禁止右键菜单
document.oncontextmenu = function(){ return false; };
// 禁止文字选择
document.onselectstart = function(){ return false; };
// 禁止复制
document.oncopy = function(){ return false; };
// 禁止剪切
document.oncut = function(){ return false; };
// 禁止粘贴
document.onpaste = function(){ return false; };
</script>

### Histcite 软件安装简介

关于Histcite是什么，前人已经夸夸了很多。这个软件已经多年停更，然而它非常low的外表也不能掩盖它的锋芒，这颗5MB的金子在那些个于paper的海洋中~~遨游~~挣扎的童鞋们的电脑上发着光。Histcite基于web of science导出的文本及引文进行分析。这么多年来web of science导出文本的格式已经有了小小的变化，所以拖入Histcite分析前需要人为操作一下。灰常麻烦，建议直接<a href="https://zhuanlan.zhihu.com/p/20902898" >下载大神写的一体化小程序</a>，自动完成首行文本格式修改工作。

Histcite是Windows软件，所以想在Mac上安装成功又是一场恶战（我最讨厌配置环境这种事情了）。在Mac的虚拟机上跑，一直无法读取文件......网上的的方法全试了个遍，也没能解决这个问题。

![](https://ws1.sinaimg.cn/large/006tNc79ly1g2264qwfd8j30f304h3z2.jpg)

当然最后还是试出来了，大概是个综合性因素吧。要成功安装，需要进行的蜜汁操作有： 
1.  IE浏览器：点击globe Internet -> 点击Local intranet -> 点击Sites -> 点击Advanced -> 输入http://127.0.0.1 -> 点击添加 -> 点击OK
2.  在虚拟机的C盘建个文件夹，把导出的文件放进去，路径中不得有中文名

![](https://ws2.sinaimg.cn/large/006tNc79ly1g226dtzx49j30e7035t9i.jpg)

3.  将文件拖拽到Histcite Icon上，而不要打开Histcite后才点击ADD导入文件

总之希望大家安装成功，使用顺利！

### 进入正题：父母教养实践与儿童认知（学业方面）发展的文献检索

![](https://ws4.sinaimg.cn/large/006tNc79ly1g226jie00mj30sj06xgmh.jpg)

在Web of Science中选择 "Web of Science Core Collection"后进行搜索，然后可以点击左侧的“创建跟踪服务”来订阅RSS，或者右侧的“创建引文报告”来看关于这个领域文章的简单分析。要导入Histcite中需要点击保存为其他文件格式。

![](https://ws1.sinaimg.cn/large/006tNc79ly1g226m2ncyxj30zp07wn0a.jpg)

分三次输出为三个纯文本文件（web of science每次最多只能输出500条，而我的搜索结果里有1453条，所以分三次输出）。需要选择“全记录与引用的参考文献”和“纯文本”格式。

![](https://ws4.sinaimg.cn/large/006tNc79ly1g226olojm3j30ju09oq3o.jpg)

将三个txt文件拖入Histcite后作图，得到文献脉络图如下所示。

![](https://ws1.sinaimg.cn/large/006tNc79ly1g226rbz5t5j31810u07wh.jpg)

### 引文分析

可见按年份排序的关键文章为74(1998), 136(2001), 176(2002):  
(Landry, Smith, Swank, Assel, & Vellet, 2001; Linver, Brooks-Gunn, & Kohen, 2002; McLoyd, 1998)  

近年领域内较有价值的综述文章有956(2015), 521(2011), 575(2011), 431(2009):  
(Coley, Lewin-Bizan, & Carrano, 2011; Iruka, 2009; Lucassen et al., 2015; Treyvaud et al., 2009)

仔细读了图中重要结点处的文献及Histcite给出的无关键词高被引文献后，大概可以有以下的梳理结果。

从文献分析可以看出最初关注家庭教育与儿童认知能力发展主要是针对disadvantage populations（早产儿，低SES人群，发展障碍儿童，移民家庭等）。主要的发现是在SES（社会经济地位）对儿童认知能力发展的影响中，一个很重要的中介变量home-based cognitive stimulation (McLoyd, 1998) – [74]，其中学习和语言刺激的不足是导致低收入群体的孩子学业能力发展和IQ得分较低的主要原因。比如家庭中学习刺激(as indicated by, e.g., presence of toys that teach color, size, and shape) ，语言刺激(verbal interactions between mothers and children)的不足都会导致儿童在语言、数学能力等学业上表现不佳。McLoyd (1998)指出较好的保护性做法是使用严格且强指导性的教养方式（well- defined house rules, clear sanctions for breaking rules, close supervision）加上高温暖，这种教养方式能补偿贫穷的儿童不良的成长环境对其发展的影响。

在另一篇重要的文献中Landry et al. (2001) – [136]指出responsive parenting（回应式教养）对早产儿和足月儿童的认知能力和社交能力的发展都有着非常重要的作用。在谈及回应式教养行为时，前人将其分为了情感-情绪回应和认知回应行为两个部分(Baumrind, 1967, 1978)。其中认知回应行为包含了有意识地增加语言输入，关注儿童的兴趣爱好（enriched verbal input, attention to children's interests）。在婴儿期父母对儿童的兴趣和行为的回应能够很好地帮助孩子发展认知能力，而在儿童期中后期影响的因素就更多了，包括认知刺激的数量和质量以及对话频次和质量（give and take in verbal exchanges）。这些研究一致地证明了稳定的持续的回应式教养对儿童认知发展有着促进作用，能够帮助at-risk population的儿童在学业上表现得更好。

在这些研究中，我们关注的的家庭教养中的认知刺激活动（cognitively stimulating activities）大多是通过HOME问卷／观察打分(Bradley & Caldwell, 1980)来测量的，这个55条目的评分表测量了孩子的玩具，拼图和书籍的数量; 母亲对孩子使用语言刺激的频率; 父母与孩子一起出游的频率，孩子是否被带到博物馆，以及孩子与父母一起吃饭的次数; 母亲是否鼓励孩子学习颜色，数字和基本单词等。在许多研究中都发现了HOME得分与孩子智力测验得分的高正相关(Linver et al., 2002)。

### 对父母实践的启示

####  语言能力发展
    Dieterich, Assel, Swank, Smith, and Landry (2006)发现，在3岁和4岁的日常活动中，母亲的口头支架（母亲的言语与儿童语言和行为的一致）能够预测孩子8岁时的语言理解能力，及10岁时的阅读理解能力。(Senechal & LeFevre, 2014)。
    
    在语言文字能力的发展方面，有研究指出，父母参与的间接教学活动（例如，与孩子一起阅读的故事书）或在家中直接教学活动（例如，以词汇为中心的教学）越多，儿童的识字技能和阅读能力发展越好(Senechal & LeFevre, 2014)。Martini and Sénéchal (2012)在研究中指出有许多家庭活动中出现的物件和场景都能经过指导促进儿童的语言能力发展，如路牌、商品标签、贺卡、信件、购物清单、食谱菜单、报纸等等。

#### 数学思维能力发展
    根据基于HOME量表进行的三项研究（使用三种不同的基于年龄的HOME版本），可以获得的玩具和学习材料的数量和质量、家庭成员与儿童一起积极参与学习的机会，以及儿童接触各种户外活动的机会和质量与儿童数学相关认知发展的能力有关(Bradley, Caldwell, Rock, Hamrick, & Harris, 1988)。

    但是，在为儿童提供提升数字理解能力的机会方面，并非所有经历对每一个孩子来说都是一样的。比如，面对一堆乐高积木的孩子可能很难弄清楚如何建造一座墙或一座桥。如果有具备一定知识的成年人或同伴协助，这个孩子可能会更快地掌握积木选择和积木定位。根据Vygotsky（1978）的观点，当成人（或同龄人）提供良好的适宜框架（fitting structure），深思熟虑的建议以及针对儿童在特定情况下的努力的给出特定的反应时（让儿童保持在近端发展区proximal development中），儿童的认知能力发展效果最好。
    
    在孩子认知能力的培养中，孩子的学习动机和兴趣也是影响认知发展结果的重要因素。父母在教学过程中使用积极策略（如赞美或商量）的孩子比从事父母使用消极策略的孩子（例如批评，恐吓; Zhou et al., 2006）更长时间地从事计算任务。

### 实操建议

1. 除了关注孩子数数、计算的能力外，还应该在日常生活中更多地发起数学概念的对话（比如，大小、多少、次序等高级数学概念）
2.	家庭里非正式的数字相关活动（如卡牌游戏、购物、煮饭中包含的数字认知情景）比正式的数学教学活动（如写数字，练习算术或背诵数字序列）对孩子的数学能力有更重要的影响(LeFevre et al., 2009; Skwarchuk, Sowinski, & LeFevre, 2014)。
3.	Newcombe and Frick (2010)强调了成年人支持幼儿共同参与日常活动（如购物：预测购物袋中有多少杂商品；烹饪的过程中发展空间意识和解决问题能力：决定如何切割百吉饼使其适合烤面包机的大小；或旅行：在地图上规划路线等等）对儿童认知发展的重要性。
4.	基于询问的学习促进：问开放性问题。捕捉孩子的兴趣点和好奇心，进行引导（在园艺工作这项活动中，家长会有很多的机会让孩子接触到空间定位的问题。特别是在收获和种植过程中，家长可以指导儿童进行与植物和花园相关的活动，在这些活动中数字概念，尺寸估计和比较都非常有用。另外家长还可以指导孩子进行观察，预测，评估和比较；博物馆则是另一个能引发集中的类似对话的场所）。

一个小例子：

![](https://ws2.sinaimg.cn/large/006tNc79ly1g22716rvuuj30cj08nq4j.jpg)


当然儿童的认知发展还有很多很多其他的方面比如视听觉、空间思维、注意、记忆等等...预知父母教养如何促进儿童其它认知能力的发展，且听下回分解（如果有的话）...

---
Reference

Baumrind, D. (1967). Child care practices anteceding three patterns of preschool behavior. Genet Psychol Monogr, 75(1), 43-88. Retrieved from https://www.ncbi.nlm.nih.gov/pubmed/6032134. 

Baumrind, D. (1978). Parental Disciplinary Patterns and Social       Competence in Children. Youth & Society, 9(3), 239-276. Retrieved   from <Go to ISI>://WOS:A1978ET22400002. doi:Doi 10.1177/0044118x7800900302

Bradley, R. H., & Caldwell, B. M. (1980). The Relation of Home-Environment, Cognitive Competence, and Iq among Males and Females. Child Development, 51(4), 1140-1148. Retrieved from <Go to ISI>://WOS:A1980KW75000022. doi:Doi 10.2307/1129555

Bradley, R. H., Caldwell, B. M., Rock, S. L., Hamrick, H. M., & Harris, P. (1988). Home Observation for Measurement of the Environment - Development of a Home Inventory for Use with Families Having Children 6 to 10 Years Old. Contemporary Educational Psychology, 13(1), 58-71. Retrieved from <Go to ISI>://WOS:A1988L849600006. doi:Doi 10.1016/0361-476x(88)90006-9

Coley, R. L., Lewin-Bizan, S., & Carrano, J. (2011). Does Early Paternal Parenting Promote Low-Income Children's Long-Term Cognitive Skills? Journal of Family Issues, 32(11), 1522-1542. Retrieved from <Go to ISI>://WOS:000295693700005. doi:10.1177/0192513x11402175

Dieterich, S. E., Assel, M. A., Swank, P., Smith, K. E., & Landry, S. H. (2006). The impact of early maternal verbal scaffolding and child language abilities on later decoding and reading comprehension skills. Journal of School Psychology, 43(6), 481-494. Retrieved from <Go to ISI>://WOS:000234896600004. doi:10.1016/j.jsp.2005.10.003

Iruka, I. U. (2009). Ethnic Variation in the Association Between Family Structures and Practices on Child Outcomes at 36 Months: Results From Early Head Start. Early Education and Development, 20(1), 148-173. Retrieved from <Go to ISI>://WOS:000264832900006
https://www.tandfonline.com/doi/abs/10.1080/10409280802206916. doi:10.1080/10409280802206916

Landry, S. H., Smith, K. E., Swank, P. R., Assel, M. A., & Vellet, S. (2001). Does early responsive parenting have a special importance for children's development or is consistency across early childhood necessary? Developmental Psychology, 37(3), 387-403. Retrieved from <Go to ISI>://WOS:000170879500009. doi:10.1037//0012-1649.37.3.387

LeFevre, J. A., Skwarchuk, S. L., Smith-Chant, B. L., Fast, L., Kamawar, D., & Bisanz, J. (2009). Home Numeracy Experiences and Children's Math Performance in the Early School Years. Canadian Journal of Behavioural Science-Revue Canadienne Des Sciences Du Comportement, 41(2), 55-66. Retrieved from <Go to ISI>://WOS:000268354300001. doi:10.1037/a0014532

Linver, M. R., Brooks-Gunn, J., & Kohen, D. E. (2002). Family processes as pathways from income to young children's development. Developmental Psychology, 38(5), 719-734. Retrieved from <Go to ISI>://WOS:000177732200008. doi:10.1037//0012-1649.38.5.719

Lucassen, N., Kok, R., Bakermans-Kranenburg, M. J., Van Ijzendoorn, M. H., Jaddoe, V. W. V., Hofman, A., . . . Tiemeier, H. (2015). Executive functions in early childhood: The role of maternal and paternal parenting practices. British Journal of Developmental Psychology, 33(4), 489-505. Retrieved from <Go to ISI>://WOS:000362837900008
https://onlinelibrary.wiley.com/doi/pdf/10.1111/bjdp.12112. doi:10.1111/bjdp.12112

Martini, F., & Sénéchal, M. (2012). Learning literacy skills at home: Parent teaching, expectations, and child interest. Canadian Journal of Behavioural Science / Revue canadienne des sciences du comportement, 44(3), 210-221. doi:10.1037/a0026758

McLoyd, V. C. (1998). Socioeconomic disadvantage and child development. American Psychologist, 53(2), 185-204. Retrieved from <Go to ISI>://WOS:000072129300008. doi:10.1037//0003-066x.53.2.185
Newcombe, N. S., & Frick, A. (2010). Early Education for Spatial Intelligence: Why, What, and How. Mind Brain and Education, 4(3), 102-111. Retrieved from <Go to ISI>://WOS:000286400300002. doi:10.1111/j.1751-228X.2010.01089.x

Senechal, M., & LeFevre, J. A. (2014). Continuity and Change in the Home Literacy Environment as Predictors of Growth in Vocabulary and Reading. Child Development, 85(4), 1552-1568. Retrieved from <Go to ISI>://WOS:000340603400017. doi:10.1111/cdev.12222

Skwarchuk, S. L., Sowinski, C., & LeFevre, J. A. (2014). Formal and informal home learning activities in relation to children's early numeracy and literacy skills: the development of a home numeracy model. Journal of Experimental Child Psychology, 121, 63-84. Retrieved from https://www.ncbi.nlm.nih.gov/pubmed/24462995
https://www.sciencedirect.com/science/article/pii/S0022096513002439?via%3Dihub. doi:10.1016/j.jecp.2013.11.006

Treyvaud, K., Anderson, V. A., Howard, K., Bear, M., Hunt, R. W., Doyle, L. W., . . . Anderson, P. J. (2009). Parenting Behavior Is Associated With the Early Neurobehavioral Development of Very Preterm Children. Pediatrics, 123(2), 555-561. Retrieved from <Go to ISI>://WOS:000262678700019
https://pediatrics.aappublications.org/content/pediatrics/123/2/555.full.pdf. doi:10.1542/peds.2008-0477

Eileen  
2019.4.14.

