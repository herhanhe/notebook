---
title: python-day2-python基础2
tags: 
notebook: pyhton
---

# Python基础2
---
<!-- TOC -->

- [Python基础2](#python基础2)
    - [1.列表、元组操作](#1列表元组操作)
        - [列表](#列表)
        - [元组](#元组)
    - [2.字符串的操作](#2字符串的操作)
    - [3.字典操作](#3字典操作)
    - [练习](#练习)

<!-- /TOC -->
## 1.列表、元组操作

### 列表
---

* 列表是我们最以后最常用的数据类型之一，通过列表可以对数据实现最方便的存储、修改等操作
* 定义列表
  
  ```python
    names = ['herhan',"Edison",'Sum']
  ```

* 通过下标访问列表中的元素，下标从0开始计数，还可以倒着取

  ```python
    >>> names[0]
    'herhan'
    >>> names[2]
    'Sum'
    >>> names[-1]
    'Sum'
    >>> names[-2]
    'Edison'
  ```

* **切片**：取多个元素

  ```python

    names = ["herhan","Amy","Tom","Jone","Rain","Jim"]

    names[1:4] # 取下标1至下标4之间的内容，包括1，不包括4
    # 输出
    ['Amy', 'Tom', 'Jone']

    names[1:-1] # 取下标1至-1的值，不包括-1
    # 输出
    ['Amy', 'Tom', 'Jone', 'Rain']

    names[0:3]
    # 输出
    ['herhan', 'Amy', 'Tom']

    names[:3] # 如果是从头开始取，0可以忽略，跟上句效果一样
    # 输出
    ['herhan', 'Amy', 'Tom']

    names[3:] # 如果想取最后一个，必须不能写-1，只能这么写
    #输出
    ['Jone', 'Rain', 'Jim']

    names[3:-1] # 这样-1就不会被包含了
    # 输出
    ['Jone', 'Rain']

    names[0::2] # 后面的2是代表，每隔一个元素，就取一个
    # 输出
    ['herhan', 'Tom', 'Rain']

    names[::2] # 和上句效果一样
    # 输出
    ['herhan', 'Tom', 'Rain']

  ```

* 追加（`append`）
  * `Append object to the end of the list`
  
  ```python
    names = ["herhan","Amy","Tom","Jone","Rain","Jim"]

    names.append("我是新来的")
    print(names)
    #输出
    ['herhan', 'Amy', 'Tom', 'Jone', 'Rain', 'Jim', '我是新来的']
  ```

* 插入（`insert`）
  * `Insert object before index.`

  ```python
    names = ['herhan', 'Amy', 'Tom', 'Jone', 'Rain', 'Jim', '我是新来的']

    names.insert(2,"强行从Tom前面插入")
    print(names)
    #输出
    ['herhan', 'Amy', '强行从Tom前面插入', 'Tom', 'Jone', 'Rain', 'Jim', '我是新来的']

    names.insert(4,"从tom后面插入")
    print(names)
    #输出

  ```

* 修改

  ```python
    names = ['herhan', 'Amy', '强行从Tom前面插入', 'Tom', 'Jone', 'Rain', 'Jim', '我是新来的']

    names[2] = "该换人了"
    #输出
    ['herhan', 'Amy', '该换人了', 'Tom', '从tom后面插入', 'Jone', 'Rain', 'Jim', '我是新来的']
  ```

* 删除（`del,remove,pop`）

  ```python
    names =  ['herhan', 'Amy', '该换人了', 'Tom', '从tom后面插入', 'Jone', 'Rain', 'Jim', '我是新来的']

    del names[2]
    print(names)
    # 输出
    ['herhan', 'Amy', 'Tom', '从tom后面插入', 'Jone', 'Rain', 'Jim', '我是新来的']

    del names[3]
    print(names)
    # 输出
    ['herhan', 'Amy', 'Tom', 'Jone', 'Rain', 'Jim', '我是新来的']

    names.remove('Tom') # 删除指定元素
    # Remove first occurrence of value.
    print(names)
    # 输出
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', '我是新来的']

    names.pop() #删除列表最后一个值
    print(names)
    # 输出
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim']
  ```

* 扩展（`extend`）
  * `Extend list by appending elements from the iterable.`

  ```python
    names = ['herhan', 'Amy', 'Jone', 'Rain', 'Jim']
    b = [1,2,3]
    names.extend(b)
    print(names)
    # 输出
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 1, 2, 3]
  ```

* 拷贝（`copy`）
  * `Return a shallow copy of the list.`

  ```python
    names = ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 1, 2, 3]
    names.append(['45','78'])
    name_copy = names.name_copy
    print(name_copy)
    #输出
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 1, 2, 3, ['45', '78']]

    names[5] = 10
    names[-1][0] = '56'
    print(name_copy)
    print(names)
    #输出 只copy了列表的第一层
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 10, 2, 3, ['56', '78']]
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 1, 2, 3, ['56', '78']]

    #深拷贝
    import copy
    names = ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 1, 2, 3, ['45', '78']]
    name_copy = copy.deepcopy(names)
    names[5] = 10
    names[-1][0] = '56'
    print(name_copy)
    print(names)
    #输出 
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 10, 2, 3, ['56', '78']]
    ['herhan', 'Amy', 'Jone', 'Rain', 'Jim', 1, 2, 3, ['45', '78']]
  ```

* 统计（`count`）
  * `Return number of occurrences of value.`

  ```python
    names = ['Alex', 'Tenglan', 'Amy', 'Tom', 'Amy', 1, 2, 3]
    names.count('Amy')
    #输出
    2
  ```

* 排列（`sort`）
  * Sort the list in ascending order and return None.

  * The sort is in-place (i.e. the list itself is modified) and stable (i.e. the order of two equal elements is maintained).
  
  * If a key function is given, apply it once to each list item and sort them, ascending or descending, according to their function values.

  ```python
    names = ['Alex', 'Tenglan', 'Amy', 'Tom', 'Amy', 1, 2, 3]
    names.sort() # 排序
    #输出
    TypeError: '<' not supported between instances of 'int' and 'str' # 3.0里不同数据类型不能放在一起排序了
    names[-1] = '3'
    names[-2] = '2'
    names[-3] = '1'

    names.sort() # 排序
    print(names)
    #输出
    ['1', '2', '3', 'Alex', 'Amy', 'Amy', 'Tenglan', 'Tom']
  ```

* 翻转（`reverse`）
  
  ```python
    names = ['1', '2', '3', 'Alex', 'Amy', 'Amy', 'Tenglan', 'Tom']

    names.reverse() #翻转
    print(names)
    #输出
    ['Tom', 'Tenglan', 'Amy', 'Amy', 'Alex', '3', '2', '1']
  ```

* 获取下标（`index`）
  * `Return first index of value. Raises ValueError if the value is not present.`
  
  ```python
    names = ['Tom', 'Tenglan', 'Amy', 'Amy', 'Alex', '3', '2', '1']
    print(names.index('Amy'))
    #输出 返回第一个找到的索引
    2
  ```

* 清空（`clear`）
  * `Remove all items from list.`
  
  ```python
    names = ['Tom', 'Tenglan', 'Amy', 'Amy', 'Alex', '3', '2', '1']
    names.clear() #清空
    print(names)
    # 输出
    []
  ```

* 列表长度（`len`）
  * `Return the number of items in a container`

  ```python
    names = ['Tom', 'Tenglan', 'Amy', 'Amy', 'Alex', '3', '2', '1']
    print(len(names))
    # 输出
    8
  ```

* 循环

  ```python
    names = ['Tom', 'Tenglan', 'Amy', 'Amy', 'Alex', '3', '2', '1']

    for i in names:
        print(i)

    #输出
    Tom
    Tenglan
    Amy
    Amy
    Alex
    3
    2
    1
  ```

### 元组

---

* 元组其实跟列表差不多，也是存一组数，只不是它一旦创建，便不能再修改，所以又叫只读列表
* 语法

  ```python
    names = ('herhan',"Edison",'Sum')
  ```

* 统计（`count`）

  ```python
    names = ('herhan',"Edison",'Sum','Sum')
    print(names.count('Sum'))
    # 输出
    2
  ```

* 获取下标（`index`）

  ```python
    names = ('herhan',"Edison",'Sum','Sum')
    print(names.index('Sum'))
    # 输出
    2
  ```

## 2.字符串的操作

* 特性不可修改

```python
  name = 'herhan'
  
  name = name.capitalize()  # 首字母大写
  # Return a capitalized version of the string.
  # More specifically, make the first character have upper case and the rest lower case.
  print(name)
  # 输出
  Herhan
  
  name = name.casefold()   # 大写全部变小写
  # Return a version of the string suitable for caseless comparisons.
  print(name)
  # 输出
  herhan

  print(name.center(10,"*")) # 输出 **herhan**
  # Return a centered string of length width.
  # Padding is done using the specified fill character (default is a space).
  # 输出
  **herhan**

  print(name.count('h')) # 统计 h 出现的次数
  print(name.count('h',1,-1))
  # S.count(sub[, start[, end]]) -> int
  # Return the number of non-overlapping occurrences of substring sub in string S[start:end].  Optional arguments start and end are interpreted as in slice notation.
  # 输出
  2
  1

  print(name.encode()) # 将字符串编码成bytes格式
  # Encode the string using the codec registered for encoding.
  '''
  encoding
    The encoding in which to encode the string.
  errors
    The error handling scheme to use for encoding errors.
    The default is 'strict' meaning that encoding errors raise a UnicodeEncodeError.  Other possible values are 'ignore', 'replace' and 'xmlcharrefreplace' as well as any other name registered with codecs.register_error that can handle UnicodeEncodeErrors.
  '''
  # 输出
  b'herhan'

  print(name.endswith("an"))  # 判断字符串是否以 an结尾
  '''
  S.endswith(suffix[, start[, end]]) -> bool
  Return True if S ends with the specified suffix, False otherwise.
  With optional start, test S beginning at that position.
  With optional end, stop comparing S at that position.
  suffix can also be a tuple of strings to try.
  '''
  # 输出
  True

  print("herhan\the".expandtabs(10)) # 将\t转换成多长的空格
  # Return a copy where all tab characters are expanded using spaces.
  # If tabsize is not given, a tab size of 8 characters is assumed.
  # 输出
  herhan    he
  
  print(name.find('a')) # 查找a,找到返回其索引， 找不到返回-1
  # S.find(sub[, start[, end]]) -> int
  # Return the lowest index in S where substring sub is found,such that sub is contained within S[start:end]. Optional arguments start and end are interpreted as in slice notation.
  # Return -1 on failure.
  #输出
  4

  # format
  # Return a formatted version of S, using substitutions from args and kwargs. The substitutions are identified by braces ('{' and '}').
  msg = "my name is {}, and age in {}"
  print(msg.format("herhan",22))
  # 输出
  'my name is herhan, and age in 22'

  msg = "my name is {1}, and age in {0}"
  print(msg.format("herhan",22))
  # 输出
  'my name is 22, and age in herhan'

  msg = "my name is {name}, and age in {age}"
  print(msg.format(age=22,name="herhan"))
  # 输出
  'my name is herhan, and age in 22'

  # format_map
  # S.format_map(mapping) -> str
  # Return a formatted version of S, using substitutions from mapping. The substitutions are identified by braces ('{' and '}').
  msg = "my name is {name}, and age in {age}"
  print(msg.format_map({'name':'herhan','age':22}))
  # 输出
  'my name is herhan, and age in 22'

  print(name.index('h')) # 返回a所在字符串的索引
  # 输出
  0

  print(name.isalnum())
  # Return True if the string is an alpha-numeric string, False otherwise.
  # A string is alpha-numeric if all characters in the string are alpha-numeric and there is at least one character in the string.
  # 输出
  True

  print(name.isdigit())
  print('1'.isdigit())
  # Return True if the string is a digit string, False otherwise.
  # A string is a digit string if all characters in the string are digits and there is at least one character in the string.
  # 输出
  False
  True

  print(name.isnumeric())
  # Return True if the string is a numeric string, False otherwise.
  # A string is numeric if all characters in the string are numeric and there is at least one character in the string.
  # 输出
  False

  print(name.isprintable())
  # Return True if the string is printable, False otherwise.
  # A string is printable if all of its characters are considered printable in repr() or if it is empty.
  # 输出
  True

  print(name.isspace())
  # Return True if the string is a whitespace string, False otherwise.
  # A string is whitespace if all characters in the string are whitespace and there is at least one character in the string.
  # 输出
  False

  print(name.istitle())
  # Return True if the string is a title-cased string, False otherwise.
  # In a title-cased string, upper- and title-case characters may only follow uncased characters and lowercase characters only cased ones.
  # 输出
  False

  print(name.isupper())
  # Return True if the string is an uppercase string, False otherwise.
  # A string is uppercase if all cased characters in the string are uppercase and there is at least one cased character in the string.
  # 输出
  False

  print("|".join(['herhan',"Edison",'Sum']))
  # Concatenate any number of strings.
  # The string whose method is called is inserted in between each given string. The result is returned as a new string.
  # Example: '.'.join(['ab', 'pq', 'rs']) -> 'ab.pq.rs'
  # 输出
  'herhan|Edison|Sum'

  # maketrans & translate
  intab = "aeiou" # This is the string having actual characters.
  outtab = "12345" # This is the string having corresponding mapping character
  trantab = str.maketrans(intab, outtab)
  print(trantab)
  # Return a translation table usable for str.translate().
  # If there is only one argument, it must be a dictionary mapping Unicode ordinals (integers) or characters to Unicode ordinals, strings or None. Character keys will be then converted to ordinals.If there are two arguments, they must be strings of equal length, and in the resulting dictionary, each character in x will be mapped to the character at the same position in y. If there is a third argument, it must be a string, whose characters will be mapped to None in the result.
  # 输出
  {97: 49, 101: 50, 105: 51, 111: 52, 117: 53}

  str = "this is string example....wow!!!"
  print(str.translate(trantab))
  # Replace each character in the string using the given translation table.
  # table : Translation table, which must be a mapping of Unicode ordinals to Unicode ordinals, strings, or None.
  # 输出
  'th3s 3s str3ng 2x1mpl2....w4w!!!'

  print(msg.partition('is'))
  # Partition the string into three parts using the given separator.
  # This will search for the separator in the string.  If the separator is found, returns a 3-tuple containing the part before the separator, the separator itself, and the part after it.
  # If the separator is not found, returns a 3-tuple containing the original string and two empty strings.
  # 输出
  ('my name ', 'is', ' {name}, and age in {age}')

  msg = 'herhan , chinese name is han'
  print(msg.replace("ha","Ha",1))
  # Return a copy with all occurrences of substring old replaced by new.
  # count：Maximum number of occurrences to replace. -1 (the default value) means replace all occurrences.
  # If the optional argument count is given, only the first count occurrences are replaced.
  # 输出
  'herHan , chinese name is han'

  print(msg.swapcase())
  # Convert uppercase characters to lowercase and lowercase characters to uppercase.
  # 输出
  'HERHAN , CHINESE NAME IS HAN'

  print(msg.lower())
  # Return a copy of the string converted to lowercase.
  # 输出
  'herhan , chinese name is han'
  
  print(msg.upper())
  # Return a copy of the string converted to uppercase.
  # 输出
  'HERHAN , CHINESE NAME IS HAN'
  
  print(msg.zfill(40))
  # Pad a numeric string with zeros on the left, to fill a field of the given width.
  # The string is never truncated.
  # 输出
  '000000000000herhan , chinese name is han'

  print(name.ljust(40,"-"))
  # Return a left-justified string of length width.
  # Padding is done using the specified fill character (default is a space).
  print(name.rjust(40,'-'))
  # Return a right-justified string of length width.
  # Padding is done using the specified fill character (default is a space).
  # 输出
  'herhan----------------------------------'
  '----------------------------------herhan'

  print(name.isidentifier()) #检测一段字符串可否被当作标志符，即是否符合变量命名规则
  # Return True if the string is a valid Python identifier, False otherwise.
  # Call keyword.iskeyword(s) to test whether string s is a reserved identifier, such as "def" or "class".
  # 输出
  True

  print('\nHerhan'.lstrip())
  # Return a copy of the string with leading whitespace removed.
  # If chars is given and not None, remove characters in chars instead.
  print('Herhan\n'.rstrip())
  # Return a copy of the string with trailing whitespace removed.
  # If chars is given and not None, remove characters in chars instead.
  print('    Herhan\n'.strip())
  # Return a copy of the string with leading and trailing whitespace removed.
  # If chars is given and not None, remove characters in chars instead.
  # 输出
  'Herhan'
  'Herhan'
  'Herhan'

  print(name.rfind('h'))
  # S.rfind(sub[, start[, end]]) -> int
  # Return the highest index in S where substring sub is found, such that sub is contained within S[start:end].  Optional arguments start and end are interpreted as in slice notation.
  # Return -1 on failure.
  # 输出
  3

  print('1+2+3+4'.split('+'))
  # Return a list of the words in the string, using sep as the delimiter string.
  # sep ：The delimiter according which to split the string. None (the default value) means split according to any whitespace, and discard empty strings from the result.
  # maxsplit ：Maximum number of splits to do. -1 (the default value) means no limit.
  # 输出
  ['1', '2', '3', '4']

  print('1+2\n3+4'.splitlines())
  # Return a list of the lines in the string, breaking at line boundaries.
  # Line breaks are not included in the resulting list unless keepends is given and true.
  # 输出
  ['1+2', '3+4']

  print('Herhan h'.swapcase())
  # Convert uppercase characters to lowercase and lowercase characters to uppercase
  # 输出
  'hERHAN H'

  print('herhan he'.title())
  # Return a version of the string where each word is titlecased.
  # More specifically, words start with uppercased characters and all remaining cased characters have lower case.
  # 输出
  'Herhan He'
```

## 3.字典操作

* 字典一种 `key - value` 的数据类型，使用就像我们上学用的字典，通过笔划、字母来查对应页的详细内容。

* 语法：

  ```python
    info = {
        'stu101': "herhan",
        'stu102': "Tom",
        'stu103': "Sam",
    }
  ```

* 字典的特性：
  * `dict`是无序的
  * `key`必须是唯一的，so天生去重

* 增加
  
  ```python
    info = {
        'stu101': "herhan",
        'stu102': "Tom",
        'stu103': "Sam",
    }
    info["stu104"] = "Tim"
    print(info)
    # 输出
    {'stu101': 'herhan', 'stu102': 'Tom', 'stu103': 'Sam', 'stu104': 'Tim'}
  ```

* 修改

  ```python
    info['stu101'] = "John"
    print(info)
    #输出
    {'stu101': 'John', 'stu102': 'Tom', 'stu103': 'Sam', 'stu104': 'Tim'}
  ```

* 删除

  ```python
    #pop
    info.pop('stu101') #标准删除姿势
    print(info)
    #输出
    {'stu102': 'Tom', 'stu103': 'Sam', 'stu104': 'Tim'}

    #del
    del info['stu104'] #换个姿势删除
    print(info)
    #输出
    {'stu102': 'Tom', 'stu103': 'Sam'}

    #popitem
    info.popitem() #随机删除
    print(info)
    # 输出
    {'stu102': 'Tom'}
  ```

* 查找

  ```python
    info = {
          'stu101': "herhan",
          'stu102': "Tom",
          'stu103': "Sam",
      }
    print("stu1102" in info) #标准用法
    # 输出
    True

    print(info.get("stu102")) #获取
    # 输出
    'Tom'

    print(info["stu1102"]) #同上，但是看下面
    # 输出
    'Tom'

    print(info["stu1105"])  #如果一个key不存在，就报错，get不会，不存在只返回None
    # 输出
    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    KeyError: 'stu1105
  ```

* 多级字典嵌套及操作
  
  ```python
    city = {
      '书籍':{
          'python书籍':{
              'python基础':{'python的基础书籍'},
              'python进阶':{'python进阶用的'},
              'python进阶到放弃':{'python开始放弃'}
          },
          '美食书籍':{
              '炒菜指南':{'教你炒菜咯'},
              '美食菜谱':{'看看有什么美食'},
              '菜品介绍':{'关于菜品的介绍以及由来'}
          }
      },
      '饮料':{
          '能量饮料':{
              '红牛':{'喝了一晚上不睡觉'},
              '战马':{'喝了奋战到天明'},
              '魔爪':{'喝了提神呦'}
          },
          '普通饮料':{
              '矿泉水':{'就普通的纯净水'},
              '冰红茶':{'这是一瓶红茶'},
              '乌龙茶':{'这是一瓶乌龙茶'}
          }
      },
      '汽车':{
          '丰田':{
              'CROWN皇冠':{'皇冠车'},
              'COROLLA卡罗拉':{'卡罗拉呦'},
              'COROLLA花冠':{'花冠经典车'}
          },
          '劳斯莱斯':{
              '银色精灵':{'别看了，买不起'},
              'ParkWard':{'豪华座驾'},
              '100EX':{'CEO专用'}
          }
      }
    }


    city["书籍"]["python书籍"]["python基础"] = "python的基"
    print(city["书籍"]["python书籍"])
    # 输出
    {'python基础': 'python的基', 'python进阶': {'python进阶用的'}, 'python进阶到放弃': {'python开始放弃'}}
  ```

* 其他功能

  ```python
    info = {
          'stu101': "herhan",
          'stu102': "Tom",
          'stu103': "Sam",
    }

    #valuses
    print(info.values())
    # D.values() -> an object providing a view on D's values
    # 输出
    dict_values(['John', 'Tom', 'Sam', 'Tim'])

    #keys
    print(info.keys())
    # D.keys() -> a set-like object providing a view on D's keys
    # 输出
    dict_keys(['stu101', 'stu102', 'stu103', 'stu104'])

    #setdefault
    # Insert key with a value of default if key is not in the dictionary.
    # Return the value for key if key is in the dictionary, else default.
    info.setdefault("stu1106","Alex")
    print(info)
    #输出
    {'stu101': 'John', 'stu102': 'Tom', 'stu103': 'Sam', 'stu104': 'Tim', 'stu1106': 'Alex'}

    info.setdefault("stu1102","龙泽萝拉")
    print(info)
    #输出
    {'stu101': 'John', 'stu102': 'Tom', 'stu103': 'Sam', 'stu104': 'Tim', 'stu106': 'Alex'}

    #update
    b = {1:2,3:4, "stu1102":"龙泽萝拉"}
    info.update(b)
    print(info)
    #输出
    {'stu101': 'John', 'stu102': '龙泽萝拉', 'stu103': 'Sam', 'stu104': 'Tim', 'stu106': 'Alex', 1: 2, 3: 4}

    #items
    # D.items() -> a set-like object providing a view on D's items
    print(info.items())
    #输出
    dict_items([('stu101', 'John'), ('stu102', '龙泽萝拉'), ('stu103', 'Sam'), ('stu104', 'Tim'), ('stu106', 'Alex'), (1, 2), (3, 4)])

    #通过一个列表生成默认dict,有个没办法解释的坑，少用吧这个
    # fromkeys
    # Create a new dictionary with keys from iterable and values set to value.
    print(dict.fromkeys([1,2,3],'testd'))
    #输出
    {1: 'testd', 2: 'testd', 3: 'testd'}
  ```

  * 循环

  ```python
    #方法1
    for key in info:
        print(key,info[key])

    #方法2
    for k,v in info.items(): #会先把dict转成list,数据里大时莫用
        print(k,v)
  ```

## 练习

* 程序：购物车程序
  
  > 需求:
  > 1.启动程序后，让用户输入工资，然后打印商品列表
  > 2.允许用户根据商品编号购买商品
  > 3.用户选择商品后，检测余额是否够，够就直接扣款，不够就提醒
  > 4.可随时退出，退出时，打印已购买商品和余额
  > 5.用户入口：1.商品信息存在文件里  2.已购商品，余额记录。
  > 6.商家入口: 1.可以添加商品，修改商品价格

  * 参考：[shopping.py](https://github.com/herhanhe/s14/tree/master/day02/shopping.py)

* 程序: 三级菜单

  > 需求:
  > 1.打印省、市、县三级菜单
  > 2.可返回上一级
  > 3.可随时退出程序

  * 参考：[level_3_menu.py](https://github.com/herhanhe/s14/tree/master/day02/level_3_menu.py)