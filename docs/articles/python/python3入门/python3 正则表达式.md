正则表达式是一个特殊的字符序列，它能帮助你方便的检查一个字符串是否与某种模式匹配

快速检索文本、实现一些替换文本的操作
1. 检查一串数字是否是电话号码
2. 检测一个字符串是否符合emai1
3. 把一个文本里指定的单词替换为另外一个单词


## re.findall 函数

在字符串中找到正则表达式所匹配的所有子串，并返回一个列表，如果有多个匹配模式，则返回元组列表，如果没有找到匹配的，则返回空列表。

**注意：** match 和 search 是匹配一次 findall 匹配所有。

语法格式为：

```python
re.findall(pattern, string, flags=0)
# 或
pattern.findall(string[, pos[, endpos]])
```
参数：
- flags  可选，表示匹配模式，比如忽略大小写，多行模式等，具体参数为：[正则表达式修饰符 - 可选标志](python3%20正则表达式.md#正则表达式修饰符-可选标志)
  - re.X 为了增加可读性，忽略空格和' # '后面的注释
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

### 字符集

`[]` 进行给字符定界，表示或的关系

```python
import re

s = 'abc, acc, adc, aec, afc, ahc'

r = re.findall('a[c-f]c', s)  # 借助普通字符进行定界
print(r)

'''
运行结果
['acc', 'adc', 'aec', 'afc']
'''
```


### 概括字符集

```python
'''
\d \D
\w 单词字符 \W
\s 空白字符 \S
. 匹配除换行符\n之外其他所有字符
'''

import re
a = 'python java&__6\r66\n66'

r = re.findall('\W', a) # 非单词字符
r1 = re.findall('[A-Za-z0-9_]', a)
r2 = re.findall('\s', a)
r3 = re.findall('.', a)
print(r)
print(r1)
print(r2)
print(r3)
'''
运行结果
[' ', '&', '\r', '\n']
['p', 'y', 't', 'h', 'o', 'n', 'j', 'a', 'v', 'a', '_', '_', '6', '6', '6', '6', '6']
[' ', '\r', '\n']
['p', 'y', 't', 'h', 'o', 'n', ' ', 'j', 'a', 'v', 'a', '&', '_', '_', '6', '\r', '6', '6', '6', '6']
'''
```

### 贪婪 与 非贪婪


`{}`  里面可以编写数量词，表示区间的话用逗号 `,` 隔开

```python
import re
a = 'python 11111java6666php'

r = re.findall('[a-z]{3,6}', a) # 这种写法属于贪婪模式，匹配3个后如果后面还符合规则就继续匹配
r1 = re.findall('[a-z]{3,6}?', a) # 这种写法属于非贪婪模式，匹配3个后就直接返回出来，继续匹配
print(r)
print(r1)
'''
运行结果
['python', 'java', 'php']
['pyt', 'hon', 'jav', 'php']
'''
```

贪婪模式：在包含`[a-z]{m,n}`的正则表达式中，当`[a-z]`中的字符匹配m次后，继续匹配至n次。

非贪婪模式：在包含`[a-z]{m,n}?`的正则表达式中，当`[a-z]`中的字符c匹配m次后，结束匹配。因此`[a-z]{m,n}?`相当于`[a-z]{m}`


### 匹配0次1次或无限次

- `* 匹配0次或无限次`
- `+ 匹配1次或无限次`
- `? 匹配0次或1次`

```python
import re

a = 'pytho0python1pythonnnnnn2'

r1 = re.findall('python*', a)
r2 = re.findall('python{1,2}?', a)
r3 = re.findall('python?', a)

print('*', r1)
print('+', r2)
print('?', r3)

'''
运行结果
* ['pytho', 'python', 'pythonnnnnn']
+ ['python', 'python']
? ['pytho', 'python', 'python']
'''
```

### 边界匹配

- `^` 起始位置，`$` 末尾位置

```python
import re
qq = '100000001'

# 需要匹配出 4 到 8位的qq号

r = re.findall('^\d{4,8}$', qq)
print(r)
'''
运行结果
[]
'''
```

### 组

- `[]` 或关系
- `()` 且关系

```python
import re
a = 'PythonPythonPythonPythonPython'

r = re.findall('(Python){3}', a) # 有同时出现3次的Python
r1 = re.findall('(Python){2}', a) # 有同时出现2次的Python
r2 = re.findall('(Python)*', a) # 有同时出现0次的Python或无限次数的Python
r3 = re.findall('(Python)+', a) # 有同时出现1次的Python或无限次数的Python
print(r)
print(r1)
print(r2)
print(r3)
'''
运行结果
['Python']
['Python', 'Python']
['Python', '']
['Python']
'''
```

### 匹配模式参数

- re.I 不区分大小写
- re.S 可以匹配换行符
- 多个匹配规则使用 `|` 进行分割

```python
import re
language = 'PythonC#\nJavaPHP'

r = re.findall('c#.{1}', language, re.I | re.S)
print(r)

'''
运行结果
['C#\n']
'''
```

## re.sub 正则替换

Python 的re模块提供了re.sub用于替换字符串中的匹配项。

语法：

```python
re.sub(pattern, repl, string, count=0, flags=0)
```

参数：

-   pattern : 正则中的模式字符串。
-   repl : 替换的字符串，也可为一个函数。
-   string : 要被查找替换的原始字符串。
-   count : 模式匹配后替换的最大次数，默认 0 表示替换所有的匹配。
-   flags : 编译时用的匹配模式，数字形式。

```python
import re
language = 'PythonC#JavaC#PHPC#'

def convert(value):
    matched = value.group()
    return '!!' + matched + '!!'

r = re.sub('C#', 'GO', language)
r1 = re.sub('C#', convert, language)
# language = language.replace('C#', 'GO')  # python 提供的方法

print(r)
print(r1)

'''
运行结果
PythonGOJavaGOPHPGO
Python!!C#!!Java!!C#!!PHP!!C#!!
'''
```

示例：

```python
import re
s = 'A87C22B34D93'

# 字符串里面数字大于50的数替换为9，小于50的替换为0
def convert(value):
    matched = value.group()
    if int(matched) >= 50:
        return '9'
    else:
        return '0'


r = re.sub(r'\d+', convert, s)
print(r)
'''
运行结果
A9C0B0D9
'''
```

## re.match 与 re.search方法

re.match 尝试从字符串的起始位置匹配一个模式，如果不是起始位置匹配成功的话，match()就返回none。

**函数语法**：
```python
re.match(pattern, string, flags=0)
```

re.search 扫描整个字符串并返回第一个成功的匹配。

**函数语法**：
```python
re.search(pattern, string, flags=0)
```

函数参数说明：

| 参数    | 描述                                                         |
| :------ | :----------------------------------------------------------- |
| pattern | 匹配的正则表达式                                             |
| string  | 要匹配的字符串。                                             |
| flags   | 标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等。参见：[正则表达式修饰符 - 可选标志](python3%20正则表达式.md#正则表达式修饰符-可选标志s) |

匹配成功re.match方法返回一个匹配的对象，否则返回None。

我们可以使用group(num) 或 groups() 匹配对象函数来获取匹配表达式。

| 匹配对象方法 | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| group(num=0) | 匹配的整个表达式的字符串，group() 可以一次输入多个组号，在这种情况下它将返回一个包含那些组所对应值的元组。 |
| groups()     | 返回一个包含所有小组字符串的元组，从 1 到 所含的小组号。     |


### re.match与re.search的区别

re.match 只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回 None，而 re.search 匹配整个字符串，直到找到一个

```python
import re

s = '87C22B34D93'

r = re.match('\d', s)
print(r.span())  # 在起始位置匹配
print(r)         # 不在起始位置匹配

r1 = re.search('\d', s)
print(r1.group())


'''
运行结果
(0, 1)
<_sre.SRE_Match object; span=(0, 1), match='8'>
8
'''
```

**group用法**

```python
import re

s = 'life is short,i use python'
r = re.search('life(.*)python', s)

print(r.group(0))  # 为0时返回整个匹配的字符串
print(r.group(1))

'''
运行结果
life is short,i use python
 is short,i use 
'''
```

**groups用法**

```python
import re

s = 'life is short,i use python, i love python'

r = re.search('life(.*)python(.*)python', s)
print(r.groups())

print(r.group(0))
print(r.group(1))
print(r.group(2))
print(r.group(0, 1, 2))

'''
运行结果
(' is short,i use ', ',i love ')
life is short,i use python,i love python
 is short,i use 
,i love
('life is short,i use python,i love python', ' is short,i use ', ',i love ')
'''
```




## 正则表达式修饰符 - 可选标志

正则表达式可以包含一些可选标志修饰符来控制匹配的模式。修饰符被指定为一个可选的标志。多个标志可以通过按位 OR(|) 它们来指定。如 re.I | re.M 被设置成 I 和 M 标志：

| 修饰符 | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| re.I   | 使匹配对大小写不敏感                                         |
| re.L   | 做本地化识别（locale-aware）匹配                             |
| re.M   | 多行匹配，影响 ^ 和 $                                        |
| re.S   | 使 . 匹配包括换行在内的所有字符                              |
| re.U   | 根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.      |
| re.X   | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。 |



## 正则表达式模式

模式字符串使用特殊的语法来表示一个正则表达式。

字母和数字表示他们自身。一个正则表达式模式中的字母和数字匹配同样的字符串。

多数字母和数字前加一个反斜杠时会拥有不同的含义。

标点符号只有被转义时才匹配自身，否则它们表示特殊的含义。

反斜杠本身需要使用反斜杠转义。

由于正则表达式通常都包含反斜杠，所以你最好使用原始字符串来表示它们。模式元素(如 r'\t'，等价于 \\t )匹配相应的特殊字符。

下表列出了正则表达式模式语法中的特殊元素。如果你使用模式的同时提供了可选的标志参数，某些模式元素的含义会改变。



| 模式         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| ^            | 匹配字符串的开头                                             |
| $            | 匹配字符串的末尾。                                           |
| .            | 匹配任意字符，除了换行符，当re.DOTALL标记被指定时，则可以匹配包括换行符的任意字符。 |
| [...]        | 用来表示一组字符,单独列出：[amk] 匹配 'a'，'m'或'k'          |
| [^...]       | 不在[]中的字符：[^abc] 匹配除了a,b,c之外的字符。             |
| re*          | 匹配0个或多个的表达式。                                      |
| re+          | 匹配1个或多个的表达式。                                      |
| re?          | 匹配0个或1个由前面的正则表达式定义的片段，非贪婪方式         |
| re{ n}       | 匹配n个前面表达式。例如，"o{2}"不能匹配"Bob"中的"o"，但是能匹配"food"中的两个o。 |
| re{ n,}      | 精确匹配n个前面表达式。例如，"o{2,}"不能匹配"Bob"中的"o"，但能匹配"foooood"中的所有o。"o{1,}"等价于"o+"。"o{0,}"则等价于"o*"。 |
| re{ n, m}    | 匹配 n 到 m 次由前面的正则表达式定义的片段，贪婪方式         |
| a\| b        | 匹配a或b                                                     |
| (re)         | 匹配括号内的表达式，也表示一个组                             |
| (?imx)       | 正则表达式包含三种可选标志：i, m, 或 x 。只影响括号中的区域。 |
| (?-imx)      | 正则表达式关闭 i, m, 或 x 可选标志。只影响括号中的区域。     |
| (?: re)      | 类似 (...), 但是不表示一个组                                 |
| (?imx: re)   | 在括号中使用i, m, 或 x 可选标志                              |
| (?-imx: re)  | 在括号中不使用i, m, 或 x 可选标志                            |
| (?#...)      | 注释.                                                        |
| (?= re)      | 前向肯定界定符。如果所含正则表达式，以 ... 表示，在当前位置成功匹配时成功，否则失败。但一旦所含表达式已经尝试，匹配引擎根本没有提高；模式的剩余部分还要尝试界定符的右边。 |
| (?! re)      | 前向否定界定符。与肯定界定符相反；当所含表达式不能在字符串当前位置匹配时成功。 |
| (?> re)      | 匹配的独立模式，省去回溯。                                   |
| \w           | 匹配数字字母下划线                                           |
| \W           | 匹配非数字字母下划线                                         |
| \s           | 匹配任意空白字符，等价于 [\t\n\r\f]。                        |
| \S           | 匹配任意非空字符                                             |
| \d           | 匹配任意数字，等价于 [0-9]。                                 |
| \D           | 匹配任意非数字                                               |
| \A           | 匹配字符串开始                                               |
| \Z           | 匹配字符串结束，如果是存在换行，只匹配到换行前的结束字符串。 |
| \z           | 匹配字符串结束                                               |
| \G           | 匹配最后匹配完成的位置。                                     |
| \b           | 匹配一个单词边界，也就是指单词和空格间的位置。例如， 'er\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。 |
| \B           | 匹配非单词边界。'er\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。 |
| \n, \t, 等。 | 匹配一个换行符。匹配一个制表符, 等                           |
| \1...\9      | 匹配第n个分组的内容。                                        |
| \10          | 匹配第n个分组的内容，如果它经匹配。否则指的是八进制字符码的表达式。 |