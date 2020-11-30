---
layout:     post
title:      自选1 Task-for-Fun 电影评分面面观 
subtitle:   the Research on MovieLens dataset
date:       2018-12-18
author:     Jiawen Wu
header-img: img/post-bg-movies.jpg
catalog: true
tags:
    - 统计软件应用
---

## 前言

    不同的人群对电影的观感是不一样的，我们就来看看不同的人群对不同电影的评分是怎样的吧！在Kaggle上看到了MovieLens的数据集，就想说看看大家对电影的观感是怎么样的，又有什么样的评分倾向。
    
	
## 使用的数据集介绍

本case中使用的数据分为两部分：

- 第一部分是由University of Minnesota的GroupLens研究小组提供的MovieLens数据集<a href="https://grouplens.org/datasets/movielens/">【点击进入MovieLens数据下载页面】</a>该数据集由“users.dat”，“ratings.dat”和“movie.dat”组成。

- 第二部分是画图需要用到的地理数据：美国每个州的边界地理数据和每个州对应的邮政编码 <a href="https://www.census.gov/geo/maps-data/data/cbf/cbf_state.html">【点击进入Boundary数据下载页面】</a>（友情提示：打开这个链接要挂梯子还得开全局模式）

下载好了数据，我们接下来要做的事情就是：
- **STEP1** 导入数据，清理数据，把数据整理称我们好用的形式
- **STEP2** 分析每个数据文件并使用ggplot进行数据可视化
- **STEP3** 合并我们感兴趣的文件，并使用ggplot和leaflet进行可视化
- **STEP4** 在下一节中我们可能还会尝试着做Shiny的反应线图（看期末季+申请季心情）
   
# STEP1 准备数据

## 导入数据
```
users <- read_delim("users.dat",col_names = FALSE,delim = "::")
users <- users %>% select(-X2,-X4,-X6,-X8) %>% 
rename(user_id = X1, gender = X3,age = X5,occupation = X7,zip_code = X9)
```
然而这个数据吧，并没有那么干净。我们需要对一些不符合规范的异常数据进行筛选和处理。比如说有人把5位的邮政编码填成了9位的......
```
users <- users %>% mutate(zip_code2 = substr(zip_code,1,5)) %>% 
                select(-zip_code) %>% rename(zip_code = zip_code2) %>%
                mutate(zip_code3 = substr(zip_code,1,3))
```
在处理异常值之后，我们可以将“用户”users.dat“和“zip code”文件组合在一起，以便查看每个用户所属的州。
```
zipcode <- read_csv("zipcode.csv")
zipcode <- zipcode %>% select(-X3)
user_zipcode <- inner_join(users,zipcode,by=c("zip_code3"="zipcode"))
```
导入“ratings” 数据集，处理“time”这一列数据.
```
ratings <- read_delim("ratings.dat",col_names = FALSE,delim = "::")
ratings <- ratings %>% select(-X2,-X4,-X6) %>% 
rename(user_id = X1, movie_id = X3,rating = X5,timestamp = X7)
```
这数据中的Timestamp时间戳是指从 1/1/1970 00:00:00 开始经历的秒数.为了分析打分的频率和时间属性，我们需要转换一些信息（用户评定的那一天的以小时为单位计算的时间，周期等）
```
ratings <- ratings %>% mutate(time = ymd_hms("1970-1-1 00:00:00")+seconds(timestamp)) %>%             
select(-timestamp) %>% mutate(hour = hour(time))

user_rating <- inner_join(x=user_zipcode,y=ratings,by = "user_id") 
user_rating_period <- user_rating %>% group_by(user_id) %>%
            mutate(difftime = difftime(max(time),min(time),units = "mins"),period = difftime/n()) %>% select(user_id,difftime,period)
```
同样的导入“movie”文件然后进行数据的重构。
```
movie <- read_delim("movies.dat",col_names = FALSE,delim = "::")
movie <- movie %>% mutate(title1 = paste(X3,X4, sep = ":" )) %>%
                   mutate(title = gsub(":NA","",title1)) %>%
                   select(-X2,-X3,-X4,-title1) %>%
                   rename(movie_id = X1, genres = X5) %>%
                   mutate(year = str_match(title,"[0-9][0-9][0-9][0-9]"))
```
为了进一步展示，我们将“movie”和“ratings”文件合并，以便后续查看电影年份与电影评分之间的关系。

# STEP2 ggplot数据可视化
***
### ●   一天24小时评分量的分布

```
ggplot(ratings,aes(hour)) +
    geom_histogram(color = "white",fill="orange",binwidth = 0.8) +
    labs(x='hour of the day',y='count',title='the distribution of the rating time') +
    theme(plot.title = element_text(hjust = 0.5)) 
```
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fyckwnkyzmj311c0qojro.jpg)

根据直方图，人们更有可能在下午3点到凌晨4点这个时段里对电影进行评分。因此，对电影播放平台来说在这段时间内跳出评分窗口可能能收集到更多的数据。

### ●   用户平均评分时段的分布
```
ggplot(user_rating_period,aes(period)) +
    geom_histogram(color = "white",fill="orange") +
    labs(x='period(mins)',y='count',title='the distribution of the period of ratings per user') +xlim(0,10000)+ylim(0,1e+05)+
    theme(plot.title = element_text(hjust = 0.5))
```
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fycl0qr39zj311c0qoq36.jpg)
这图表明人们更有可能一次性给出几个电影的评分，而不是在一段较长的时间里对电影进行单独评分。

### ●   电影总体评分按年度的分布
```
ggplot(movie_for_year,aes(year1,mean_rating_by_year))+
  geom_point(aes(size = number_of_ratings, color = "purple") )+
  geom_smooth()+
  labs(x='year of the movie',y='mean ratings',title='the trend of the ratings for movies in different year') +
    theme(plot.title = element_text(hjust = 0.5))
```
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fycl5rgl3gj311c0qogm5.jpg)
如图所示，可以看出人们对电影的评分是有一定驱使的。相对来说，人们更喜欢1940年至1960年间拍摄的电影。电影制作的时间越近，总体评分越低，当然这也可能受到当年制作的电影评分数量的影响。 随着早年电影数量的增加和在早些年制作的电影上评分的人数的增加，评分可能会有所降低。

### ●   排名前20的电影
```
mean_rate_each_movie <- movie_rating %>%
                      group_by(movie_id) %>%
                      mutate(mean_rating_each_movie = mean(rating)) %>%
                      select(movie_id,title,mean_rating_each_movie,year,genres) %>%
                      unique 
top_20_movie <- mean_rate_each_movie %>% ungroup() %>%
                      arrange(-mean_rating_each_movie) %>%
                      slice(1:20)
top_20_movie
```
下面就是打印出来的排名前20的电影信息
```
rank rating year genres                         Title 
 1   5    1973  Horror                    Baby, The (1973)                                                   
 2   5    1995  Documentary               Gate of Heavenly Peace, The (1995)                                 
 3   5    1995  Drama                     Schlafes Bruder (Brother of Sleep) (1995)                          
 4   5    1998  Comedy                    Follow the Bitch (1998)                                            
 5   5    2000  Documentary               Bittersweet Motel (2000)                                           
 6   5    1967  Comedy                    Smashing Time (1967)                                               
 7   5    1954  Adventure                 Ulysses (Ulisse) (1954)                                            
 8   5    1947  Crime                     Lured (1947)                                                       
 9   5    1936  Drama                     Song of Freedom (1936)                                             
10   5    1973  Comedy|Drama|Western      One Little Indian (1973)                                           
11   4.8  1964  Drama                     I Am Cuba (Soy Cuba/Ya Kuba) (1964)                                
12   4.75 1994  Drama                     Lamerica (1994)                                                    
13   4.67 1998  Drama                     Apple, The (Sib) (1998)                                            
14   4.61 1962  Action|Adventure          Sanjuro (1962)                                                     
15   4.56 1954  Action|Drama              Seven Samurai (The Magnificent Seven) (Shichinin no samurai) (1954)
16   4.55 1994  Drama                     Shawshank Redemption, The (1994)                                   
17   4.52 1972  Action|Crime|Drama        Godfather, The (1972)                                              
18   4.52 1995  Animation|Comedy|Thriller Close Shave, A (1995)                                              
19   4.52 1995  Crime|Thriller            Usual Suspects, The (1995)                                         
20   4.51 1993  Drama|War                 Schindler's List (1993)         
```



### ●   年龄，职业 V.S. 电影类别偏好
我们可以根据年龄和职业来转换数据并查看不同人群最喜欢的电影类型
```
all <- inner_join(movie,user_rating)
movie_genres <- c("Action","Adventure","Animation","Children's","Comedy","Crime","Documentary","Drama","Film-Noir","Horror","Musical","Mystery","Romance","Sci-Fi","Thriller","War","Western")

best_movie_by_age_occu <- all %>% group_by(age,occupation) %>%
                          filter(rating == max(rating)) %>%
                          select(age,occupation,genres,movie_id) %>%
                          unique %>%             
                          mutate(Action = sum(str_count(genres,movie_genres[1]),na.rm = TRUE),Adventure = sum(str_count(genres,movie_genres[2]),na.rm = TRUE),Animation = sum(str_count(genres,movie_genres[3]),na.rm = TRUE),Childrens = sum(str_count(genres,movie_genres[4]),na.rm = TRUE),Comedy = sum(str_count(genres,movie_genres[5]),na.rm = TRUE),Crime = sum(str_count(genres,movie_genres[6]),na.rm = TRUE),Documentary = sum(str_count(genres,movie_genres[7]),na.rm = TRUE),Drama=sum(str_count(genres,movie_genres[8]),na.rm = TRUE),Film_Noir=sum(str_count(genres,movie_genres[9]),na.rm = TRUE),Horror=sum(str_count(genres,movie_genres[10]),na.rm = TRUE),Musical=sum(str_count(genres,movie_genres[11]),na.rm = TRUE),Mystery=sum(str_count(genres,movie_genres[12]),na.rm = TRUE),Romance=sum(str_count(genres,movie_genres[13]),na.rm = TRUE),Sci_Fi=sum(str_count(genres,movie_genres[14]),na.rm = TRUE),Thriller=sum(str_count(genres,movie_genres[15]),na.rm = TRUE),War=sum(str_count(genres,movie_genres[16]),na.rm = TRUE),Western=sum(str_count(genres,movie_genres[17]),na.rm = TRUE))  %>%
                    select(age,occupation,Action:Western) %>% unique 
long_b <- best_movie_by_age_occu %>%
                      gather(genre,frequency,Action:Western) %>%
                      group_by(age,occupation) %>%
                      filter(frequency == max(frequency)) %>%
                      select(age, occupation, genre) %>% slice(1)
wide_b <- long_b %>% spread(key = occupation, value = genre)  %>%
  rename("other or not specified"="0","academic/educator"="1","artist"="2","clerical/admin"="3","college/grad student"="4","customer service"="5","doctor/health care"="6","executive/managerial"="7","farmer"="8","homemaker"="9","K-12 student"="10","lawyer"="11","programmer"="12","retired"="13","sales/marketing"="14","scientist"="15","self-employed"="16","technician/engineer"="17","tradesman/craftsman"="18","unemployed"="19","writer"="20")
wide_b
```
下面贴一个简要的结果，有兴趣的童鞋可以自己跑跑代码看看完整表格吼！
```
# A tibble: 7 x 22
# Groups:   age [7]
age `other or not specified` `academic/educator` artist
* <int>                    <chr>               <chr>  <chr>
1     1                    Drama              Comedy Comedy
2    18                    Drama               Drama  Drama
3    25                    Drama               Drama  Drama
4    35                    Drama               Drama  Drama
5    45                    Drama               Drama  Drama
6    50                    Drama               Drama  Drama
7    56                    Drama               Drama  Drama
# ... with 18 more variables: `clerical/admin` <chr>, `college/grad
#   student` <chr>, `customer service` <chr>, `doctor/health care` <chr>,
#   `executive/managerial` <chr>, farmer <chr>, homemaker <chr>, `K-12
#   student` <chr>, lawyer <chr>, programmer <chr>, retired <chr>,
#   `sales/marketing` <chr>, scientist <chr>, `self-employed` <chr>,
#   `technician/engineer` <chr>, `tradesman/craftsman` <chr>,
#   unemployed <chr>, writer <chr>
```
我们可以看到哈，人们还是比较喜欢Drama的，果然......人生如戏。


### 文件下载：
- [录屏操作一对一教学]  ～ 敬请期待 ～

***
说好的STEP3和4呢？？？
- STEP3 合并我们感兴趣的文件，并使用ggplot和leaflet进行可视化
- STEP4 在下一节中我们可能还会尝试着做Shinyapp的反应线图并上传到网上

～（看期末季+申请季心情）～