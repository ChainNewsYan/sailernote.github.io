---
layout: post
title: "re - Regular Compression #2"
date: 2017-09-15
---

# re - Regular Conpression #2

### Constraining the Search

### 限制搜索

如果希望匹配的字符是以某字符串开头的，比如搜索"abc"在文本中，如果文本是“cbc”，那么在比较第一个字符不符合就不需要继续比较了，如果文本是“acc”，那么在第二个字符就不需要比较了。

函数match()，提供了这样的功能。

```python
# re_match.py
import re

text = 'This is some text -- with punctuation.'
pattern = 'is'

print('Text   :', text)
print('Pattern:', pattern)

m = re.match(pattern, text)
print('Match  :', m)
s = re.search(pattern, text)
print('Search :', s)
```

因为文本不是以“is”开头，所以`match()`返回`None`而`search()`返回了结果

```shell
$ python3 re_match.py

Text   : This is some text -- with punctuation.
Pattern: is
Match  : None
Search : <_sre.SRE_Match object; span=(2, 4), match='is'>
```

函数`fullmatch()`要求整个个文本和模式匹配。

```Python
# re_fullmatch.py
import re

text = 'This is some text -- with punctuation.'
pattern = 'is'

print('Text       :', text)
print('Pattern    :', pattern)

m = re.search(pattern, text)
print('Search     :', m)
s = re.fullmatch(pattern, text)
print('Full match :', s)
```

`search()`可以搜索到匹配结果，但是匹配模式并没有消耗整个文本作为匹配结果所以`fullmatch()`返回`None`

```shell
$ python3 re_fullmatch.py

Text       : This is some text -- with punctuation.
Pattern    : is
Search     : <_sre.SRE_Match object; span=(2, 4), match='is'>
Full match : None
```

在一些情况下，是不需要对整个文本进行搜索的，通过编译表达式，确定搜索的起止位置

```Python
#  re_search_substring.py
import re

text = 'This is some text -- with punctuation.'
pattern = re.compile(r'\b\w*is\w*\b')

print('Text:', text)
print()

pos = 0
while True:
    match = pattern.search(text, pos)
    if not match:
        break
    s = match.start()
    e = match.end()
    print('  {:>2d} : {:>2d} = "{}"'.format(
        s, e - 1, text[s:e]))
    # Move forward in text for the next search
    pos = e
```

```shell
$ python3 re_search_substring.py

Text: This is some text -- with punctuation.

   0 :  3 = "This"
   5 :  6 = "is"
```

### Dissecting Matches with Groups

### 用组解析匹配

正则表达式嵌套

```Python
# re_groups.py
from re_test_patterns import test_patterns

test_patterns(
    'abbaaabbbbaaaaa',
    [('a(ab)', 'a followed by literal ab'),
     ('a(a*b*)', 'a followed by 0-n a and 0-n b'),
     ('a(ab)*', 'a followed by 0-n ab'),
     ('a(ab)+', 'a followed by 1-n ab')],
)
```

```shell
 python3 re_groups.py

'a(ab)' (a followed by literal ab)

  'abbaaabbbbaaaaa'
  ....'aab'

'a(a*b*)' (a followed by 0-n a and 0-n b)

  'abbaaabbbbaaaaa'
  'abb'
  ...'aaabbbb'
  ..........'aaaaa'

'a(ab)*' (a followed by 0-n ab)

  'abbaaabbbbaaaaa'
  'a'
  ...'a'
  ....'aab'
  ..........'a'
  ...........'a'
  ............'a'
  .............'a'
  ..............'a'

'a(ab)+' (a followed by 1-n ab)

  'abbaaabbbbaaaaa'
  ....'aab'
```

通过`groups()`可以按照序列的方式返回匹配到的字符串

```python
# re_groups_match.py
import re

text = 'This is some text -- with punctuation.'

print(text)
print()

patterns = [
    (r'^(\w+)', 'word at start of string'),
    (r'(\w+)\S*$', 'word at end, with optional punctuation'),
    (r'(\bt\w+)\W+(\w+)', 'word starting with t, another word'),
    (r'(\w+t)\b', 'word ending with t'),
]

for pattern, desc in patterns:
    regex = re.compile(pattern)
    match = regex.search(text)
    print("'{}' ({})\n".format(pattern, desc))
    print('  ', match.groups())
    print()
```

```shell
$ python3 re_groups_match.py

This is some text -- with punctuation.

'^(\w+)' (word at start of string)

   ('This',)

'(\w+)\S*$' (word at end, with optional punctuation)

   ('punctuation',)

'(\bt\w+)\W+(\w+)' (word starting with t, another word)

   ('text', 'with')

'(\w+t)\b' (word ending with t)

   ('text',)
```