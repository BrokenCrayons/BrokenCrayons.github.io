---
layout:     post
title:      Task5 Cohen的效应量d中重叠部分的概率计算
subtitle:   使用R进行统计相关问题的解决
date:       2018-12-25
author:     Jiawen Wu
header-img: img/post-bg-cohend.jpg
catalog: true
tags:
    - 统计软件应用
---

### 前言
这个...统计软件课程作业的发布贴——Task5 使用R进行统计相关问题的解决。
我还是要在这里羞耻地写上我的大名吴嘉雯，还有我的学号15307130412。

***	

## 问题的来源

在Cohen的*Statistical Power Analysis for the Behavioral Sciences*(1988, 2nd ed.)的第21-22页，他写道：

> “If we maintain the assumption that the populations
being compared are normal and with equal variability, and conceive them
further as equally numerous, it is possible to define measures of nonoverlap
(U) associated with d which are intuitively compelling and meaningful”

> "When d = 0.1, for instance, U1 here
equals 7.7%, i.e., 7.7% of the area covered by both populations combined is
not overlapped and when d = 2, U1 then equals 81.1%, the amount
of combined area not shared by the two population distributions."

然而他说了**as equally numerous**，却使用的是面积来进行计算，这和他之前的基于频率来计算重叠部分的表达方式矛盾。

因而本post就通过可视化的方式来看一下问题出在哪里。

*Statistical Power Analysis for the Behavioral Sciences*(1988, 2nd ed.)：<a href="https://www.utstat.toronto.edu/~brunner/oldclass/378f16/readings/CohenPower.pdf" >点击下载</a>

## 环境设置

### 需要使用到的R package
- library(dplyr)
- library(ggplot2)
- library(tidyr)
- library(ggthemes)

### ggplot的主题参数设置
```
bg <- "#ECF0F2" # 背景颜色
# 把画图时用到的一些底色都调成背景颜色
theme_set(theme_economist() + theme(panel.background = element_rect(fill = bg),
                                    plot.background = element_rect(fill = bg),
                                    strip.background = element_rect(fill = bg),
                                    legend.background  = element_rect(fill = bg),
                                    legend.key = element_rect(fill = bg)))
# 给会用到的几种点设置一下颜色
scale_color_rpsy <- scale_color_manual(values = c("experiment" = "#E8948E", 
                                                  "control" = "#3E91BA", 
                                                  "control_overlap" = "#82D9CB", 
                                                  "experiment_overlap" = "#FEF3AC"))
```
 再次推荐一下超好用的科研作图调色盘：<a href="https://color.adobe.com/zh/explore/?filter=most-popular&time=month" >Adobe Color CC</a>

### 题外话：介绍一下管道函数 %>% 的用法
- **符号**：**%>%** 管道操作，其意思是将 **%>%** 左边的对象传递给右边的函数 
- **说明**：％>％ 来自dplyr包的管道函数，其作用是将前一步的结果直接传参给下一步的函数，从而省略了中间的赋值步骤，可以大量减少内存中的对象，节省内存
- x %>% f(y) 等同于 f(x, y) 
- y %>% f(x, ., z) 等同于 f(x, y, z )

```
# 例1 下面的两段代码是一样的
plot(data.frame(x = rnorm(n = 200,100,15),
                control = rnorm(n = 200,110,16),
                experiment = rnorm(n = 200,130,13)))

data.frame(x = rnorm(n = 200,100,15),
                control = rnorm(n = 200,110,16),
                experiment = rnorm(n = 200,130,13)) %>% plot()

# 例2 下面的两段代码是一样的
plot(table(mtcars$gear,mtcars$am))

mtcars$gear %>%
                table(.,mtcars$am) %>%
                plot()
```
## 做数据

### 做两个效应量相差为Cohen‘s d = 2 的分布

```
set.seed(4443451)
cohend <- 2
# 产生随机的观测值（实验组和控制组）
n <- 10000
d <- data.frame(x = runif(n, -5, 5.5),
                control = runif(n, min = 0, max = 0.4),
                experiment = runif(n, min = 0, max = 0.4)) # 从同一个分布中抽取样本
str(d)

# 把这两个分布弄成效应量为2的两个分布
d <- d %>% 
        mutate(control = ifelse(control <= dnorm(x, 0, 1), control, NA),
               experiment = ifelse(experiment <= dnorm(x, cohend, 1), experiment, NA))

# d <- mutate(d,control = ifelse(control <= dnorm(x, 0, 1), control, NA),
#               experiment = ifelse(experiment <= dnorm(x, 0.5, 1), experiment, NA))

str(d)
```

### 蒙特•卡罗积分来得到重叠部分的观测值
```
# 写一个计算overlap的函数方便后续调用
overlap <- function(x) {
    pmin(dnorm(x, 0, 1), dnorm(x, cohend, 1))
}

d_long <- d %>%
    gather(dist, y, -x) %>% 
    mutate(overlap = ifelse(y <= overlap(x), paste(dist, "_overlap", sep = ""), dist),
           overlap = factor(overlap)) %>% 
    filter(!is.na(y))
```
## 画图演示
### 画出我们自己做出来的两个分布
```
d_long %>% 
    ggplot(aes(x, y, color = dist)) + 
    geom_point() +
    facet_wrap(~ dist, ncol = 1) +
    labs(title = "Cohen's d = 0.5时控制组和实验组的结果分布", 
         subtile = "每个点代表了1个观测值") +
  theme(text = element_text(family = "STHeiti")) +
  scale_color_rpsy
```
![](https://ws2.sinaimg.cn/large/006tNbRwly1fyib3ll9t7j30nw0j5q5p.jpg)

### 画出重叠图
```{r}
d_long %>% 
    ggplot(aes(x, y, color = dist)) +
    geom_point(alpha = 0.5, size = 1.3) +
    labs(title = "两个分布的重叠部分", subtitle = "Cohen's d = 0.5") +
    theme(text = element_text(family = "STHeiti")) +
     scale_color_rpsy
```
![](https://ws2.sinaimg.cn/large/006tNbRwly1fyib3vhwg2j30oo0ihn6y.jpg)
### 对不同的部分上不同的颜色方便查看
```{r}
# 再次介绍一下使用了管道函数的代码组织方式
mutate(summarise(group_by(d_long,overlap),n = n()),
          prop = n/sum(n),
          prop = paste(round(prop, 1)*100, "%", sep = ""),
          x = c(1.5, 0.25, -1, 0.25),
          y = c(0.2, 0.25, 0.2, 0.2))

# 上面那条就等价于下面这个
labels <- d_long %>% 
    group_by(overlap) %>% 
    summarise(n = n()) %>% 
    mutate(prop = n/sum(n),
           prop = paste(round(prop, 1)*100, "%", sep = ""),
           x = c(-1, 1, 3, 1),
           y = c(0.1, 0.15, 0.1, 0.1))

d_long %>% 
    ggplot(aes(x, y, color = overlap)) +
    geom_point(alpha = 0.6) +
    geom_label(data = labels, 
               aes(x=x, y = y, label = prop, color = overlap), 
               vjust = "center", show.legend = FALSE, size = 5) +
    labs(title = "各个部分所占的百分比", subtitle = "重叠部分的概率解释") +
    theme(text = element_text(family = "STHeiti")) +
    scale_color_rpsy
```
![](https://ws1.sinaimg.cn/large/006tNbRwly1fyib43syawj30oq0ii7e0.jpg)
- 这里计算百分比的办法：观测值的个数／总数
- 所以就是20 + 20 = 40%

### Cohen计算重叠部分使用的分布 
面积计算而非频率计算，所以我们需要把重叠部分的点弄掉一半
```{r}
labels <- d_long %>% 
    filter(overlap != "experiment_overlap") %>% 
    group_by(overlap) %>% 
    summarise(n=n()) %>% 
    mutate(prop = n/sum(n),
           prop = paste(round(prop, 2) * 100, "%", sep = ""),
           x = c(-0.5, 1, 2.5))

d_long %>% 
    filter(overlap != "experiment_overlap") %>% 
    ggplot(aes(x, y, color = overlap)) +
    geom_point(alpha = 0.5, size = 1.3) +
    geom_label(data = labels, 
               aes(x=x, y = 0.15, label = prop, color = overlap), 
               vjust = "center", show.legend = FALSE, size = 5) +
    labs(title = "被两个群体分布都cover了的比例",
         subtitle = "Cohen对于overlap的比例解释") +
    theme(text = element_text(family = "STHeiti")) +
    scale_color_rpsy
```
![](https://ws1.sinaimg.cn/large/006tNbRwly1fyib4r1yzrj30oq0iik0g.jpg)

## Reference
Grice & Barrett (2014) 在他们的文章 "A note on Cohen's overlapping proportions of normal distributions" 里就指出了Cohen犯的这个错误。
<a href="https://journals.sagepub.com/doi/abs/10.2466/03.pr0.115c29z4" >原文链接</a>

***

#### 参考文件下载：
- 【Task5 Rmarkdown及参考文献】
<a href="https://github.com/BrokenCrayons/Statistics-Application/tree/master/%23Task5%20R%E7%BB%9F%E8%AE%A1%E6%A8%A1%E5%9E%8B%E5%8F%AF%E8%A7%86%E5%8C%96" >点击进入下载页面</a>
- 【Task5 录屏演示】<a href="https://pan.baidu.com/s/1hdVhyWETaLWHxykD5v93CQ" >点击进入下载页面</a>
（密码:fcem）
