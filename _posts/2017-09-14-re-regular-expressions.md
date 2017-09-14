---
layout: post
title: "re-Regular Expressions"
date: 2017-09-14 20:57:53
---
#re-Regular Expressions

#### Finding Patterns in Text

#### 在文本中寻找模式

```python
# re_simple_match.py
import re

pattern = 'this'
text = 'Does this text match the pattern?'

match = re.search(pattern, text)

s = match.start()
e = match.end()

print('Found "{}"\nin "{}"\nfrom {} to {} ("{}")'.format(
    match.re.pattern, match.string, s, e, text[s:e]))
```

```shell
$ python3 re_simple_match.py

Found "this"
in "Does this text match the pattern?"
from 5 to 9 ("this")
```

re中最常用的就是函数`search()`，第一个参数`pattern`表示搜索模式，第二个参数`text`表示被搜索的文本，当找到文本中能够匹配模式的部分时，返回一个`Match object`匹配对象，如果没有匹配，函数将返回`None`。

每个`Match object`对象中的属性包含了原本输入的`text`string字符串，使用的`pattern`正则表达式，和在文本中被匹配到部分的位置。

```python
match.re.pattern
match.re.string
match.start()
match.end()
```

### Compiling Expressions

### 编译表达式

尽管正则表达式是以字符串的形式作用在模块级别的函数中，在被频繁调用的时候，使用编译过的正则表达式更为高效。

使用`compile()`函数可以将字符串形式的表达式放在一个`RegexObject`对象中。

```python
# re_simple_compiled.py
import re

# Precompile the patterns
regexes = [
    re.compile(p)
    for p in ['this', 'that']
]
text = 'Does this text match the pattern?'

print('Text: {!r}\n'.format(text))

for regex in regexes:
    print('Seeking "{}" ->'.format(regex.pattern),
          end=' ')

    if regex.search(text):
        print('match!')
    else:
        print('no match')
```

```shell
$ python3 re_simple_compiled.py

Text: 'Does this text match the pattern?'

Seeking "this" -> match!
Seeking "that" -> no match

```



Ps:结合原文的注释，个人理解是需要将字符串形式的表达式转化能作用在字符串上的形式。而维护这种形式是需要内存开销的，通过提前“编译”操作，将这种开销在匹配之前完成，而不是在用户使用中消耗。

### Multiple Matches

### 多目标匹配

在之前的例子中，都是使用函数`search()`在文本中寻找“单独的“一个”纯文字的字符串“，通过使用函数`findall()`可以返回所有的子字符串的列表。注意，这种匹配是非重叠的。

非重叠的意思是在”aaaa“中寻找”aa“会得到["aa","aa"]两个匹配，而不是["aa","aa","aa","aa"]4个匹配，既第二个匹配是从上一个匹配之后的文本开始的，可以简单理解为一个被逐渐剪短的布条。

```python
# re_findall.py
import re

text = 'abbaaabbbbaaaaa'

pattern = 'ab'

for match in re.findall(pattern, text):
    print('Found {!r}'.format(match))
```

```shell
$ python3 re_findall.py

Found 'ab'
Found 'ab'
```

这里可以看到通过`findall()`找到的匹配是字符串，而通过另一个函数`finditer()`可以得到匹配实例

```python
# re_finditer.py
import re

text = 'abbaaabbbbaaaaa'

pattern = 'ab'

for match in re.finditer(pattern, text):
    s = match.start()
    e = match.end()
    print('Found {!r} at {:d}:{:d}'.format(
        text[s:e], s, e))
```

```shell
$ python3 re_finditer.py

Found 'ab' at 0:2
Found 'ab' at 5:7
```

也可以通过内部的方法获取匹配到的文本的位置。

### Pattern Syntax

### 模式句法

> Regular expressions support more powerful patterns than simple literal text strings. Patterns can repeat, can be anchored to different logical locations within the input, and can be expressed in compact forms that do not require every literal character to be present in the pattern. All of these features are used by combining literal text values with *meta-characters* that are part of the regular expression pattern syntax implemented by `re`.

GoogleTranslate:正则表达式比简单的文字文本字符串支持更强大的模式。模式可以重复，可以锚定到输入内的不同逻辑位置，并且可以以不需要在模式中存在每个字面字符的紧凑形式表示。所有这些功能都通过将文本文本值与元字符组合使用，元字符是由re实现的正则表达式模式语法的一部分。

```python
re_test_patterns.py
import re


def test_patterns(text, patterns):
    """Given source text and a list of patterns, look for
    matches for each pattern within the text and print
    them to stdout.
    """
    # Look for each pattern in the text and print the results
    for pattern, desc in patterns:
        print("'{}' ({})\n".format(pattern, desc))
        print("  '{}'".format(text))
        for match in re.finditer(pattern, text):
            s = match.start()
            e = match.end()
            substr = text[s:e]
            n_backslashes = text[:s].count('\\')
            prefix = '.' * (s + n_backslashes)
            print("  {}'{}'".format(prefix, substr))
        print()
    return


if __name__ == '__main__':
    test_patterns('abbaaabbbbaaaaa',
                  [('ab', "'a' followed by 'b'"),
                   ])
```

```Shell
$ python3 re_test_patterns.py

'ab' ('a' followed by 'b')

  'abbaaabbbbaaaaa'
  'ab'
  .....'ab'
```

`test_pattern()`这个函数，在这里并没有提前上述提到的特性，仅提供了一种输出模板，用来直观的展示接下来的特性。

#### Repetition

#### 重复

```Python
re_repetition.py
from re_test_patterns import test_patterns

test_patterns(
    'abbaabbba',
    [('ab*', 'a followed by zero or more b'),
     ('ab+', 'a followed by one or more b'),
     ('ab?', 'a followed by zero or one b'),
     ('ab{3}', 'a followed by three b'),
     ('ab{2,3}', 'a followed by two to three b')],
)
```

```shell
$ python3 re_repetition.py

'ab*' (a followed by zero or more b)

  'abbaabbba'
  'abb'
  ...'a'
  ....'abbb'
  ........'a'

'ab+' (a followed by one or more b)

  'abbaabbba'
  'abb'
  ....'abbb'

'ab?' (a followed by zero or one b)

  'abbaabbba'
  'ab'
  ...'a'
  ....'ab'
  ........'a'

'ab{3}' (a followed by three b)

  'abbaabbba'
  ....'abbb'

'ab{2,3}' (a followed by two to three b)

  'abbaabbba'
  'abb'
  ....'abbb'
```

例子中的`* + ? {3} {2,3}`被称为元字符**meta-character**，具有独立语法含义的最小单位，在表达式中被预定义好的的基本逻辑符号，都只是作用在他们前一个的表达式单位上，这里的表达式就是单个的字符。例子中的英文解释很通俗易懂，不在赘述。

再看一个例子

```python
# re_repetition_non_greedy.py
from re_test_patterns import test_patterns

test_patterns(
    'abbaabbba',
    [('ab*?', 'a followed by zero or more b'),
     ('ab+?', 'a followed by one or more b'),
     ('ab??', 'a followed by zero or one b'),
     ('ab{3}?', 'a followed by three b'),
     ('ab{2,3}?', 'a followed by two to three b')],
)
```

```shell
$ python3 re_repetition_non_greedy.py

'ab*?' (a followed by zero or more b)

  'abbaabbba'
  'a'
  ...'a'
  ....'a'
  ........'a'

'ab+?' (a followed by one or more b)

  'abbaabbba'
  'ab'
  ....'ab'

'ab??' (a followed by zero or one b)

  'abbaabbba'
  'a'
  ...'a'
  ....'a'
  ........'a'

'ab{3}?' (a followed by three b)

  'abbaabbba'
  ....'abbb'

'ab{2,3}?' (a followed by two to three b)

  'abbaabbba'
  'abb'
  ....'abb'
```

在`pattern`部分，每一个表达式的元字符后面多个一个`?`，跟随在元字符后面，表示关闭贪婪模式。贪婪模式**greedy**是指一种描述方式，在重复特性下不会有纯文字的匹配的“绝对性”，因为元字符具有`or`的含义，可能有很多种情况被匹配到，而贪婪是指，当一段文本被匹配到的时候，匹配会试图将下一个字符加入到这段文本中，如果符合会继续尝试下一个，直到不符合匹配。而非贪婪模式则只是，匹配模式下最短的那个，一旦匹配能满足表达式，则停止尝试下一个字符，在这里截断。

#### Character Sets

#### 字符集

前面的例子通过元字符对字符的个数进行限制，本例通过`[]`对可以出现哪些字符进行限制，如一个3个字符组成的字符串a[ab]c，那么在匹配到文本中的第二个字符将只能是”a“或者”b“。

```python
# re_charset.py
from re_test_patterns import test_patterns

test_patterns(
    'abbaabbba',
    [('[ab]', 'either a or b'),
     ('a[ab]+', 'a followed by 1 or more a or b'),
     ('a[ab]+?', 'a followed by 1 or more a or b, not greedy')],
)
```

```python
$ python3 re_charset.py

'[ab]' (either a or b)

  'abbaabbba'
  'a'
  .'b'
  ..'b'
  ...'a'
  ....'a'
  .....'b'
  ......'b'
  .......'b'
  ........'a'

'a[ab]+' (a followed by 1 or more a or b)

  'abbaabbba'
  'abbaabbba'

'a[ab]+?' (a followed by 1 or more a or b, not greedy)

  'abbaabbba'
  'ab'
  ...'aa'
```

分别表示，或，贪婪或，非贪婪或，的三种情况。

字符集具备”排除“的使用特性，通过使用`[^a]`可以将该位置为”a“，转换表述为该位置不可以为”a“

```python
re_charset_exclude.py
from re_test_patterns import test_patterns

test_patterns(
    'This is some text -- with punctuation.',
    [('[^-. ]+', 'sequences without -, ., or space')],
)
```

```python
$ python3 re_charset_exclude.py

'[^-. ]+' (sequences without -, ., or space)

  'This is some text -- with punctuation.'
  'This'
  .....'is'
  ........'some'
  .............'text'
  .....................'with'
  ..........................'punctuation'
```

当字符集边长的时候，输入连续的字符就变的很乏味了。像电话号码号段和车牌号尾数，很多的可匹配选项都是连续的使用”范围“表示方法，可以简化这种问题，只需输入起始和结尾即可。

```python
# re_charset_ranges.py
from re_test_patterns import test_patterns

test_patterns(
    'This is some text -- with punctuation.',
    [('[a-z]+', 'sequences of lowercase letters'),
     ('[A-Z]+', 'sequences of uppercase letters'),
     ('[a-zA-Z]+', 'sequences of letters of either case'),
     ('[A-Z][a-z]+', 'one uppercase followed by lowercase')],
)
```

```shell
$ python3 re_charset_ranges.py

'[a-z]+' (sequences of lowercase letters)

  'This is some text -- with punctuation.'
  .'his'
  .....'is'
  ........'some'
  .............'text'
  .....................'with'
  ..........................'punctuation'

'[A-Z]+' (sequences of uppercase letters)

  'This is some text -- with punctuation.'
  'T'

'[a-zA-Z]+' (sequences of letters of either case)

  'This is some text -- with punctuation.'
  'This'
  .....'is'
  ........'some'
  .............'text'
  .....................'with'
  ..........................'punctuation'

'[A-Z][a-z]+' (one uppercase followed by lowercase)

  'This is some text -- with punctuation.'
  'This'
```

period`.`表示匹配任一单一字符

```Python
# re_charset_dot.py
from re_test_patterns import test_patterns

test_patterns(
    'abbaabbba',
    [('a.', 'a followed by any one character'),
     ('b.', 'b followed by any one character'),
     ('a.*b', 'a followed by anything, ending in b'),
     ('a.*?b', 'a followed by anything, ending in b')],
)
```

```Shell
$ python3 re_charset_dot.py

'a.' (a followed by any one character)

  'abbaabbba'
  'ab'
  ...'aa'

'b.' (b followed by any one character)

  'abbaabbba'
  .'bb'
  .....'bb'
  .......'ba'

'a.*b' (a followed by anything, ending in b)

  'abbaabbba'
  'abbaabbb'

'a.*?b' (a followed by anything, ending in b)

  'abbaabbba'
  'ab'
  ...'aab'
```

#### Escape Codes

#### 转义字符

在正则表达式中，元字符提供了一种紧凑的描述方法，还有一种更为紧凑的方式是使用转义字符，

| Code | Meaning                                |
| ---- | -------------------------------------- |
| `\d` | a digit                                |
| `\D` | a non-digit                            |
| `\s` | whitespace (tab, space, newline, etc.) |
| `\S` | non-whitespace                         |
| `\w` | alphanumeric                           |
| `\W` | non-alphanumeric                       |

要注意的是，为了让`\`在python环境下的正则表达式中表示正则式转义控制符，而不是python转义字符，需要使用`r''`表达方式消除这个问题。(`r'xxxx'`告诉解释器，”xxxx“的类型是raw string，内部所有的字符都是字符消除在python中的元字符含义，这个字符串再用来被正则表达式重新解释。避免冲突）

```python
# re_escape_codes.py
from re_test_patterns import test_patterns

test_patterns(
    'A prime #1 example!',
    [(r'\d+', 'sequence of digits'),
     (r'\D+', 'sequence of non-digits'),
     (r'\s+', 'sequence of whitespace'),
     (r'\S+', 'sequence of non-whitespace'),
     (r'\w+', 'alphanumeric characters'),
     (r'\W+', 'non-alphanumeric')],
)
```

```shell
$ python3 re_escape_codes.py

'\d+' (sequence of digits)

  'A prime #1 example!'
  .........'1'

'\D+' (sequence of non-digits)

  'A prime #1 example!'
  'A prime #'
  ..........' example!'

'\s+' (sequence of whitespace)

  'A prime #1 example!'
  .' '
  .......' '
  ..........' '

'\S+' (sequence of non-whitespace)

  'A prime #1 example!'
  'A'
  ..'prime'
  ........'#1'
  ...........'example!'

'\w+' (alphanumeric characters)

  'A prime #1 example!'
  'A'
  ..'prime'
  .........'1'
  ...........'example'

'\W+' (non-alphanumeric)

  'A prime #1 example!'
  .' '
  .......' #'
  ..........' '
  ..................'!'
```

如果`\`也需要被匹配，那么被搜索的文本，和表达式中的"\"都需要被转换成`raw string`的形式。

```python
# re_escape_escapes.py
from re_test_patterns import test_patterns

test_patterns(
    r'\d+ \D+ \s+',
    [(r'\\.\+', 'escape code')],
)

```

```shell
 python3 re_escape_escapes.py

'\\.\+' (escape code)

  '\d+ \D+ \s+'
  '\d+'
  .....'\D+'
  ..........'\s+'

```

#### Anchoring

#### 锚固

| Code | Meaning                                  |
| ---- | ---------------------------------------- |
| `^`  | start of string, or line                 |
| `$`  | end of string, or line                   |
| `\A` | start of string                          |
| `\Z` | end of string                            |
| `\b` | empty string at the beginning or end of a word |
| `\B` | empty string not at the beginning or end of a word |

在一段文本中，可能会有很多的个地方被匹配到。但是文字本身出现的位置也是具有特殊含义的，比如我们指向匹配文本中第一个单词“Ave”。

```Python
# re_anchoring.py
from re_test_patterns import test_patterns

test_patterns(
    'This is some text -- with punctuation.',
    [(r'^\w+', 'word at start of string'),
     (r'\A\w+', 'word at start of string'),
     (r'\w+\S*$', 'word near end of string'),
     (r'\w+\S*\Z', 'word near end of string'),
     (r'\w*t\w*', 'word containing t'),
     (r'\bt\w+', 't at start of word'),
     (r'\w+t\b', 't at end of word'),
     (r'\Bt\B', 't, not start or end of word')],
)
```

```shell
$ python3 re_anchoring.py

'^\w+' (word at start of string)

  'This is some text -- with punctuation.'
  'This'

'\A\w+' (word at start of string)

  'This is some text -- with punctuation.'
  'This'

'\w+\S*$' (word near end of string)

  'This is some text -- with punctuation.'
  ..........................'punctuation.'

'\w+\S*\Z' (word near end of string)

  'This is some text -- with punctuation.'
  ..........................'punctuation.'

'\w*t\w*' (word containing t)

  'This is some text -- with punctuation.'
  .............'text'
  .....................'with'
  ..........................'punctuation'

'\bt\w+' (t at start of word)

  'This is some text -- with punctuation.'
  .............'text'

'\w+t\b' (t at end of word)

  'This is some text -- with punctuation.'
  .............'text'

'\Bt\B' (t, not start or end of word)

  'This is some text -- with punctuation.'
  .......................'t'
  ..............................'t'
  .................................'t'
```



## Reference

Pyhon 3 module of the Week. re - Regular Expressions

Retrieved 2017-09-14

from *PyMOTW-3* ： https://pymotw.com/3/re/index.html



— EOF —  