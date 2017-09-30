---
layout: post
title: "os.path - Platform-independent Manipulation of Filenames"
date: 2017-09-30 11:42:55
---

# os.path - Platform-independent Manipulation of Filenames

os.path 平台独立的文件名操作module

**目的**：解析，建立，测试，对文件名和路径进行一些额外的处理工作。

os.path 多平台兼容，即使单平台使用，它也是一个很好的选择。

#### Parsing paths

#### 路径解析

路径解析取决于os中定义的几个变量：

路径之间的分隔符，像

```os.sep e.g.  "/"or"\"```

文件名与文件扩展名分隔符

```os.extsep e.g. "."```

路径组件中表示目录树向上一级

```os.pardir (e.g.,"..")```

路径组件中表示目录树同级

```os.curdir (e.g.,".")```

**split()**

ps:将“/”之间的部分称为路径元素，将“/”称为路径组件，“.”作为组建时候表示路径树级别，作为文件分隔符讲看做连接前后元素内容的一部分。

函数```split()```将path切分成两部分，然后一个元组作为结果，元组中的最后第二个元素为path路径中最后一个组件，第一个元素为组件前的所有内容。

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_split.py
import os.path
PATHS = [
    '/one/two/three',
    '/one/two/three/',
    '/one/two/three/four.txt',
    '/',
    '.',
    '',
]
for path in PATHS:
    print('{!r:>17}:{}'.format(path,os.path.split(path)))
```

当路径以```"/"``` 结尾的时候，元组中第二个元素为空。

```shell
 '/one/two/three':('/one/two', 'three')
'/one/two/three/':('/one/two/three', '')
'/one/two/three/four.txt':('/one/two/three', 'four.txt')
              '/':('/', '')
              '.':('', '.')
               '':('', '')

Press ENTER or type command to continue
```

**basename()**

如果只需要`split()`返回元组中的第二个元素，使用`basename()`

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_basename.py
import os.path
PATHS = [
    '/one/two/three',
    '/one/two/three/',
    '/one/two/three/four.txt',
    '/',
    '.',
    '',
]

for path in PATHS:
    print('{!r:>17}:{}'.format(path,os.path.basename(path)))
```

```shell
 '/one/two/three':three
'/one/two/three/':
'/one/two/three/four.txt':four.txt
              '/':
              '.':.
               '':

Press ENTER or type command to continue
```

**dirname()**

相似的，如果只需要第一部分使用 `dirname()`

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_dirname.py
import os.path
PATHS = [
    '/one/two/three',
    '/one/two/three/',
    '/',
    '',
]
for path in PATHS:
    print('{!r:>17}:{}'.format(path, os.path.dirname(path)))
```

dirname + basename = original path

```shell
 '/one/two/three':/one/two
'/one/two/three/':/one/two/three
              '/':/
               '':

Press ENTER or type command to continue
```

**splitext()**

`splitext()`类似`split()`但是分割发生在扩展名分隔符，而不是目录分隔符。

```python
  #!/usr/bin/env python
  # encoding: utf-8

  # ospath_splitext.py
  import os.path
  PATHS = [
      'filename.txt',
      'filenam',
      '/path/to/filename.txt',
      '/',
      'my-archive.tar.gz',
      'no-extension.',
  ]

  for path in PATHS:
S>    print('{!r:>21}:{!r}'.format(path,os.path.splitext(path)))
```

 `"."`是被划分到第二个元素的

```shell
       'filename.txt':('filename', '.txt')
            'filenam':('filenam', '')
'/path/to/filename.txt':('/path/to/filename', '.txt')
                  '/':('/', '')
  'my-archive.tar.gz':('my-archive.tar', '.gz')
      'no-extension.':('no-extension', '.')

Press ENTER or type command to continue
```

**commonprefix()**

将一组路径的列表作为参数，返回数组的相同前缀字符串，这个值可能是一个实际不存在的路径（截取到相同文件名的前缀处停止了），既不会停止在分隔符上。

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_commonprefix.py
import os.path
paths = [
    '/one/two/three/four',
    '/one/two/threefold',
    '/one/two/three/',
]
for path in paths:
    print('PATH:',path)

print()
print('PREFIX:',os.path.commonprefix(paths))
```

```shell
('PATH:', '/one/two/three/four')
('PATH:', '/one/two/threefold')
('PATH:', '/one/two/three/')
()
('PREFIX:', '/one/two/three')

Press ENTER or type command to continue
```

**commonpath()**

不同于上面的函数，这个函数会在目录分隔符上停止

```python
#!/usr/bin/env python3
# encoding: utf-8

# ospath_commonpath.py
import os.path
paths = [
    '/one/two/three/four',
    '/one/two/threefold',
    '/one/two/three/',
]

for path in paths:
    print('PATH:', path)

print()
print('PREFIX:', os.path.commonpath(paths))
```

```shell
PATH: /one/two/three/four
PATH: /one/two/threefold
PATH: /one/two/three/

PREFIX: /one/two

Press ENTER or type command to continue
```

#### Building Paths

#### 建立路径，拼接路径

**join()**

将多个路径组件拼接成一个

```python
#!/usr/bin/env python3
# encoding: utf-8

# ospath_join.py
import os.path
PATHS = [
    ('one', 'two', 'three'),
    ('/', 'one', 'two', 'three'),
    ('/one', '/two', '/three'),
    ('/', '/one', '/two', '/three'),
]

for parts in PATHS:
    print('{} : {!r}'.format(parts, os.path.join(*parts)))
```

如果一个参数前面带有目录分隔符，那么前面的部分将不会出现

```shell
('one', 'two', 'three') : 'one/two/three'
('/', 'one', 'two', 'three') : '/one/two/three'
('/one', '/two', '/three') : '/three'
('/', '/one', '/two', '/three') : '/three'

Press ENTER or type command to continue
```

**expanduser()**

还可以使用包含可以自动扩展的“可变”组件的路径。例如，`expanduser()`将波形符（〜）字符转换为用户主目录的名称。

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_expanduser.py

import os.path

for user in ['', 'qducc', 'someoneelse', 'nosuchuser']:
    lookup = '~' + user
    print('{!r:>15} : {!r}'.format(lookup, os.path.expanduser(lookup)))
```

~~~shell
            '~' : '/Users/qducc'
       '~qducc' : '/Users/qducc'
 '~someoneelse' : '~someoneelse'
  '~nosuchuser' : '~nosuchuser'

Press ENTER or type command to continue
~~~

**expandvars()**

使用变量

```Python
#!/usr/bin/env python
# encoding: utf-8

# ospath_expandvars.py

import os.path
import os
os.environ['MYVAR'] = 'VALUE'

print(os.path.expandvars('/path/to/$MYVAR'))
```

```shell
/path/to/VALUE

Press ENTER or type command to continue
```

**normpath()**

规范化表达式

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_normpath.py

import os.path

PATHS = [
    'one//two//three',
    'one/./two/./three',
    'one/../alt/two/three',
]

for path in PATHS:
    print('{!r:>22} : {!r}'.format(path, os.path.normpath(path)))
```

```shell
     'one//two//three' : 'one/two/three'
   'one/./two/./three' : 'one/two/three'
'one/../alt/two/three' : 'alt/two/three'

Press ENTER or type command to continue
```

**abspath**

绝对路径

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_abspath.py
import os
import os.path

os.chdir('/usr')

PATHS = [
    '.',
    '..',
    './one/two/three',
    '../one/two/three',
]

for path in PATHS:
    print('{!r:>21} : {!r}'.format(path, os.path.abspath(path)))
```

```Shell
                  '.' : '/usr'
                 '..' : '/'
    './one/two/three' : '/usr/one/two/three'
   '../one/two/three' : '/one/two/three'

Press ENTER or type command to continue
```

#### File Times

#### 文件参数

```python
#!/usr/bin/env python
# encoding: utf-8

# ospath_properties.py
import os.path
import time

print('File         :', __file__)
print('Access time  :', time.ctime(os.path.getatime(__file__)))
print('Modified time:', time.ctime(os.path.getmtime(__file__)))
print('Change time  :', time.ctime(os.path.getctime(__file__)))
print('Size         :', os.path.getsize(__file__))
```

```shell
('File         :', '/Users/qducc/code/PyMOTW-3/TheFileSystem/ospath_properties.p
y')
('Access time  :', 'Sat Sep 30 16:47:27 2017')
('Modified time:', 'Sat Sep 30 16:46:41 2017')
('Change time  :', 'Sat Sep 30 16:46:41 2017')
('Size         :', 369)

Press ENTER or type command to continue
```

#### Testing Files

验证文件路径信息

```python
!/usr/bin/env python
# encoding: utf-8

# ospath_tests.py
import os.path

FILENAMES = [
    __file__,
    os.path.dirname(__file__),
    '/',
    './broken_link',
]

for file in FILENAMES:
    print('File        : {!r}'.format(file))
    print('Absolute    :', os.path.isabs(file))
    print('Is File?    :', os.path.isfile(file))
    print('Is Dir?     :', os.path.isdir(file))
    print('Is Link?    :', os.path.islink(file))
    print('Mountpoint? :', os.path.ismount(file))
    print('Exists?     :', os.path.exists(file))
    print('Link Exists?:', os.path.lexists(file))
    print()
```

```shell
File        : '/Users/qducc/code/PyMOTW-3/TheFileSystem/ospath_tests.py'
('Absolute    :', True)
('Is File?    :', True)
('Is Dir?     :', False)
('Is Link?    :', False)
('Mountpoint? :', False)
('Exists?     :', True)
('Link Exists?:', True)
()
File        : '/Users/qducc/code/PyMOTW-3/TheFileSystem'
('Absolute    :', True)
('Is File?    :', False)
('Is Dir?     :', True)
('Is Link?    :', False)
('Mountpoint? :', False)
('Exists?     :', True)
('Link Exists?:', True)
()
File        : '/'
('Absolute    :', True)
('Is File?    :', False)
('Is Dir?     :', True)
('Is Link?    :', False)
('Mountpoint? :', True)
('Exists?     :', True)
('Link Exists?:', True)
()
File        : './broken_link'
('Absolute    :', False)
('Is File?    :', False)
('Is Dir?     :', False)
('Is Link?    :', False)
('Mountpoint? :', False)
('Exists?     :', False)
('Link Exists?:', False)
()

Press ENTER or type command to continue
```

## Reference

Pyhon 3 module of the Week. 

os.path - Platform-independent Manipulation of Filenames

Retrieved 2017-09-30

from *PyMOTW-3* ： https://pymotw.com/3/re/index.html

— EOF —