---
layout:     post
title:      Task4 freeplay时爸妈对宝宝说的话
subtitle:   R中wordcloud函数使用演示
date:       2018-12-10
author:     Jiawen Wu
header-img: img/post-bg-parentchild.jpg
catalog: true
tags:
    - 统计软件应用
---

### 前言
> 这个...统计软件课程作业的发布贴。
> 我还是要在这里羞耻地写上我的大名吴嘉雯。
> 求假装没看见...哼唧

***	

## 概述

本文对67对父／母与1-2岁的孩子在freeplay范式中父母说的话进行分词。看看父母在对语言能力刚刚发展的婴儿讲话时，多讲的是哪些词语。实验为20分钟的freeplay，10分钟为父／母与孩子自由互动，10分钟为父母被分配任务，孩子自己玩耍，两个条件的先后顺序随机。数据集可以[点击这里下载](https://github.com/BrokenCrayons/Statistics-Application/tree/master/%23Task4%20R%E5%87%BD%E6%95%B0%E8%B7%9F%E8%B8%AA%E8%A7%A3%E8%AF%BB)

本文的数据来源：
freeplay过程视频拍摄 -> INTERACT中进行编码 -> 导出编码数据

下面是从INTERACT中导出的数据示例
![](https://raw.githubusercontent.com/BrokenCrayons/ImageBase/master/Images/006tNbRwgy1fy1l4of2vej30o30gg43h.jpg)

数据集导入R中后使用**str()**函数查看

```
'data.frame':	7141 obs. of  11 variables:
 $ Type                : Factor w/ 2 levels "E","T": 2 1 1 1 1 ...
 $ Number              : int  1 1 2 3 4 5 6 7 8 9 ...
 $ Entry               : num  0 0 1.4 4 11.8 ...
 $ Exit                : num  0 0 1.4 4 11.8 ...
 $ Infant.vocalizations: Factor w/ 71 levels "","infant vocalization",..: 5 1 1 1 1 1 ...
 $ Parent.vocalizations: Factor w/ 7 levels "","0.04","infant vocalization",..: 2 1 7 7 7 7 7 ...
 $ Vocalizations       : Factor w/ 5488 levels "","_","_____，那里不要去。",..: 382 1 1 68 815 611 4214 2600 2601 4492 ...
 $ Referent            : Factor w/ 23 levels "","baby gives a can",..: 1 7 1 1 1 1 1 1 1 1 ...
 $ sound               : logi  NA NA NA NA NA NA ...
 $ Others              : Factor w/ 11 levels "","（scare the baby)",..: 1 1 1 1 ...
 $ Duration            : num  NA 0.04 0.04 0.04 0.04 0.04 0.04 ...
```
## 处理数据

对本实验的数据处理需要注意以下几个方面
- 参与实验的家长（父／母），需要区分，也就是每个参与者的Referent是谁需要记录下来
- 不同的实验条件下的说话需要被记录在不同的变量中，condition shift前后需要注意
- 孩子说话和（Infant.vocalizations）和父母说话（Parent.vocalizations）需要区分开
- 以condition shift为切割点的两个condition的准确时间需要记录

### 调整格式

```
is.factor(freeplay$Infant.vocalizations)
freeplay$Infant.vocalizations<-as.character(freeplay$Infant.vocalizations)
freeplay$Referent<-as.character(freeplay$Referent)
```
### 遍历原始数据进行整合
对7141条编码数据按要求进行提取整合
```
i<-1
Sample <-c(); Parent <- c(); Parent_text_c1 <- c()
Baby_text_c1 <- c(); Parent_text_c2 <- c(); Baby_text_c2 <- c()
c1_time <-c(); c2_time<- c()
parent_p <-" "; infant_p <-" "

for(i in 1:7141){
  print(i)
  ## Sample
  if(i==1) {Sample <- c(Sample,freeplay$Infant.vocalizations[1]) 
  condition <- "c1"}
  if (grepl(pattern = "T",x = freeplay$Type[i]) && !(i==1)){ 
    if (!(i==7141)){Sample <- c(Sample,freeplay$Infant.vocalizations[i])}
    if(grepl(pattern = "c1",x = condition)){
      Parent_text_c1 <- c(Parent_text_c1,parent_p)
      Baby_text_c1 <- c(Baby_text_c1, infant_p)
      c1_time <- c(c1_time, freeplay$Exit[i-1] )################ end - begin
      Parent_text_c2 <- c(Parent_text_c2, " ")
      Baby_text_c2 <- c(Baby_text_c2, " ")
      c2_time <- c(c2_time, " ")
    }
    if(grepl(pattern = "c2",x = condition)){
      Parent_text_c2 <- c(Parent_text_c2,parent_p)
      Baby_text_c2 <- c(Baby_text_c2, infant_p)
      c2_time <- c(c2_time,freeplay$Exit[i-1]-time_mark  )################ end - begin
    }
    condition <- "c1"
    parent_p <-" "
    infant_p <-" "
  }
  # condition shift
  if (grepl(pattern = "Condition shift",x = freeplay$Others[i])){
    Parent_text_c1 <- c(Parent_text_c1,parent_p)
    Baby_text_c1 <- c(Baby_text_c1, infant_p)
    c1_time <- c(c1_time, freeplay$Exit[i])################ end - begin
    time_mark <- freeplay$Exit[i]
    condition <- "c2"
    parent_p <-" "
    infant_p <-" "
  }
  ## Character
  if(grepl(pattern = "Dad",x = freeplay$Referent[i])){ 
    Parent <- c(Parent, freeplay$Referent[i])
  }
  if(grepl(pattern = "Mom",x = freeplay$Referent[i])){ 
    Parent <- c(Parent, freeplay$Referent[i])
  }
  if(grepl(pattern = "Grandpa",x = freeplay$Referent[i])){ 
    Parent <- c(Parent, freeplay$Referent[i])
  }
  if(grepl(pattern = "Grandma",x = freeplay$Referent[i])){ 
    Parent <- c(Parent, freeplay$Referent[i])
  }
  ## c1,c2
  if(grepl(pattern = "arent",x = freeplay$Parent.vocalizations[i])){ 
    parent_p <- paste(parent_p,freeplay$Vocalizations[i],collapse = " ")
  }
  if(grepl(pattern = "nfant",x = freeplay$Infant.vocalizations[i])){ 
    infant_p <- paste(infant_p,freeplay$Vocalizations[i],collapse = " ")
  }
}
```
### 使用Rwordseg分词包对所得文本进行分词

Rwordseg的安装需要rJava，同时还需要配置好jre，jdk及相关的环境变量。在Rconsole或terminal里直接从repo下载安装有可能会有奇效。
```
install.packages("Rwordseg",repos="http://R-Forge.R-project.org")
library(Rwordseg)
```
如果不管用的话...网上可多经验贴，或者直接扔给开源中文分词平台
在这里推荐Dr. Kevin Zhang的[NLPIR-ICTCLAS汉语分词系统](https://github.com/NLPIR-team/NLPIR)

先写一个小函数来进行分词并计算词频
```
countwords<- function(list_of_words){
  # segmentCN分词
  a<- lapply(X = list_of_words, FUN=segmentCN) 
  b <- unlist(summary(a))
  word_count <- b[,1]
  # 计算词频
  c <- lapply(X = a,FUN = table)
  # 计算词频的方差
  sd <-unlist(lapply(X = c,FUN = sd))
  # 计算词语种类
  type <- unlist(lapply(X = c,FUN = length))
  return(data.frame(word_count,sd,type))
}

# 调用上面的函数对父母／孩子 * condition 1/2四个条件下的文本进行处理
pc1 <- countwords(Parent_text_c1)
bc1 <- countwords(Baby_text_c1)
pc2 <- countwords(Parent_text_c2)
bc2 <- countwords(Baby_text_c2)
result_rawfreeplay_light <- data.frame(Sample,pc1,bc1,pc2,bc2)
result_rawfreeplay <- data.frame(Sample,Parent, Parent_text_c1, pc1, Baby_text_c1, bc1, c1_time, Parent_text_c2, pc2,Baby_text_c2, bc2, c2_time )
```

## 使用wordcloud函数制作词云

为了制作词云，我们需要得到一个67位家长合在一起的关键词表以及对应的词频。所以首先要把所有的 parent volcolization 给拼到一起。然后再使用分词包segmentCN重新分词，计算词频。

```
cloud <-c()
for(i in 1:67){
  cloud <- paste(cloud,result_rawfreeplay$Parent_text_c1[i],collapse = " ")
  cloud <- paste(cloud,result_rawfreeplay$Parent_text_c2[i],collapse = " ")
}
myfile.words<-unlist(lapply(X = cloud,FUN = segmentCN)) 
myfile.freq <- table(unlist(myfile.words))
myfile.freq<- rev(sort(myfile.freq))
myfile.freq<- data.frame(word=names(myfile.freq), freq=myfile.freq)
mycolors<- brewer.pal(8,"Dark2")
```
所有的都配置好了之后，就可以调用wordcloud函数来画图了
-------> 
```
wordcloud(myfile.freq$word,myfile.freq$freq.Freq,random.order=FALSE,random.color=FALSE,colors=mycolors,family ="GB18030 Bitmap") 
```
上图！

![](https://raw.githubusercontent.com/BrokenCrayons/ImageBase/master/Images/006tNbRwly1fy1u9p0lapj30dw0dwn0p.jpg)

***

#### 参考文件下载：
- [Task 4 FollowUp 代码及数据] 
<a href="https://github.com/BrokenCrayons/Statistics-Application/tree/master/%23Task4%20R%E5%87%BD%E6%95%B0%E8%B7%9F%E8%B8%AA%E8%A7%A3%E8%AF%BB" >点击进入下载页面</a>

