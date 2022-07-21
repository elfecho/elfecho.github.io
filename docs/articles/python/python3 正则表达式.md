# 正则表达式

正则表达式是一个特殊的字符序列，它能帮助你方便的检查一个字符串是否与某种模式匹配

快速检索文本、实现一些替换文本的操作
1. 检查一串数字是否是电话号码
2. 检测一个字符串是否符合emai1
3. 把一个文本里指定的单词替换为另外一个单词


## re.findall函数

在字符串中找到正则表达式所匹配的所有子串，并返回一个列表，如果有多个匹配模式，则返回元组列表，如果没有找到匹配的，则返回空列表。

**注意：** match 和 search 是匹配一次 findall 匹配所有。

语法格式为：

```python
re.findall(pattern, string, flags=0)
# 或
pattern.findall(string[, pos[, endpos]])
```
参数：
-   pattern 匹配模式。
-   string 待匹配的字符串。
-   pos 可选参数，指定字符串的起始位置，默认为 0。
-   endpos 可选参数，指定字符串的结束位置，默认为字符串的长度。
`

```python
import re 
result1 = re.findall(r'\d+','runoob 123 google 456') 
pattern = re.compile(r'\d+') # 查找数字 
result2 = pattern.findall('runoob 123 google 456') 
result3 = pattern.findall('run88oob123google456', 0, 10) 

print(result1) 
print(result2) 
print(result3)

'''
运行
['123', '456']
['123', '456']
['88', '12']
'''
```