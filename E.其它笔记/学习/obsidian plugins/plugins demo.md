> [!multi-column]
> 
>> [!note|left-small] 最近新增
>> ```dataview
>> list from !"zz-Template" and !"yy-Memo"
>> where date(today) - file.ctime <=dur(7 days)
>> sort file.ctime desc
>> limit 6
>> ``` 
>
>> [!note|right-medium] 最近编辑
>> ```dataview
>> list from !"zz-Template" and !"yy-Memo"
>> sort file.mtime desc
>> limit 6
>> ```


## dataview

## callouts
###      symbol list
-   note
-   abstract, summary, tldr
-   info, todo
-   tip, hint, important
-   success, check, done
-   question, help, faq
-   warning, caution, attention
-   failure, fail, missing
-   danger, error
-   bug
-   example
-   quote, cite


> [!note] Callouts can have custom titles, note


> [!TIP] Callouts can have custom titles, which also supports **markdown**!

> [!abstract] Callouts can have custom titles, abstract

> [!todo] Callouts can have custom titles, todo

> [!success] Callouts can have custom titles, success

> [!question] Callouts can have custom titles, question

> [!warning] Callouts can have custom titles, warning

> [!failure] Callouts can have custom titles, failure

> [!danger] Callouts can have custom titles, danger

> [!bug] Callouts can have custom titles, bug

> [!example] Callouts can have custom titles, example

> [!quote] Callouts can have custom titles, quote

