---
cssClasses: cards, cards-align-bottom, cards-cover,cards-1-1, table-max,cards-cols-8
banner: "![[animals-g79ee9ab76_1920.jpg]]"
banner_y: 0.152
banner_icon: 🌴
tags: index
---

# 站内导航 

| [[index-A|闪念笔记]]  | [[index-B|文献笔记]] | [[index-C|永久笔记]] |---| [[项目看板|项目看板]] | [[index-D|落叶寻枝]] | [[index-E|初稿汇聚]] | [[index-F|编辑校对]] |---| [[关键词漫游|找灵感]] | [[index-H|帮助]] |  

## 在线工具 

 | [[必应搜索]] | [[wikiHow]] |---| [[时空地图]]  | [[全景地图]] |---| [[文本处理]] |---| [[微信读书]] |---| [[审美]] | [[喜马拉雅]] | [[每日一文]] |

---

# 常用功能 

`button-memos`   `button-heian`  `button-mingliang`    `button-tu`    

```ad-abstract
title: 今日最重要的一件事
collapse: closed
 - [x] 读书、实践、创造
``` 


```ad-note
title: 我的小白板
collapse: closed

<iframe src="https://excalidraw.com" width="1500" height="400" > </iframe>  

```


---

#  最近阅读

```dataview
table without id ("![](" + cover + ")") as Cover, file.link as Name, default(split(author," ")[1],author) as "Author"
from "B-文献笔记"  
where status = "在读"
sort file.cday asc 

```

---

# 图表看板

---

## 笔记排行榜

```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Bar

#-----------------#
#- chart data    -#
#-----------------#
data: | 
  dataviewjs:
  return dv.pages()
           .groupBy(p => p.file.folder)
           .sort(p => p.rows.length)
           .map(p => ({folder: p.key || "ROOT", count: p.rows.length}) )
           .array()
           .reverse();

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: "count"
  yField: "folder"
  padding: auto
  height: 400
  color: "#ff2d51"
  
  meta:
    count:
      alias: "数量"

```
---

## 标签云

```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: WordCloud

#-----------------#
#- chart data    -#
#-----------------#
data: | 
  dataviewjs: 
  return (() => {
    const tags = this.app.metadataCache.getTags();
   
    let dataArray = [];
    Object.keys(tags).forEach(key => dataArray.push ({tag: key.replace("#",""),count: tags[key]}));
    return dataArray;
   })();


#-----------------#
#- chart options -#
#-----------------#
options:
  wordField: "tag"
  weightField: "count"
  colorField: "tag"
style:
  backgroundColor: "translucent"

#-----------------------------------------------#
#--- 可选择多彩颜色(colorField) 或单色 (color) ---#
#---  colorField: "tag" ---#
#---  color: "#bb5548" ---#
#-----------------------------------------------#

  wordStyle:
    rotation: 0
```

---

## 柱状图

```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Column

#-----------------#
#- chart data    -#
#-----------------#
data: |
  dataviewjs:
  return dv.pages()
           .groupBy(p => p.file.folder)
           .map(p => ({folder: p.key || "ROOT", count: p.rows.length}))
           .array();

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: "folder"
  yField: "count"
  padding: auto
  color: "#801dae"
  columnStyle:
    fillOpacity: 1
    lineWidth: 0.9
    strokeOpacity: 1.7
    shadowColor: "grey"
    shadowBlur: 5
    shadowOffsetX: 5
    shadowOffsetY: 5

  meta:
    count:
      alias: "文件数量" 
```
---