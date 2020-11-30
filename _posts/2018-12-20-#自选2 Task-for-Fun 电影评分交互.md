---
layout:     post
title:      自选2 Task-for-Fun 电影评分交互制作
subtitle:   ggplot Leaflet and Shiny Plot
date:       2018-12-20
author:     Jiawen Wu
header-img: img/post-bg-movies2.jpg
catalog: true
tags:
    - 统计软件应用
---

## 前言

    接着上一节的内容，自己挖的坑跪着也要填上。
    嗯......我是负责任的好孩子。
    那么，直接进入正题。
	
## 使用的数据集介绍

本case中使用的数据分为两部分：

- 第一部分是由University of Minnesota的GroupLens研究小组提供的MovieLens数据集<a href="https://grouplens.org/datasets/movielens/">【点击进入MovieLens数据下载页面】</a>。该数据集由“users.dat”，“ratings.dat”和“movie.dat”组成。

- 第二部分是画图需要用到的地理数据：美国每个州的边界地理数据和每个州对应的邮政编码。 <a href="https：//www.census.gov/geo/maps-data/data/cbf/cbf_state.html">【点击进入Boundary数据下载页面】</a>

具体的数据处理的方法可以参见上一个post<a href="https://brokencrayons.github.io/2018/12/18/%E8%87%AA%E9%80%891-Task-for-Fun-%E7%94%B5%E5%BD%B1%E8%AF%84%E5%88%86%E9%9D%A2%E9%9D%A2%E8%A7%82/"> 自选 Task-for-Fun 电影评分面面观</a>

# STEP3 综合分析及可视化

## 导入数据

导入地理数据并将其与电影数据相结合
```
states <- readOGR(dsn="...",
                  layer = "cb_2016_us_state_500k",verbose = FALSE)
states <- states %>% select(-STATEFP)
```
```
mean_rating_state <- user_rating %>% group_by(state) %>%
                     mutate(mean_rating_by_state = mean(rating)) %>%
                     select(state,mean_rating_by_state) %>%
                     unique
```

```
rating_state <- inner_join(states,mean_rating_state,by=c("STUSPS"="state"))
```
## 使用Leaflet展示每个州的平均电影评分
```
bins <- c(quantile(rating_state$mean_rating_by_state,probs = c(0,0.1,0.2,0.4,0.6,0.8,1)))
pal <- colorBin("YlOrRd", domain = rating_state$mean_rating_by_state, bins = bins)
labels <- sprintf(
  "<strong>%s</strong><br/>%g ",
  rating_state$STUSPS , rating_state$mean_rating_by_state) %>% lapply(htmltools::HTML)

m <- rating_state %>% leaflet() %>%
  setView(-100.2727, 37.8716,4) %>%
  addTiles() %>%
  addPolygons(
    fillColor = ~pal(mean_rating_by_state),
    weight = 2,
    opacity = 1,
    color = "white",
    dashArray = "3",
    fillOpacity = 0.7,
    highlight = highlightOptions(
      weight = 5,
      color = "#666",
      dashArray = "",
      fillOpacity = 0.7,
      bringToFront = TRUE),
    label = labels, 
    labelOptions = labelOptions(
    style = list("font-weight" = "normal", padding = "3px 8px"),
    textsize = "15px",
    direction = "auto"))%>% 
  addLegend(pal = pal, values = ~mean_rating_by_state, opacity = 0.7, title = NULL,
  position = "bottomleft") %>%
  addCircleMarkers(lng = -122.2727, lat = 37.8716)
```
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fyd7d8q1t7g30k80ee1l5.gif)
颜色越深，代表人们的平均评分越高。

## 显示男性Animation电影的平均评分

```
data_male_animation <- all %>% filter(gender == "M") %>%
                      filter(str_detect(genres,"Animation")== TRUE) %>%
                      group_by(age) %>%
                      mutate(mean_rating_ma = mean(rating)) %>%
                      select(age,mean_rating_ma,occupation) %>%
                      unique

ggplot(data_male_animation,aes(age,mean_rating_ma))+
  geom_point()+
  geom_line(aes(group = occupation, color = as.factor(occupation) ))+
  labs(x='age',y='mean ratings',title='mean rating for drama movie by male by age') +
    theme(plot.title = element_text(hjust = 0.5))+
  labs(color = "Occupation")+
  scale_color_discrete(labels=c( "other or not specified","academic/educator","artist","clerical/admin","college/grad student","customer service","doctor/health care","executive/managerial","farmer","homemaker","K-12 student","lawyer","programmer","retired","sales/marketing","scientist","self-employed","technician/engineer","tradesman/craftsman","unemployed","writer"))
```
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fyd73wppavj311c0qo0tb.jpg)
图中不同颜色的线条代表不同的职业群体。



# STEP4 Shiny交互图的制作展示
Shinyapp 如果要发布，需要如下写两个文件，一个是ui文件，一个是server文件。ui文件设置的是网页的界面，而server则负责网页背后的计算程序。
```
# ui.R

library(shiny)
library(ggplot2)

shinyUI(fluidPage(
  titlePanel("Reactive lines of ratings of movies by Gender and Genres"),
  sidebarLayout(sidebarPanel(
    selectInput("GenderSelector",
                label = "Select Plot Gender:",
                choice = c("Male","Female"),
                selected = "Male"),
    selectInput("GenresSelector",
                label = "Select Plot Genres:",
                choice = c("Action","Adventure","Animation","Children's","Comedy","Crime","Documentary","Drama","Film-Noir","Horror","Musical","Mystery","Romance","Sci-Fi","Thriller","War","Western"                ),
                selected = "Action")),
    mainPanel(
      textOutput("showTitle"),
      plotOutput("showThePlot")
    )
  )
))

# server.R

library(shiny)
library(tidyverse)
library(ggplot2)
library(dplyr)
library(lubridate)
library(stringr)

users <- read_delim("users.dat",col_names = FALSE,delim = "::")
users <- users %>% select(-X2,-X4,-X6,-X8) %>% 
  rename(user_id = X1, gender = X3,age = X5,occupation = X7,zip_code = X9)
users <- users %>% mutate(zip_code2 = substr(zip_code,1,5)) %>% 
  select(-zip_code) %>% rename(zip_code = zip_code2) %>%
  mutate(zip_code3 = substr(zip_code,1,3))
zipcode <- read_csv("zipcode.csv")
zipcode <- zipcode %>% select(-X3)
user_zipcode <- inner_join(users,zipcode,by=c("zip_code3"="zipcode"))
ratings <- read_delim("ratings.dat",col_names = FALSE,delim = "::")
ratings <- ratings %>% select(-X2,-X4,-X6) %>% 
  rename(user_id = X1, movie_id = X3,rating = X5,timestamp = X7)
ratings <- ratings %>% mutate(time = ymd_hms("1970-1-1 00:00:00")+seconds(timestamp)) %>%             select(-timestamp) %>%
  mutate(hour = hour(time))
user_rating <- inner_join(x=user_zipcode,y=ratings,by = "user_id") 

movie <- read_delim("movies.dat",col_names = FALSE,delim = "::")
movie <- movie %>% mutate(title1 = paste(X3,X4, sep = ":" )) %>%
  mutate(title = gsub(":NA","",title1)) %>%
  select(-X2,-X3,-X4,-title1) %>%
  rename(movie_id = X1, genres = X5) %>%
  mutate(year = str_match(title,"[0-9][0-9][0-9][0-9]"))
all <- inner_join(movie,user_rating)

data_selected <- function(x,y){
  ds<- all %>% filter(gender == x) %>%
    filter(str_detect(genres,y)== TRUE) %>%
    group_by(age) %>%
    mutate(mean_rating = mean(rating)) %>%
    select(age,mean_rating,occupation) %>%
    unique
  ds}

shinyServer(function(input, output) {
  
  output$showTitle <- renderText({
    paste("The lines plot of mean rating of",input$GenresSelector,"movies by",input$GenderSelector,"by age")
  })
  output$showThePlot <- renderPlot({
    gender <- switch(input$GenderSelector,
                   "Male"= "M",
                   "Female" = "F")
    
      ggplot(data_selected(gender,input$GenresSelector),aes(age,mean_rating))+
      geom_point()+
      geom_line(aes(group = occupation, color = as.factor(occupation) ))+
      labs(x='age',y='mean ratings') +
      theme(plot.title = element_text(hjust = 0.5))+
      labs(color = "Occupation")+
      scale_color_discrete(labels=c( "other or not specified","academic/educator","artist","clerical/admin","college/grad student","customer service","doctor/health care","executive/managerial","farmer","homemaker","K-12 student","lawyer","programmer","retired","sales/marketing","scientist","self-employed","technician/engineer","tradesman/craftsman","unemployed","writer"))
    
  }
  )
})
```
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fyd7csns39g30hs09xb2d.gif)

最终的交互网页展示可以用shiny server发布 <a href="https://brokencrayons.shinyapps.io/2_movielens_part2/"> 点击这里查看</a>

***
### 文件下载：
- [录屏操作一对一教学]  ～ 敬请期待 ～
