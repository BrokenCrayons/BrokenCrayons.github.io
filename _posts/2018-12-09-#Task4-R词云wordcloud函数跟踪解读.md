---
layout:     post
title:      Task4 词云函数wordcloud
subtitle:   R函数跟踪解读 wordcloud makes words less cloudy
date:       2018-12-10
author:     Jiawen Wu
header-img: img/post-bg-wordcloud.jpg
catalog: true
tags:
    - 统计软件应用
---

### 前言

	是的，今天又是个认真做作业的我...统计软件课程作业的发布贴。
	～自动忽略～隐身～唔该嗮！

***	
先放个果子：

**爸爸妈妈和一岁宝宝freeplay说话的词云图**

![](https://raw.githubusercontent.com/BrokenCrayons/ImageBase/master/Images/006tNbRwgy1fy0h2yh6waj30dw0dwdhv.jpg)

***
## wordcloud函数介绍

先来看看函数头：
```
function (words, freq, scale = c(4, 0.5), min.freq = 3, max.words = Inf, 
          random.order = TRUE, random.color = FALSE, rot.per = 0.1, 
          colors = "black", ordered.colors = FALSE, use.r.layout = FALSE, 
          fixed.asp = TRUE, ...)
```
### 一一解释一下函数头里包含的参数
* **words** — 关键词列表
  * the words
* **freq** - 关键词对应的词频列表（用来调整字号的大小，词频越高的词字越大）
  * the words‘ frequencies
* **scale** - 规定字号的范围，比如默认的c(4,0.5)表示规定字号在0.5到4之间
  * A vector of length 2 indicating the range of the size of the words 
* **min.freq** - 最小的可显示词频，低于最小词频的词不会被显示，默认为3
  * words with frequency below min.freq will not be plotted
* **max.words** - 显示的最大词数量（在超出最大词量的情况下排在末尾的会被舍掉，默认为无限多）
  * Maximum number of words to be plotted. least frequent terms dropped
* **random.order** - 词在图上的排列顺序 （TRUE：词随机排列；FALSE：词按频数从图中心位置往外降序排列，即频数大的词出现在最中心的位置上；默认为TRUE）
  * plot words in random order. If false, they will be plotted in decreasing frequency
* **random.color** - 调整字体的颜色（TRUE：字体颜色随机分配；FALSE：根据词频分配字体颜色；默认为FALSE）
  * choose colors randomly from the colors. If false, the color is chosen based on the frequency
* **rot.per** - 控制有多少词旋转90度摆放，比如默认为0.1，表示有10%的词将在图中被旋转90度 
  * proportion words with 90 degree rotation
* **colors** 字体颜色列表（从词频最低到词频最高分别使用什么字体，默认为黑色）
  * color words from least to most frequent
* **ordered.colors** - 控制字体颜色使用顺序（TRUE：按照指定的顺序一一对应给出每个关键词字体颜色；FALSE：任意给出字体颜色；默认为FALSE）
  * if true, then colors are assigned to words in order
* **use.r.layout** - 是否使用r函数来做矛盾分析
  * if false, then c++ code is used for collision detection, otherwise R is used
* **fixed.asp** - 是否固定图形的长宽比例
  * if TRUE, the aspect ratio is fixed. Variable aspect ratio only supported if rot.per==0

* *Notes* 也可以不填入词频而直接填写一堆需要分析的材料，或者使用语料库的数据
  * If freq is missing, then words can either be a character vector, or Corpus. If it is a vector and freq is missing, standard stop words will be removed prior to plotting.


### 现在来看看函数本身
#### 函数头头

```
library(wordcloud)
function (words, freq, scale = c(4, 0.5), min.freq = 3, max.words = Inf, 
          random.order = TRUE, random.color = FALSE, rot.per = 0.1, 
          colors = "black", ordered.colors = FALSE, use.r.layout = FALSE, 
          fixed.asp = TRUE, ...) 
{
  if (!fixed.asp && rot.per > 0) 
    stop("Variable aspect ratio not supported for rotated words. Set rot.per=0.")
  tails <- "g|j|p|q|y"
  last <- 1
  nc <- length(colors)
  if (missing(freq)) { # 如果没有指明词频这一列的话，说明用户直接把需要分析的材料全部放进来了
    requireNamespace("tm") #在函数中要求有条件地调用tm (text mining)
    requireNamespace("slam") #在函数中要求有条件地调用slam (Sparse Lightweight Arrays and Matrices)
    if (is.character(words) || is.factor(words)) { # 判断给的关键词列表是否为character类型或factor类型
      corpus <- tm::Corpus(tm::VectorSource(words)) # 先将我们的关键词表转换为tm这个包可以处理的语料库corpus的形式
      corpus <- tm::tm_map(corpus, tm::removePunctuation) # 使用tm这个包里的函数removePunctuation,删去所有的标点符号 
      corpus <- tm::tm_map(corpus, function(x) tm::removeWords(x, tm::stopwords())) 
      # 使用tm这个包里的函数，自定义一个参数，遍历corpus，删除所有非检索用词
    }# 如果是character类型或factor类型
    else corpus <- words # 如果用户给出的words不是character也不是factor类型的话，直接将words赋值给corpus
    tdm <- tm::TermDocumentMatrix(corpus) # 使用tm包中的corpus函数把corpus变成一个词项文档矩阵
    freq <- slam::row_sums(tdm) # 然后使用slam这个包中的row_sums函数将上面的这个矩阵中有值的行数进行计数，就得到了词频
    words <- names(freq) # 将freq这个表的名字赋值给words，其实就是生成了一个关键词表。
  }
  if (ordered.colors) { # 如果按照词频分配颜色的这个按钮被置为TRUE的话
    if (length(colors) != 1 && length(colors) != length(words)) {# 先判断颜色的个数是否与关键词表的长度相同
      stop(paste("Length of colors does not match length of words", # 如果不同就跳出函数并报错
                 "vector"))
    }
  }
  if (min.freq > max(freq)) 
    min.freq <- 0      # 如果允许的最小词频比给出的词表中最大的词频还大的话，就自动把最小词频改为0
```

上面这一par主要是在检查传入的word列表，或者根据传入的东西创建一个语料库类型的东西。根据传入的信息和要求，调整颜色，size等等。

#### 判断是否重叠的函数

下面这一par突然跳开去写了一个服务于后面的名叫**overlap**的函数，用于判断某个词是否与已经出现在图上的其他词重叠了。

```
  ################################################## 写一个名叫overlap的函数 服务于下面调整词语在图上的位置
  overlap <- function(x1, y1, sw1, sh1) { 
    if (!use.r.layout) # 如果用的是c++而不是r的话
      return(is_overlap(x1, y1, sw1, sh1, boxes)) # 返回 is_overlap(x1, y1, sw1, sh1, boxes) 也就是一个直接的判断是否重叠了的logical值
    s <- 0
    if (length(boxes) == 0) # 如果boxes里啥都没有，就返回FALSE（也就是当屏幕上没有东西的时候，新加入的词是不会和别的东西重叠到一起去的）
      return(FALSE)
    for (i in c(last, 1:length(boxes))) { # boxes为空，则走一次这个流程（last控制）；boxes不为空，则for循环遍历boxes里面所有已经存在了的元素
      bnds <- boxes[[i]]
      x2 <- bnds[1] # 将boxes里第i个元素的四个定位点搬出来，和x1, y1, sw1, sh1分别比较
      y2 <- bnds[2]
      sw2 <- bnds[3]
      sh2 <- bnds[4]
      if (x1 < x2) # 判断x方向上是否有冲突
        overlap <- x1 + sw1 > x2 - s
      else overlap <- x2 + sw2 > x1 - s
      if (y1 < y2) # 判断y方向上是否有冲突
        overlap <- overlap && (y1 + sh1 > y2 - s)
      else overlap <- overlap && (y2 + sh2 > y1 - s)
      if (overlap) { # x或y方向上有至少一个方向上有冲突
        last <<- i # 则全局赋值 把i给last，下一次调用last的时候，last就变成i而不是1了
        return(TRUE) # 然后返回TRUE表示真的重叠了
      }
    }
    FALSE # 否则返回FALSE表示没有重叠
  }
```

#### 根据要求更改参数
下面这一par又是新一轮的根据各种传入的要求更改相应的参数

```  
  ##################################################
  ord <- rank(-freq, ties.method = "random") # 按照词频的倒序进行排列，当有遇到词频一样的情况时，采用随机的排列方式
  words <- words[ord <= max.words] # 选出关键词列表中的top x个词（如果有规定最大词数的话），并按顺序排列
  freq <- freq[ord <= max.words] # 同样把词频列也缩减到top x个词的词频，并按顺序排列
  if (ordered.colors) { # 如果字体是要求按词频顺序着色的
    colors <- colors[ord <= max.words] # 把颜色也缩减到要求的个数，并按顺序排列
  }
  if (random.order) # 如果词云的排列方式时随机的话
    ord <- sample.int(length(words)) # 按关键词表的个数产生随机排列的一列数字作为次序
  else ord <- order(freq, decreasing = TRUE) # 如果词云的方式不是随机排列，则按照词频的降序排列
  words <- words[ord] # 重新排列
  freq <- freq[ord] # 重新排列
  words <- words[freq >= min.freq] # 将大于最小词频的词重新排列
  freq <- freq[freq >= min.freq] # 将大于最小词频的筛选出来
  if (ordered.colors) { # 如果安顺序着色
    colors <- colors[ord][freq >= min.freq] # 那么着色的列表也需要修改到对应的长度
  }
  thetaStep <- 0.1 
  rStep <- 0.05
  plot.new() # 开始画图
  op <- par("mar")
  par(mar = c(0, 0, 0, 0)) # 将四条边的边框都设置为0
  if (fixed.asp) # 如果指定了图形得比例的话
    plot.window(c(0, 1), c(0, 1), asp = 1) # 就将图形设置为1:1的形式
  else plot.window(c(0, 1), c(0, 1)) # 如果没有指定图形的比例，就不设置asp
  normedFreq <- freq/max(freq) # 根据最大的词频，将所有的频率调整为标准化的词频（好像也不是标准化，就是成比例的吧）
  size <- (scale[1] - scale[2]) * normedFreq + scale[2] # 用这个公式调整size，使得词频从0-max的词字号match最小字号—最大字号
```
#### 小心翼翼地画图
终于到了要一点点把词语安排在图上的时候了！

```
  boxes <- list() # 一开始是个空的list，下面的for循环每走一格，就会增加一个元素
  for (i in 1:length(words)) { # 遍历关键词表
    rotWord <- runif(1) < rot.per # 随机判断正在遍历的这个词要不要被旋转(runif(1) -> 生成1个0-1之间均匀分布随机数的函数) 
    r <- 0
    theta <- runif(1, 0, 2 * pi) # 生成1个0-2pi间均匀分布的随机数当成theta
    x1 <- 0.5
    y1 <- 0.5
    wid <- strwidth(words[i], cex = size[i], ...) # 根据之前调整的参数计算这个词的占地面积 - 宽
    ht <- strheight(words[i], cex = size[i], ...) # 根据之前调整的参数计算这个词的占地面积 - 高
    if (grepl(tails, words[i]))  # 判断词语中是否有"g|j|p|q|y"这几个字母
      ht <- ht + ht * 0.2 # 如果有的话要对高度再做一些调整（当然对中文字就不适用了）
    if (rotWord) { # 如果根据上面的runif这个词要被旋转的话
      tmp <- ht # 那就宽变成高，高变成宽
      ht <- wid
      wid <- tmp
    }
```
#### 调整图形位置
利用上面写好的overlap函数调整图形位置

```
    ################################################## 下面这一部分都是为了调整图形的位置
    isOverlaped <- TRUE
    while (isOverlaped) { 
      if (!overlap(x1 - 0.5 * wid, y1 - 0.5 * ht, wid, ht) && x1 - 0.5 * wid > 0 && y1 - 0.5 * ht > 
          0 && x1 + 0.5 * wid < 1 && y1 + 0.5 * ht < 1) { # 如果overlap返回FALSE表示没有重叠，而且也没有超出框框的限制
        if (!random.color) { # 如果不是随机填色的话
          if (ordered.colors) { # 如果字体颜色顺序需要控制
            cc <- colors[i] # 就按之前调好的颜色列表相应的填色
          }
          else { # 如果字体颜色不需要控制
            cc <- ceiling(nc * normedFreq[i]) # 就按照词频排列来填色（向上取整：词表长度*标准化的频率）
            cc <- colors[cc] # 调取排名为cc的颜色
          }
        }
        else { # 如果是随机填色的话
          cc <- colors[sample(1:nc, 1)] # 那就在colors 1-nc之间随机抽一个颜色
        }
        text(x1, y1, words[i], cex = size[i], offset = 0, 
             srt = rotWord * 90, col = cc, ...) # 在调整好的跟别的地方没有重叠的x1,y1上将词打印出来，字号也是调好的，颜色也是调好的，是不是要旋转也是根据rotword确定的。
        boxes[[length(boxes) + 1]] <- c(x1 - 0.5 * wid, y1 - 0.5 * ht, wid, ht) # 然后把它的各项指标放到boxes里占坑，以便之后的词不要碰到它
        isOverlaped <- FALSE # 然后将isOverlaped置为FALSE退出while循环
      }
      else { # 如果overlap返回TRUE表示有重叠
        if (r > sqrt(0.5)) { # 判断r已经到了边缘，没办法继续了，就祭出警告，然后中止画图
          warning(paste(words[i], "could not be fit on page. It will not be plotted."))
          isOverlaped <- FALSE
        }
        theta <- theta + thetaStep # 调整角度
        r <- r + rStep * thetaStep/(2 * pi) # 调整半径
        x1 <- 0.5 + r * cos(theta) # 重新设置x1
        y1 <- 0.5 + r * sin(theta) # 重新设置y1 然后回到 while里再试一遍和boxes里其他已经有了的词有没有冲突
      }
    }
  }
  par(mar = op) # 将一开始调整了的环境参数调回来
  invisible() # 返回一张看不见的图 （返回对象的临时不可见副本的函数）
}
```
## wordcloud函数升级版

wordcloud2函数

可以生成更美丽的图噢，上几张图大家自行感受一下。

![](https://raw.githubusercontent.com/BrokenCrayons/ImageBase/master/Images/006tNbRwly1fy0xs6ye79j308508cjuy.jpg)

credit to [The R Graph Gallery](https://www.r-graph-gallery.com/)

![](https://raw.githubusercontent.com/BrokenCrayons/ImageBase/master/Images/006tNbRwly1fy0xsa0a84j30dc0dotc3.jpg)

credit to [The R Graph Gallery](https://www.r-graph-gallery.com/)

关于wordcloud2详细的介绍大家可以<a href="https://cran.r-project.org/web/packages/wordcloud2/vignettes/wordcloud.html">点击自取</a>


![](https://raw.githubusercontent.com/BrokenCrayons/ImageBase/master/Images/006tNbRwly1fy0ylgtd7aj31b803174b.jpg)

嘿嘿嘿

至于开头出现的果子

具体介绍wordcloud函数的应用

在下一篇post里

拉到最后点NEXT就可以看到啦！


***

#### 本po参考文件下载：
- [Task 4 R wordcloud function Intro] 
<a href="https://github.com/BrokenCrayons/Statistics-Application/tree/master/%23Task4%20R%E5%87%BD%E6%95%B0%E8%B7%9F%E8%B8%AA%E8%A7%A3%E8%AF%BB" >点击进入下载页面</a>
- [录屏Demo观看及下载]
<a href="https://pan.baidu.com/s/1_19M0kcJ2FKkbUt01GNn4w" >点击进入下载页面</a> (密码:qfmu)
