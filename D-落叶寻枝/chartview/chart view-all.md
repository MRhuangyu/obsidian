
# 图表可视化展示

## 标签云-词频统计-样式1
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
    return dataArray.filter(p => !p.tag.includes("<")&&!p.tag.includes(">"));
   })();


#-----------------#
#- chart options -#
#-----------------#
options:
  wordField: "tag"
  weightField: "count"
  color: "#9c5333"
  wordStyle:
    fontFamily: "Verdana"
    fontSize: [24, 80]
  interactions:
    type: "element-active"
  style:
    backgroundColor: "translucent"
  state:
    active:
      style:
        lineWidth: 3
```

## 标签云-词频统计--样式2
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

#-----------------------------------------------#
#--- 可选择多彩颜色(colorField) 或单色 (color) ---#
#---  colorField: "tag" ---#
#---  color: "#e6b422" ---#
#-----------------------------------------------#

  wordStyle:
    rotation: 0

```
---
## 柱状图-文件数量
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

## 笔记数量排行榜-文件数量

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
  height: 500

  meta:
    count:
      alias: "数量"
```

---

## 饼状图-文件数量

```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Pie

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
  angleField: "count"
  colorField: "folder"
  radius: 1
  label:
    type: "spider"
    content: "{percentage}\n{name}"
  legend:
    layout: "horizontal"
    position: "bottom"
```


---

## 雷达图-书籍类型
```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Radar

#-----------------#
#- chart data    -#
#-----------------#
data:
  - item: "马列主义"
    user: "a"
    score: 2
  - item: "哲学"
    user: "a"
    score: 65
  - item: "社会科学总论"
    user: "a"
    score: 10
  - item: "政治、法律"
    user: "a"
    score: 4
  - item: "军事"
    user: "a"
    score: 2
  - item: "经济"
    user: "a"
    score: 10
  - item: "文化教育"
    user: "a"
    score: 28
  - item: "语言文字"
    user: "a"
    score: 14
  - item: "文学"
    user: "a"
    score: 20
  - item: "艺术"
    user: "a"
    score: 0	
  - item: "历史地理"
    user: "a"
    score: 9
  - item: "自然科学总论"
    user: "a"
    score: 1
  - item: "数理化"
    user: "a"
    score: 0
  - item: "天文地球"
    user: "a"
    score: 3
  - item: "生物"
    user: "a"
    score: 3
  - item: "医药卫生"
    user: "a"
    score: 3
  - item: "农业"
    user: "a"
    score: 4
  - item: "工业"
    user: "a"
    score: 11
  - item: "交通"
    user: "a"
    score: 1
  - item: "航空航天"
    user: "a"
    score: 1
  - item: "环境安全"
    user: "a"
    score: 0
  - item: "综合"
    user: "a"
    score: 1

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: "item"
  yField: "score"
  seriesField: ""
  color: "#ff461f"
  meta:
    score:
      alias: "Score"
      min: 0
      nice: true
  xAxis:
    line: null
    tickLine: null
  yAxis:
    label: false
    grid:
      alternateColor: "rgba(0, 0, 0, 0.04)"
  point: {}
  area: {}
```

## 混合折线图-温度-假数据
```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Mix

#-----------------#
#- chart data    -#
#-----------------#
data.area:
  - time: 1246406400000
    temperature: [14.3, 27.7]
  - time: 1246492800000
    temperature: [14.5, 27.8]
  - time: 1246579200000
    temperature: [15.5, 29.6]
  - time: 1246665600000
    temperature: [16.7, 30.7]
  - time: 1246752000000
    temperature: [16.5, 25.0]
  - time: 1246838400000
    temperature: [17.8, 25.7]

data.line:
  - time: 1246406400000
    temperature: 21.5
  - time: 1246492800000
    temperature: 22.1
  - time: 1246579200000
    temperature: 23
  - time: 1246665600000
    temperature: 23.8
  - time: 1246752000000
    temperature: 21.4
  - time: 1246838400000
    temperature: 21.3

#-----------------#
#- chart options -#
#-----------------#
options:
  appendPadding: 8
  syncViewPadding: true
  tooltip:
    shared: true
    showMarkers: false
    showCrosshairs: true
    offsetY: -50

options.area:
  axes: {}
  meta:
    time:
      type: 'time'
      mask: 'MM-DD'
      nice: true
      tickInterval: 172800000
      range: [0, 1]
    temperature:
      nice: true
      sync: true
      alias: '温度范围'
  geometries:
    - type: 'area'
      xField: 'time'
      yField: 'temperature'
      mapping: {}

options.line:
  axes: false
  meta:
    time:
      type: 'time'
      mask: 'MM-DD'
      nice: true
      tickInterval: 172800000
      range: [0, 1]
    temperature:
      sync: 'temperature'
      alias: '温度'
  geometries:
    - type: 'line'
      xField: 'time'
      yField: 'temperature'
      mapping: {}
    - type: 'point'
      xField: 'time'
      yField: 'temperature'
      mapping:
        shape: 'circle'
        style:
          fillOpacity: 1
```

## 折线图-假数据

```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: TinyLine

#-----------------#
#- chart data    -#
#-----------------#
data: [0, 64, 417, 438, 887, 309, 397, 0, 550, 575, 563, 430, 525, 592, 492, 0, 467, 513, 546, 983, 340, 539, 243, 226, 192]

#-----------------#
#- chart options -#
#-----------------#
options:
  height: 400 
  autoFit: true
  smooth: false
  tooltip: true
  annotations:
    - type: "line"
      start: ["min", "mean"]
      end: ["max", "mean"]
      style:
        stroke: "rgba(0, 0, 0, 0.45)"
      text:
        content: "Average 平均值"
        offsetY: -2
        style:
          textAlign: "left"
          fontSize: 10
          fill: "rgba(44, 53, 66, 0.45)"
          textBaseline: "bottom"
    - type: "line"
      start: ["min", 800]
      end: ["max", 800]
      style:
        stroke: "rgba(200, 0, 0, 0.55)"
      text:
        content: "Target 目标值"
        offsetY: -2
        style:
          textAlign: "left"
          fontSize: 10
          fill: "rgba(44, 53, 66, 0.45)"
          textBaseline: "bottom"
```
---

## 树形构架图
```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: OrganizationTreeGraph

#-----------------#
#- chart data    -#
#-----------------#
data:
  id: "root"
  label: "Root"
  children:
    - id: "c1"
      label: "C1"
      children:
        - id: "c1-1"
          label: "C1-1"
          children:
            - id: "c1-1-1"
              label: "C1-1-1"
            - id: "c1-1-2"
              label: "C1-1-2"
        - id: "c1-2"
          label: "C1-2"
          children:
            - id: "c1-2-1"
              label: "C1-2-1"
            - id: "c1-2-2"
              label: "C1-2-2"
    - id: "c2"
      label: "C2"
      children:
        - id: "c2-1"
          label: "C2-1"
          children:
            - id: "c2-1-1"
              label: "C2-1-1"

#-----------------#
#- chart options -#
#-----------------#
options: {}
```
---

假数据

```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: DualAxes

#-----------------#
#- chart data    -#
#-----------------#
data:
  -
    - time: "2019-03"
      value: 350
      count: 800
    - time: "2019-04"
      value: 900
      count: 600
    - time: "2019-05"
      value: 300
      count: 400
    - time: "2019-06"
      value: 450
      count: 380
    - time: "2019-07"
      value: 470
      count: 22
  -
    - time: "2019-03"
      value: 350
      count: 800
    - time: "2019-04"
      value: 900
      count: 600
    - time: "2019-05"
      value: 300
      count: 400
    - time: "2019-06"
      value: 450
      count: 380
    - time: "2019-07"
      value: 470
      count: 22

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: 'time'
  yField: ['value', 'count']
  yAxis:
    value:
      min: 0
      label:
        formatter:
          function formatter(val) {
            return ''.concat(val, '个');
          }
  geometryOptions:
    - geometry: 'column'
    - geometry: 'line'
      lineStyle:
        lineWidth: 2
```

---

- [u] [[#图表可视化展示|一键回到顶部]]