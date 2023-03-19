# 基础用法介绍
`list` 、`table` 、`task` 、`calendar`分别对应列表、表格、任务、日历； 

`from` 指的是从哪里获取数据，可以从 `#tag` 标签获取、 从 `folder` 文件夹获取、从 `[[link]]` 链接获取，或者从链接了 link 的文件获取 `outgoing([[link]])` ； 

`where` 指的是上边获取的数据，要符合怎样的规则，也就是筛选；

`sort` 指的是排序，默认升序。

一个标准的语法是这样的：

````
```dataview
list|table|task|calendar // 任选一个类型
from // 紧跟设置数据来源
where // 数据筛选
sort // 排序规则
group //分组规则
limit //显示条目
```
````


![[Pasted image 20220320130347.png]]

## 可操作的字段

![[Pasted image 20220320130404.png]]

### 页面-隐式字段

Dataview 会自动向每个页面添加大量元数据：

-   `file.name`：文件标题（字符串）。
-   `file.folder`：此文件所属文件夹的路径。
-   `file.path`：完整的文件路径（字符串）。
-   `file.link`：指向文件的链接（链接）。
-   `file.size`：文件的大小（以字节为单位）（数字）。
-   `file.ctime`：文件的创建日期（日期 + 时间）。
-   `file.cday`：文件的创建日期（只是一个日期）。
-   `file.mtime`：上次修改文件的日期（日期 + 时间）。
-   `file.mday`：上次修改文件的日期（只是一个日期）。
-   `file.tags`：注释中所有唯一标记的数组。子标记按每个级别细分，因此将作为 存储在数组中。`#Tag/1/A``[#Tag, #Tag/1, #Tag/1/A]`
-   `file.etags`：注释中所有显式标记的数组;与 不同，不包含子标记。`file.tags`
-   `file.inlinks`：指向此文件的所有传入链接的数组。
-   `file.outlinks`：此文件中的所有传出链接的数组。
-   `file.aliases`：笔记的所有别名的数组。
-   `file.tasks`：此文件中所有任务（即 ）的数组。`- [ ] blah blah blah`

如果文件的标题（形式或 ）内有日期，或者具有字段/内联字段，则它还具有以下属性：`yyyy-mm-dd``yyyymmdd``Date`

-   `file.day`：与文件关联的显式日期。


### 任务-隐式字段

与页面一样，Dataview 会向每个任务添加许多隐式字段：

-   任务从其父页面继承_所有字段_ - 因此，如果您的页面中有一个字段，您也可以在任务中访问该字段。`rating`
-   `completed`：此_特定_任务是否已完成;这不考虑任何子任务的完成/未完成。
-   `fullyCompleted`：此任务及其**所有**子任务是否已完成。
-   `text`：此任务的文本。
-   `line`：此任务显示的行。
-   `path`：此任务所在的文件的完整路径。
-   `section`：指向包含此任务的部分的链接。
-   `link`：指向此任务附近最近的可链接块的链接;对于创建指向任务的链接很有用。
-   `subtasks`：此任务的任何子任务。
-   `real`：如果这是真的，这是一个真正的任务;否则，它是任务上方/下方的列表元素。
-   `completion`：任务完成的日期。如果未添加注释，则默认为文件修改时间。
-   `due`：任务到期的日期（如果有）。
-   `created`：任务的创建日期。如果未添加注释，则默认为文件创建时间。
-   `annotated`：如果任务具有任何自定义批注，则为 true，否则为 false。



# 显示形式 /查询类型
![[Pasted image 20220320130356.png]]
## 列表查询 list

语法

`LIST FROM <source>`

查询

`LIST FROM #games/mobas OR #games/crpg`

#### 没有 ID 的列表

如果不希望在列表视图中包含文件名/组键，可以使用：`LIST WITHOUT ID`

语法

`LIST WITHOUT ID <expression> FROM <source>` 

查询

`LIST WITHOUT ID file.path FROM "4. Archive"`






## 表查询 table

表支持页面数据的表格视图。您可以通过提供要呈现的 YAML 前端字段的逗号分隔列表来构造表，如下所示：

`TABLE file.day, file.mtime FROM <source>` 

您可以使用以下语法选择标题名称来呈现计算字段：`AS`

`TABLE (file.mtime + dur(1 day)) AS next_mtime, ... FROM <source>`


#### 不带 ID 的表

如果您不希望输出中使用默认的"文件"或"组"字段（替换它或因为它不需要），则可以使用：`TABLE WITHOUT ID`

查询

`TABLE WITHOUT ID
 time-played AS "Time Played",
 length AS "Length",
 rating AS "Rating"
FROM #game
SORT rating DESC`


## 任务查询 task

语法

`TASK FROM <source>` 

查询

`TASK FROM "dataview"`

## 日历查询 calendar

日历视图在日历视图中呈现与查询匹配的所有页面，使用给定的日期表达式选择要在其上呈现页面的日期。

语法

`CALENDAR <date>
FROM <source>` 

查询

`CALENDAR file.mtime
FROM "dataview"`

任务视图呈现其页面与给定谓词匹配的所有任务。

语法

`TASK FROM <source>` 

查询

`TASK FROM "dataview"`




# 数据筛选
## 检索范围 from

这目前意味着按文件夹，按标签或传入/传出链接。`FROM`

1.  **标签**： 表格的来源 .`#tag`
2.  **文件夹**：表单的来源 。`"folder"`
3.  **特定文件**：指定文件路径：。`"folder/File"`
    -   如果有路径完全相同的文件和文件夹，则 Dataview 优先选择该文件夹。可以指定文件：`folder/File.md`
4.  **链接**：您可以选择指向文件的链接，也可以选择文件中的所有链接。
    -   要获取链接到的所有页面，请使用 。`[[note]]``[[note]]`
    -   要获取该文件中的所有链接，请使用 。`[[note]]``outgoing([[note]])`
    -   可以通过 或 隐式引用当前文件。`[[#]]``[[]]`

高级筛选 (and or !)与或非

-   例如，`#tag and "folder"``folder``#tag`
-   从 中查询将仅返回包含但不包含 的页面。`#food and !#fastfood``#food``#fastfood`
-   `[[Food]] or [[Exercise]]`将给出任何链接到OR的页面。`[[Food]]``[[Exercise]]`

排除指定标签和文件夹：`-`
-   `-#tag`将排除具有给定标记的文件。
-   `#tag and -"folder"`将仅包含标记的文件，这些文件不在 中。`#tag``"folder"`

## 聚合条件 where

筛选字段上的页面。只有子句的计算结果为的页面才会生成。`true`

`WHERE <clause>` 

1.  获取过去 24 小时内修改的所有文件：
    
    `LIST WHERE file.mtime >= date(today) - dur(1 day)` 
    
2.  查找所有未标记为已完成且已超过一个月的项目：
    
    `LIST FROM #projects
    WHERE !completed AND file.ctime <= date(today) - dur(1 month)`

## 排序属性 sort 

![[Pasted image 20220320130656.png]]

按一个或多个字段对所有结果进行排序。

`SORT date [ASCENDING/DESCENDING/ASC/DESC]` 

您还可以提供多个字段作为排序依据。排序将基于第一个字段完成。然后，如果发生平局，则第二个字段将用于对并列字段进行排序。如果仍有平局，则第三种将解决它，依此类推。

`SORT field1 [ASCENDING/DESCENDING/ASC/DESC], ..., fieldN [ASC/DESC]`

## 分组依据 group

对字段上的所有结果进行分组。每个唯一字段值生成一行，该值具有 2 个属性：一个对应于要分组的字段，另一个数组字段包含所有匹配的页面。`rows`

`GROUP BY field
GROUP BY (computed_field) AS name` 

为了使数组的使用更容易，Dataview 支持字段"旋转"。如果您希望从数组中的每个对象获取字段，则将自动从 中的每个对象获取该字段，从而生成一个新数组。然后，可以像对生成的数组一样应用聚合运算符。`rows``test``rows``rows.test``test``rows``sum()`

## 扁平化 flatten

在每行中展平数组，在数组中的每个条目生成一个结果行。

`FLATTEN field
FLATTEN (computed_field) AS name` 

例如，将每个文献注释中的字段拼合为每位作者一行：`authors`

查询

`TABLE authors FROM #LiteratureNote
FLATTEN authors`

## 显示条数 limit
![[Pasted image 20220320130753.png]]

将结果限制为最多 N 个值。

`LIMIT 5` 

命令按照其写入顺序进行处理，因此以下命令在限制_后_对结果进行排序：

`LIMIT 5
SORT date ASCENDING`
