# 基础用法
`list` 、`table` 、`task` 分别对应 dataview 的列表、表格以及任务内容； 

`from` 指的是从哪里获取数据，可以从 `#tag` 标签获取、 从 `folder` 文件夹获取、从 `[[link]]` 链接获取，或者从链接了 link 的文件获取 `outgoing([[link]])` ； 

`where` 指的是上边获取的数据，要符合怎样的规则，也就是筛选；

`sort` 指的是排序，默认升序。

---

```
```dataview
table time-played AS "啥时候玩的", length AS "玩了多久", rating AS "多好玩"
from "games"
sort rating desc
​```
````

备注，其中的 `SORT` 命令可以是单个参数，例如 `sort rating desc` 指的就是以 `rating` 为参数降序，类似地，当你用 `sort rating asc` 的时候，代表的就是基于 `rating` 升序；那么也许你会想知道，那么怎么实现多个区域分别排序升序呢，很简单，参考下边的官方例子：

```text
SORT rating [ASCENDING/DESCENDING/ASC/DESC], ..., time-played [ASC/DESC]
```




# 高级用法


```

```dataviewjs

dv.list([1, 2, 3]) //生成一个1，2，3的列表
dv.list(dv.pages().file.name)  //生成所有文件的文件名列表
dv.list(dv.pages().file.link)  //生成所有文件的文件链接列表，即双链
dv.list(dv.pages("").file.tags.distinct()) //生成所有标签的列表
dv.list(dv.pages("#book").where(p => p.rating > 7)) //生成在标签 book 内所有评分大于 7 的书本列表
​```
```

## 行内代码块

Dataview 目前支持的行内代码块主要是对于日期以及本页信息的显示，例如：

### 显示时间差

你可以用行内代码块计算出任意的时间差，如下：

```text
`=(date(2021-12-31)-date(today))`
```


## 参考资料

https://zhuanlan.zhihu.com/p/373623264