---
cssClasses: cards, cards-align-bottom,  cards-cover, cards-2-3
---

# 卡片语法
```
---
cssClasses: cards
---
```
| 类 | 描述 |
| ---- | ---- |
| cards | （必填）	将所有数据视图表设置为卡片布局 |
| cards-align-bottom | 将卡片的最后一个元素对齐到底部 |
| cards-cover  |    调整图像大小以填充定义的空间  |
| cards-16-9  |   将卡片中的图像调整为 16：9 比例   |
| cards-1-1  |   将卡片中的图像拟合为 1：1 比例（正方形）   |
| cards-2-1  |   将卡片中的图像拟合到 2：1 的比例   |
| cards-2-3  |    将卡片中的图像拟合到 2：3 的比例  |
| cards-cols-1自8 |   强制特定数量的列（从 1 到 8）   |

# 线宽设置
可以使用"最小主题设置"设置设置以下线宽首选项：

正常行宽：正常文本行的宽度（默认为 40em）
宽线宽："宽"元素的宽度（默认为 50em）
最大行宽：内容在窗格内可以填充的最大宽度百分比（默认为 88%）

## 帮助程序类
以下 Helper 类可用于强制特定注释中块的宽度：

| 类 | 描述 |
| ---- | ---- |
| table-100, ,img-100iframe-100 | 填充窗格宽度的 100% |
| table-max, ,img-maxiframe-max | 填充最大线宽 |
| table-wide, ,img-wideiframe-wide | 填充宽线宽 |
	
	table-wide,table-max
	