---
layout: post
title: "pip-pop 源码学习"
date: 2017-10-12 12:19:21
---

# pip-pop 源码学习

## lawyer up

项目开始，使用MIT开源协议。s

## dummy dir

创建目录`pip_pop`

```shell
.
├── LICENSE
└── pip_pop
    └── __init__.py

1 directory, 2 files
```

[python dir中 `__init__.py`详解](http://www.cnblogs.com/Lands-ljk/p/5880483.html)

## READ IT

添加说明文件

```shell
.
├── LICENSE
├── README.rst
└── pip_pop
    └── __init__.py

1 directory, 3 files
```

`README.rst`使用了rst格式而不是md格式。在PyPi中对rst的支持更好。

```Rst
pip-pop: tools for managing requirements files
==============================================

Working with lots of ``requirements.txt`` files can be a bit annoying.
Have no fear, **pip-pop** is here!

(work in progress)

Planned Commands
----------------

``$ pip-diff [--fresh | --stale] <reqfile> <reqfile> [output]``

Generates a diff between two given requirements files. Lists either stale or fresh packages.

``$ pip-flatten [--unsorted] <reqfile> [output]``

Takes a single requirements file, and expands it. Essential when working with included files.


Possible Future Commands
------------------------

- ``pip-where``
```

通过git记录可以看到大神们写代码的顺序和中间的变化。需要实现哪些功能和命令的格式在一开始就被规定好了。

`pip-diff`给定的两个需求文件，输出差异。列出需求文件中旧或新的包。

`pip-flatten`取一个需求文件，并将其扩展。扩展包括使用时候必不可少的项。

*不清楚是什么意思，可能列出是一些包的依赖。*

## docopt

```shell
.
├── LICENSE
├── README.rst
├── pip_pop
│   └── __init__.py
└── requirements.txt

1 directory, 4 files
```

添加项目本需求文件

 docopt可以为app添加丰富的交互信息，包括参数和帮助以及描述信息。

[github:docopt](https://github.com/docopt/docopt)

[小赖的Python学习笔记 《docopt - 创建漂亮的命令行交互界面》](https://wp-lai.gitbooks.io/learn-python/content/0MOOC/docopt.html)

wsgiref 是 [PEP 333](http://legacy.python.org/dev/peps/pep-0333/) 定义的 wsgi 规范的范例实现。

[wsgiref_doc](http://peak.telecommunity.com/wsgiref_docs/)

[Cizixs blog  《wsgiref 源码解析》](http://cizixs.com/2014/11/09/dive-into-wsgiref)

进行到这里在Pycharm中建立虚拟环境安装需求文件

要注意docopt支持python2而不是python3

```Shell
pip install -r requirement.txt
```

```shell
Collecting docopt==0.6.2 (from -r requirements.txt (line 1))
Requirement already satisfied (use --upgrade to upgrade): wsgiref==0.1.2 in /System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7 (from -r requirements.txt (line 2))
Installing collected packages: docopt
Successfully installed docopt-0.6.2
```

## note about blacklisting plans

```rst
Will potentially support blacklisting modules (wsgiref, distribute, setuptools)
```

readme.rst中添加了新的描述。可能支持黑名单模块

## Update README.rst

说明文件内容结构变动

## exes

```shell
.
├── LICENSE
├── README.rst
├── bin
│   ├── pip-diff
│   └── pip-flatten
├── pip_pop
│   └── __init__.py
├── requirements.txt
└── setup.py

2 directories, 7 files

```

添加了`setup.py`应该是配合`setuptools`使用

不清楚bin文件夹是干什么的。

[闫肃博客 《Python包管理工具setuptools详解》](http://yansu.org/2013/06/07/learn-python-setuptools-in-detail.html)

```Python
"""
pip-pop manages your requirements files.
"""
import sys
from setuptools import setup


setup(
    name='pip-pop',
    version='0.0.0',
    url='https://github.com/kennethreitz/pip-pop',
    license='MIT',
    author='Kenneth Reitz',
    author_email='me@kennethreitz.org',
    description=__doc__.strip('\n'),
    #packages=[],
    scripts=['bin/pip-diff', 'bin/pip-flatten'],
    #include_package_data=True,
    zip_safe=False,
    platforms='any',
    install_requires=['docopt'],
    classifiers=[
        # As from https://pypi.python.org/pypi?%3Aaction=list_classifiers
        #'Development Status :: 1 - Planning',
        #'Development Status :: 2 - Pre-Alpha',
        #'Development Status :: 3 - Alpha',
        'Development Status :: 4 - Beta',
        #'Development Status :: 5 - Production/Stable',
        #'Development Status :: 6 - Mature',
        #'Development Status :: 7 - Inactive',
        'Programming Language :: Python',
        'Programming Language :: Python :: 2',
        #'Programming Language :: Python :: 2.3',
        #'Programming Language :: Python :: 2.4',
        #'Programming Language :: Python :: 2.5',
        'Programming Language :: Python :: 2.6',
        'Programming Language :: Python :: 2.7',
        #'Programming Language :: Python :: 3',
        #'Programming Language :: Python :: 3.0',
        #'Programming Language :: Python :: 3.1',
        #'Programming Language :: Python :: 3.2',
        #'Programming Language :: Python :: 3.3',
        'Intended Audience :: Developers',
        'Intended Audience :: System Administrators',
        'License :: OSI Approved :: BSD License',
        'Operating System :: OS Independent',
        'Topic :: System :: Systems Administration',
    ]
)
```

diffing works

使用pycham打不开这个没有后缀的文件，还是动用了vim。

```python
  #!/usr/bin/env python
  # -*- coding: utf-8 -*-

  """Usage:
    pip-diff (--fresh | --stale) <reqfile1> <reqfile2>
    pip-diff (-h | --help)

  Options:
    -h --help     Show this screen.
    --fresh       List newly added packages.
    --stale       List removed packages.
  """
  import os
  from docopt import docopt
  from pkg_resources import parse_requirements
```

前面的字符串应该是给`docopt`使用提供帮助信息的

```python
  # TODO: ignore lines
  IGNORABLE_LINES = '#', '-r'
  VERSION_OPERATORS = ['==', '>=', '<=', '>', '<', ',']

  def split(s):
      for operator in VERSION_OPERATORS:
          s = s.replace(operator, '!')

      return s.split('!')
```

函数参数为文本，如果文本中出现列表中的字符，将字符替换成！然后根据！做切分。

*每次遍历只能修改一种符号，好蛋疼。不过感觉也没别的方法*

![Screen Shot 2017-10-12 at 3.38.50 PM](/Users/qducc/Desktop/Screen Shot 2017-10-12 at 3.38.50 PM.png)

关于super()

[漩涡鸣人博客 《python super()》](http://www.cnblogs.com/lovemo1314/archive/2011/05/03/2035005.html)

`super(classname, self).__init_()`调用该类父类的方法。

既先使用父类的初始化，再执行自己的初始化代码。

关于`__repr__`

[readthedocs 《python3-cookbook 8.1改变对象的字符串显示》](http://python3-cookbook.readthedocs.io/zh_CN/latest/c08/p01_change_string_representation_of_instances.html)

用于改变对象的显示形式，python解释器中直接输出对象的时候会根据`__repr__`显示，使用print的时候则会根据`__str__`的返回值显示。

在python中还有repr和str两个函数，repr并不转化对象，而str会将对象转化为str类型。repr是一种明确的表示形式，而str则可能改变对象结构如对float型数字的输出。

`os.path.exists`(*path*)

如果*path*引用一个存在的路径，返回`True`。如果path引用一个断开的符号链接，返回`False`。在某些平台上，尽管*path*物理存在，但是由于没有执行[`os.stat()`](http://python.usyiyi.cn/documents/python_278/library/os.html#os.stat)的权限，该函数也会返回`False`。

[os.path - 常用路径名操作](http://python.usyiyi.cn/translate/python_278/library/os.path.html)

使用return和raise抛出异常有什么区别？

在try中raise异常和在except中raise异常有什么区别？

关于`s.strip()`

``s.strip`()`函数移除字符串头尾处指定的字符，默认为空格，若指定‘0’，则`s.strip('0')`

```python
str = "0000000this is string 0000example....wow!!!0000000";
print str.strip( '0' )
```

```shell
this is string 0000example....wow!!!
```

关于`all()和any()`

[简书 翎月 《Python之all()\any()》](http://www.jianshu.com/p/65b6b4a62071)

在python解释器中使用help查看函数的解释

```Txt
>>>help(all)
```

```Txt
all(...)
    all(iterable) -> bool

    Return True if bool(x) is True for all values x in the iterable.
    If the iterable is empty, return True.
```

all()的参数列表为一个可迭代对象，返回值为bool型。对于可迭代对象中的每一个对象，对象都为True时，返回True，特别的如果可迭代对象为空，也返回True。

```Txt
any(...)
    any(iterable) -> bool

    Return True if bool(x) is True for any x in the iterable.
    If the iterable is empty, return False.
```

any()的参数列表为一个可迭代对象，返回值为bool型。对于可迭代对象中的每一个对象，对象有一个为True时，返回True，特别的如果可迭代对象为空，也返回False。

```python
# Skip lines that start with any comment/control charecters.
if not any([line.startswith(p) for p in IGNORABLE_LINES]):
    data.append(line)
```

行是否以忽略列表中的某一个字符开头，若一个都没有。则跳过将行加入data中。

比如#是注释控制符，若行以#开头，则any返回True，判断返回False，不执行append()

```python
for requirement in parse_requirements(data):
	self.requirements.append(requirement)
```

```Python
parse_requirements()
```

'# todo' 是setuptools中的函数，具体用来干什么不清楚。

```python
# Generate fresh packages.
other_reqs = (
    [r.project_name for r in r1.requirements]
    if ignore_versions else r1.requirements
    )

for req in r2.requirements:
    r = req.project_name if ignore_versions else req

	if r not in other_reqs:
    	results['fresh'].append(req)
# Generate stale packages.
other_reqs = (
    [r.project_name for r in r2.requirements]     
    if ignore_versions else r2.requirements
    )

for req in r1.requirements:
    r = req.project_name if ignore_versions else req

    if r not in other_reqs:
        results['stale'].append(req)

return results
```

‘# todo’ 经过parse_requirements()几乎data被变成了对象。

关于三元表达式

[eastlakeside.gitbooks Python进阶 三元表达式](https://eastlakeside.gitbooks.io/interpy-zh/content/ternary_operators/ternary_operators.html)

```Python
#如果条件为真，返回真 否则返回假
condition_is_true if condition else condition_is_false
```

```shell
>>> a = 1
>>> b = 0 if a>0 else 2
>>> b
0
>>> a = 0
>>> b = 0 if a>0 else 2
>>> b
2
>>>
```

在requirements.txt同目录下添加requirements2.txt，修改其中一个文件的版本号。

```shell
(vir_2_pip-pop)iMac:bin qducc$ python pip-diff --fresh ../requirements.txt  ../requirements.txt 
{'fresh': [], 'stale': []}
(vir_2_pip-pop)iMac:bin qducc$ python pip-diff --fresh ../requirements.txt  ../requirements2.txt 
{'fresh': [Requirement.parse('wsgiref==0.1.3')], 'stale': [Requirement.parse('wsgiref==0.1.2')]}
(vir_2_pip-pop)iMac:bin qducc$ python pip-diff --fresh ../requirements2.txt  ../requirements.txt 
{'fresh': [Requirement.parse('wsgiref==0.1.2')], 'stale': [Requirement.parse('wsgiref==0.1.3')]}
(vir_2_pip-pop)iMac:bin qducc$ 

```

It works~. 捕捉到了变化。然后把x2.txt删除

## output for pip-diff

```shell
 
 def diff(r1, r2, include_fresh=False, include_stale=False):
     # assert that r1 and r2 are files.
 
+    include_versions = True if include_stale else False
+
     try:
         r1 = Requirements(r1)
         r2 = Requirements(r2)
     except ValueError:
         print 'There was a problem loading the given requirements files.'
         exit(os.EX_NOINPUT)
 
-    results = r1.diff(r2)
-    print results
+    results = r1.diff(r2, ignore_versions=True)
+
+    if include_fresh:
+        for line in results['fresh']:
+            print line.project_name if include_versions else line
+
+    if include_stale:
+        for line in results['stale']:
+            print line.project_name if include_versions else line

```

发生变化的文件是bin/pip-diff

更改了diff的输出模式，从类标签，变成了print项目名，而且忽略的版本号。这样之前在requirement2.txt中修改版本号的做法就不会得到输出，将True修改成False就可以看到输出了。

## cleanup

删除了一些无用的注释，和todo

## tuples

```Shell
 import os
 from docopt import docopt
 from pkg_resources import parse_requirements
 
 
 # TODO: ignore lines
-IGNORABLE_LINES = '#', '-r'
-VERSION_OPERATORS = ['==', '>=', '<=', '>', '<', ',']
+IGNORABLE_LINES = ('#', '-r')
+VERSION_OPERATORS = ('==', '>=', '<=', '>', '<', ',')
 
 def split(s):
     for operator in VERSION_OPERATORS:
         s = s.replace(operator, '!')
 
     return s.split('!')

```

将变量从列表修改为元组。元组可以避免危险修改。

[Python 元组|菜鸟教程](http://www.runoob.com/python/python-tuples.html)

## remove bunk files

删除 `pip_pop/__init__.py`文件，不清楚为什么将其称为bunk files，也不清楚为什么要删除它。



## rely on pip

从setuptools的需求文件语法分析器转而依赖pip的。

修改1

```Shell
   -h --help     Show this screen.
   --fresh       List newly added packages.
   --stale       List removed packages.
 """
 import os
 from docopt import docopt
-from pkg_resources import parse_requirements
-
+from pip.req import parse_requirements
 
 # TODO: ignore lines
 IGNORABLE_LINES = ('#', '-r')
 VERSION_OPERATORS = ('==', '>=', '<=', '>', '<', ',')
 
 def split(s):


```

移除setuptools的需求文件语法分析方法，转而使用pip的需求文件语法分析方法。

修改2

```shell
 
     def load(self, reqfile):
 
         if not os.path.exists(reqfile):
             raise ValueError('The given requirements file does not exist.')
 
-        with open(reqfile) as f:
-            data = []
-
-            for line in f:
-                line = line.strip()
-
-                # Skip lines that start with any comment/control charecters.
-                if not any([line.startswith(p) for p in IGNORABLE_LINES]):
-                    data.append(line)
-
-            for requirement in parse_requirements(data):
-                self.requirements.append(requirement)
+        for requirement in parse_requirements(reqfile):
+            self.requirements.append(requirement.req)
 
 
     def diff(self, requirements, ignore_versions=False):
         r1 = self
         r2 = requirements
         results = {'fresh': [], 'stale': []}

```

使用了新的需求文件语法分析器后，不需要再对文件中的命令符和控制符进行特殊处理。pip的分析器函数可能封装了特殊处理。

## getting simpler and simpler!

在转而依赖pip的parse后之前需要处理特殊符号的部分可以被简化了。

```shell
# TODO: ignore lines
IGNORABLE_LINES = ('#', '-r')
VERSION_OPERATORS = ('==', '>=', '<=', '>', '<', ',')

def split(s):
    for operator in VERSION_OPERATORS:
        s = s.replace(operator, '!')

    return s.split('!')
```

下面这部分被移除。

## only check lines that have explicit requirements

```shell
     def load(self, reqfile):
 
         if not os.path.exists(reqfile):
             raise ValueError('The given requirements file does not exist.')
 
         for requirement in parse_requirements(reqfile):
-            self.requirements.append(requirement.req)
+            if requirement.req:
+                self.requirements.append(requirement.req)
 
 
     def diff(self, requirements, ignore_versions=False):
         r1 = self
         r2 = requirements
         results = {'fresh': [], 'stale': []}

```

仅检查哪些具有明确需求的行。

没有去看这个分析器的源码和解析。应该是一些没有版本号或“不明确的”行将被忽略。

## initial version of pip-grep

开始进行第二个功能

```shell
     url='https://github.com/kennethreitz/pip-pop',
     license='MIT',
     author='Kenneth Reitz',
     author_email='me@kennethreitz.org',
     description=__doc__.strip('\n'),
     #packages=[],
-    scripts=['bin/pip-diff', 'bin/pip-flatten'],
+    scripts=['bin/pip-diff', 'bin/pip-grep'],
     #include_package_data=True,
     zip_safe=False,
     platforms='any',
     install_requires=['docopt'],
     classifiers=[
         # As from https://pypi.python.org/pypi?%3Aaction=list_classifiers

```

删除了pip-flatten 创建了pip-grep文件 setup.py中的文件名也进行了同步修改。

```shell
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""Usage:
  pip-grep <reqfile> <package>...

Options:
  -h --help     Show this screen.
"""
import os
from docopt import docopt
from pip.req import parse_requirements


class Requirements(object):
    def __init__(self, reqfile=None):
        super(Requirements, self).__init__()
        self.path = reqfile
        self.requirements = []

        if reqfile:
            self.load(reqfile)

    def __repr__(self):
        return '<Requirements \'{}\'>'.format(self.path)

    def load(self, reqfile):

        if not os.path.exists(reqfile):
            raise ValueError('The given requirements file does not exist.')

        for requirement in parse_requirements(reqfile):
            self.requirements.append(requirement)


def grep(reqfile, packages):

    try:
        r = Requirements(reqfile)
    except ValueError:
        print 'There was a problem loading the given requirement file.'
        exit(os.EX_NOINPUT)

    for requirement in r.requirements:
        if requirement.req.project_name in packages:
            print 'Package {} found!'.format(requirement.req.project_name)
            exit(0)

        print 'Not found.'.format(requirement.req.project_name)
        exit(1)


def main():
    args = docopt(__doc__, version='pip-grep')

    kwargs = {'reqfile': args['<reqfile>'], 'packages': args['<package>']}

    grep(**kwargs)


if __name__ == '__main__':
    main()
```

```shell
def grep(reqfile, packages):

    try:
        r = Requirements(reqfile)
    except ValueError:
        print 'There was a problem loading the given requirement file.'
        exit(os.EX_NOINPUT)

    for requirement in r.requirements:
        if requirement.req.project_name in packages:
            print 'Package {} found!'.format(requirement.req.project_name)
            exit(0)

        print 'Not found.'.format(requirement.req.project_name)
        exit(1)
```

核心是这个函数， 在需求文件中寻找指定的packages。

## updated readme

更新需求文件

```shell
 
 (work in progress)
 
 Planned Commands
 ----------------
 
-``$ pip-diff [--fresh | --stale] <reqfile> <reqfile> [output]``
+``$ pip-diff [--fresh | --stale] <reqfile> <reqfile>``
 
 Generates a diff between two given requirements files. Lists either stale or fresh packages.
 
-``$ pip-flatten [--unsorted] <reqfile> [output]``
+``$ pip-grep <reqfile> <package>...``
 
-Takes a single requirements file, and expands it. Essential when working with included files.
-Will potentially support blacklisting modules (wsgiref, distribute, setuptools).
+Takes a requirements file, and searches for the speficied package (or packages) within it.
+Essential when working with included files.
 
 
 Possible Future Commands
 ------------------------
 
-- ``pip-where``
+- Install with blacklisting support (wsgiref, distribute, setuptools).

```

## fix for pip-grep

```shell
         r = Requirements(reqfile)
     except ValueError:
         print 'There was a problem loading the given requirement file.'
         exit(os.EX_NOINPUT)
 
     for requirement in r.requirements:
-        if requirement.req.project_name in packages:
-            print 'Package {} found!'.format(requirement.req.project_name)
-            exit(0)
 
-        print 'Not found.'.format(requirement.req.project_name)
-        exit(1)
+        if requirement.req:
+
+            if requirement.req.project_name in packages:
+                print 'Package {} found!'.format(requirement.req.project_name)
+                exit(0)
+
+    print 'Not found.'.format(requirement.req.project_name)
+    exit(1)
 
 
 def main():
     args = docopt(__doc__, version='pip-grep')

```

## silent mode for pip-pop

添加silent模式

```shell
 #!/usr/bin/env python
 # -*- coding: utf-8 -*-
 
 """Usage:
-  pip-grep <reqfile> <package>...
+  pip-grep [-s] <reqfile> <package>...
 
 Options:
   -h --help     Show this screen.
 """
 import os
 from docopt import docopt

```

添加了新模式的说明

```shell
-def grep(reqfile, packages):
+def grep(reqfile, packages, silent=False):
 
     try:
         r = Requirements(reqfile)
     except ValueError:
-        print 'There was a problem loading the given requirement file.'
+        if not silent:
+            print 'There was a problem loading the given requirement file.'
         exit(os.EX_NOINPUT)
 
     for requirement in r.requirements:
 
         if requirement.req:
 
             if requirement.req.project_name in packages:
-                print 'Package {} found!'.format(requirement.req.project_name)
+                if not silent:
+                    print 'Package {} found!'.format(requirement.req.project_name)
                 exit(0)
 
     print 'Not found.'.format(requirement.req.project_name)
     exit(1)

```

为函数配置了参数，当关闭寂静模式会输出反馈信息，当打开寂静模式不会输出。

```shell
 def main():
     args = docopt(__doc__, version='pip-grep')
 
-    kwargs = {'reqfile': args['<reqfile>'], 'packages': args['<package>']}
+    kwargs = {'reqfile': args['<reqfile>'], 'packages': args['<package>'], 'silent': args['-s']}
+
 
     grep(**kwargs)
 
 
 
 if __name__ == '__main__':

```

在命令行里配置新模式的参数。

## silence not found

```shell
-    print 'Not found.'.format(requirement.req.project_name)
+    if not silent:
+        print 'Not found.'.format(requirement.req.project_name)
+
     exit(1)

```

和上一次commit的目标相同，not found的寂静被漏掉了，补充上

*不过这种每个语句都要加控制参数的方式，后面可能有更优雅的方式来实现。*

## python 3 compatibillity

兼容python3，python3 print是必须要用()的，而python2是可用可不用的。

```shell
-        print 'There was a problem loading the given requirements files.'
+        print('There was a problem loading the given requirements files.')
         exit(os.EX_NOINPUT)
 
     results = r1.diff(r2, ignore_versions=True)
 
     if include_fresh:
         for line in results['fresh']:
-            print line.project_name if include_versions else line
+            print(line.project_name if include_versions else line)
 
     if include_stale:
         for line in results['stale']:
-            print line.project_name if include_versions else line
+            print(line.project_name if include_versions else line)

```

```shell
-            print 'There was a problem loading the given requirement file.'
+            print('There was a problem loading the given requirement file.')
 
         exit(os.EX_NOINPUT)
 
     for requirement in r.requirements:
 
         if requirement.req:
 
             if requirement.req.project_name in packages:
 
                 if not silent:
-                    print 'Package {} found!'.format(requirement.req.project_name)
+                    print('Package {} found!'.format(requirement.req.project_name))
 
                 exit(0)
 
     if not silent:
-        print 'Not found.'.format(requirement.req.project_name)
+        print('Not found.'.format(requirement.req.project_name))

```

## v0.1.0

版本发布

```shell
 import sys
 from setuptools import setup
 
 
 setup(
     name='pip-pop',
-    version='0.0.0',
+    version='0.1.0',
     url='https://github.com/kennethreitz/pip-pop',
     license='MIT',
     author='Kenneth Reitz',
     author_email='me@kennethreitz.org',
     description=__doc__.strip('\n'),
     #packages=[],

```

[github 起草关于版本号拟定规则](http://semver.org/)

[github 起草关于版本号拟定规则 中文版](http://semver.org/lang/zh-CN/)

## Add a dummp finder so parse_requirement does not fail on —arguments

这是其他用户提交的一个分支，对程序进行了修改。

```shell
  --fresh       List newly added packages.
  --stale       List removed packages.
"""
import os
from docopt import docopt
from pip.req import parse_requirements
from pip.index import PackageFinder # new import

class Requirements(object):
    def __init__(self, reqfile=None):
        super(Requirements, self).__init__()
        self.path = reqfile
        self.requirements = []

```

又是pip的参数

```shell
 
     def load(self, reqfile):
 
         if not os.path.exists(reqfile):
             raise ValueError('The given requirements file does not exist.')
 
-        for requirement in parse_requirements(reqfile):
+        finder = PackageFinder([], [])
+        for requirement in parse_requirements(reqfile, finder=finder):
             if requirement.req:
                 self.requirements.append(requirement.req)
 
 
     def diff(self, requirements, ignore_versions=False):
         r1 = self

```

似乎是需求文件语法分析器会接收一个参数，为了防止接收参数错误，模拟了一个数据结构合法的假参数用来在参数列表中占位

*又学到一个新词汇，dommy 假，蜡像，佯装的*

pip-grep也做了类似的修改，通过git记录可以看到，不再赘述。

## Merge pull request #2 from dangra/1-add-finder

上一个修改被接受，开了个分支合并过来测试一下。

## Merge remote-tracking branch 'origin/master'

应该是测试通过合并到主分支。

## support for latest pip

支持最新版本pip

```shell
  --stale       List removed packages.
"""
import os
from docopt import docopt
from pip.req import parse_requirements
from pip.index import PackageFinder
from pip._vendor.requests import session # new import

requests = session() # + new line

class Requirements(object):
    def __init__(self, reqfile=None):
        super(Requirements, self).__init__()
        self.path = reqfile
        self.requirements = []

```

```shell
 
     def load(self, reqfile):
 
         if not os.path.exists(reqfile):
             raise ValueError('The given requirements file does not exist.')
 
-        finder = PackageFinder([], [])
+        finder = PackageFinder([], [], session=requests)
         for requirement in parse_requirements(reqfile, finder=finder):
             if requirement.req:
                 self.requirements.append(requirement.req)
 
 
     def diff(self, requirements, ignore_versions=False):

```

作用的位置是之前的dummy 佯装参数。

*同样不知道是做什么的*

## Update pip-grep

感觉就是提交了一个空的commit，然后建立了分支。

## Add option to print the requirement, if found.

```shell
 #!/usr/bin/env python
 # -*- coding: utf-8 -*-
 
 """Usage:
-  pip-grep [-s] <reqfile> <package>...
+  pip-grep [-sp] <reqfile> <package>...
 
 Options:
-  -h --help     Show this screen.
+  -h --help         Show this screen.
+  -s --silent       Suppress output.
+  -p --print-req    If found, print the requirement.
 """
 import os
 from docopt import docopt
 from pip.req import parse_requirements
 from pip.index import PackageFinder

```

更新了doc部分，准备添加新的mode，输出模式 print option

```shell
-def grep(reqfile, packages, silent=False):
+def grep(reqfile, packages, silent=False, print_req=False):
 
     try:
         r = Requirements(reqfile)
     except ValueError:
 
         if not silent:
-            print('There was a problem loading the given requirement file.')
+            print('There was a problem loading the given requirements file.')
 
         exit(os.EX_NOINPUT)
 
     for requirement in r.requirements:
 
         if requirement.req:
 
             if requirement.req.project_name in packages:
 
-                if not silent:
+                if print_req:
+                    print(str(requirement.req))
+                elif not silent:
                     print('Package {} found!'.format(requirement.req.project_name))
-
                 exit(0)
 
     if not silent:
-        print('Not found.'.format(requirement.req.project_name))
+        print('Not found.')
 
     exit(1)
 
 
 def main():
     args = docopt(__doc__, version='pip-grep')
 
-    kwargs = {'reqfile': args['<reqfile>'], 'packages': args['<package>'], 'silent': args['-s']}
-
+    kwargs = {'reqfile': args['<reqfile>'], 'packages': args['<package>'],
+              'silent': args['--silent'], 'print_req': args['--print-req']}
 
     grep(**kwargs)

```

和寂静模式类似，控制输出。

## Merge pull request #3 from thenovices/print-line

分支合并

## Update from python buildpack

python buildpack是什么？

print option被移除了。

```shell
         finder = PackageFinder([], [], session=requests)
-        for requirement in parse_requirements(reqfile, finder=finder):
+        for requirement in parse_requirements(reqfile, finder=finder, session=requests):

```

比较困惑的是这个地方，session=requests，在finder里添加了一次，在parse中又被当做参数添加了一次。

## exclude in pip-pop

```shell
 #!/usr/bin/env python
 # -*- coding: utf-8 -*-
 
 """Usage:
-  pip-diff (--fresh | --stale) <reqfile1> <reqfile2>
+  pip-diff (--fresh | --stale) <reqfile1> <reqfile2> [--exclude <package>...]
   pip-diff (-h | --help)
 
 Options:
   -h --help     Show this screen.
   --fresh       List newly added packages.
   --stale       List removed packages.
```

添加了一个新的参数列表，在列表中的package对比的时候将会被排除。fresh和stale都不会显示。

```shell
-    def diff(self, requirements, ignore_versions=False):
+    def diff(self, requirements, ignore_versions=False, excludes=None):

```

默认关闭。

```shell
-            if r not in other_reqs:
+            if r not in other_reqs and r not in excludes:
                 results['fresh'].append(req)

```

```shell
-            if r not in other_reqs:
+            if r not in other_reqs and r not in excludes:
                 results['stale'].append(req)

```

```shell
    include_versions = True if include_stale else False
    excludes = excludes if len(excludes) else []

```

将None处理成空的列表。

```shell
     args = docopt(__doc__, version='pip-diff')
 
     kwargs = {
         'r1': args['<reqfile1>'],
         'r2': args['<reqfile2>'],
         'include_fresh': args['--fresh'],
-        'include_stale': args['--stale']
+        'include_stale': args['--stale'],
+        'excludes': args['<package>']
     }
 
     diff(**kwargs)

```

命令行参数修改。

## README.rst: Fix spelling error

```shell
-Takes a requirements file, and searches for the speficied package (or packages) within it.
+Takes a requirements file, and searches for the specified package (or packages) within it.

```

拼写错误

## Merge pull request #6 from msabramo/patch-1

合并fix分支

## update

一个兼容性的处理，pip 8.1.2 之前的版本 没有name属性。使用project_name为name赋值。

```shell
            if requirement.req:
                if not getattr(requirement.req, 'name', None):
                    # Prior to pip 8.1.2 the attribute `name` did not exist.
                    requirement.req.name = requirement.req.project_name

```

```shell
         # Generate fresh packages.
         other_reqs = (
-            [r.project_name for r in r1.requirements]
+            [r.name for r in r1.requirements]
             if ignore_versions else r1.requirements
         )
 
         for req in r2.requirements:
-            r = req.project_name if ignore_versions else req
+            r = req.name if ignore_versions else req
 
             if r not in other_reqs and r not in excludes:
                 results['fresh'].append(req)
 
         # Generate stale packages.
         other_reqs = (
-            [r.project_name for r in r2.requirements]
+            [r.name for r in r2.requirements]
             if ignore_versions else r2.requirements
         )
 
         for req in r1.requirements:
-            r = req.project_name if ignore_versions else req
+            r = req.name if ignore_versions else req

```

```shell
     results = r1.diff(r2, ignore_versions=True, excludes=excludes)
 
     if include_fresh:
         for line in results['fresh']:
-            print(line.project_name if include_versions else line)
+            print(line.name if include_versions else line)
 
     if include_stale:
         for line in results['stale']:
-            print(line.project_name if include_versions else line)
-
+            print(line.name if include_versions else line)
 

```

## v0.1.1

```shell
-    version='0.1.0',
+    version='0.1.1',
```

没有增加新功能，修复了一些问题。3号位的版本号加了1。

## update setup.py

```shell
-        'Development Status :: 4 - Beta',
-        #'Development Status :: 5 - Production/Stable',
+        # 'Development Status :: 4 - Beta',
+        'Development Status :: 5 - Production/Stable',
         #'Development Status :: 6 - Mature',
         #'Development Status :: 7 - Inactive',
         'Programming Language :: Python',
         'Programming Language :: Python :: 2',
         #'Programming Language :: Python :: 2.3',
         #'Programming Language :: Python :: 2.4',
         #'Programming Language :: Python :: 2.5',
         'Programming Language :: Python :: 2.6',
         'Programming Language :: Python :: 2.7',
-        #'Programming Language :: Python :: 3',
+        'Programming Language :: Python :: 3',
         #'Programming Language :: Python :: 3.0',
         #'Programming Language :: Python :: 3.1',
         #'Programming Language :: Python :: 3.2',
         #'Programming Language :: Python :: 3.3',
+        'Programming Language :: Python :: 3.4',
+        'Programming Language :: Python :: 3.5',

```

不清楚setup.py的规则。

## Require pip>=1.5.0

```shell
-    install_requires=['docopt'],
+    install_requires=['docopt', 'pip>=1.5.0'],

```

要求

## Remove unused wsgiref from requirements.txt

```shell
  docopt==0.6.2
- wsgiref==0.1.2

```

移除了没有用到的wsgiref

## Add a tox config and some very primitive pip-grep and pip-deff tests

*primitive 原始的，简陋的*

关于tox，python中用于测试代码在不同环境下的兼容性。在不同的环境下进行暴力尝试，反馈哪个不行~

[Huang Huang 的博客 《[python]使用 tox 测试代码在不同环境下的兼容性》](https://mozillazg.github.io/2014/07/python-use-tox-test-code.html)

```shell
.
├── LICENSE
├── README.rst
├── bin
│   ├── pip-diff
│   └── pip-grep
├── requirements.txt
├── setup.py
├── tests
│   ├── test-requirements.txt
│   └── test-requirements2.txt
└── tox.ini

2 directories, 9 files

```

```shell
(vir_2_pip-pop)qduccdeiMac:pip-pop qducc$ ls -a
.                ..               .git             .gitignore       .idea            LICENSE          README.rst       bin              requirements.txt setup.py         tests            tox.ini

```

创建了.gitinore 忽略文件

```txt
/.tox
/*.egg-info
*.pyc
```

[zhihu Python什么情况下会生成pyc文件](https://www.zhihu.com/question/30296617)

tox.ini

```Ini
[tox]
# To reduce the size of the test matrix, tests the following pip versions:
#  * all of the latest major version releases (8.x)
#  * only the first and last point release of each older major version
#  * plus the latest distro version available via LTS Ubuntu package managers:
#    (1.5.4 for Trusty, 8.1.1 for Xenial).
envlist = pip{154,156,600,611,700,712,800,801,802,803,810,811,812}

[testenv]
# Speeds up pip install and reduces log spam, for compatible versions of pip.
setenv = PIP_DISABLE_PIP_VERSION_CHECK=1

deps =
    pip154: pip==1.5.4
    pip156: pip==1.5.6
    pip600: pip==6.0.0
    pip611: pip==6.1.1
    pip700: pip==7.0.0
    pip712: pip==7.1.2
    pip800: pip==8.0.0
    pip801: pip==8.0.1
    pip802: pip==8.0.2
    pip803: pip==8.0.3
    pip810: pip==8.1.0
    pip811: pip==8.1.1
    pip812: pip==8.1.2

# TODO: Replace with something like https://scripttest.readthedocs.io or else
# rework the pip-grep and pip-diff scripts so they can more easily be unit-tested.
commands =
    pip-grep tests/test-requirements.txt cffi
    pip-grep tests/test-requirements.txt requests
    pip-diff --fresh tests/test-requirements.txt tests/test-requirements2.txt
    pip-diff --stale tests/test-requirements.txt tests/test-requirements2.txt
```

```python
envlist # 需要测试的版本
setenv # 测试的依赖环境，想测试版本pip 1.5.4当然需要安装这个版本
commands # 需要测试的命令
```

```python
    tests_require=['tox'],
```

修改了setup.py

添加了测试用的两个需求文件。

更新了说明文件。

## Add Travis config

关于Travis

[wiki Travis CI](https://zh.wikipedia.org/wiki/Travis_CI)

Travis CI是在软件开发领域中的一个在线的，分布式的持续集成服务，用来构建及测试在GitHub托管的代码。这个软件的代码同时也是开源的，可以在GitHub上下载到，尽管开发者当前并不推荐在闭源项目中单独使用它。 [维基百科](https://zh.wikipedia.org/zh-cn/Travis_CI)

*Travis似乎在Github上会得到很好的支持，添加了这个文件后，提交代码后会尝试执行这个测试，如果测试不能通过会禁止提交因为有tox的存在所以脚本就是直接执行 -tox就可以确保代码能够稳定执行，这里涉及到CI，既持续集成的概念。*

## Update PyPI classifiters to reflect tested Python versions

更新PyPI分类器以反映测试的Python版本

```python
-        #'Programming Language :: Python :: 2.3',
-        #'Programming Language :: Python :: 2.4',
-        #'Programming Language :: Python :: 2.5',
-        'Programming Language :: Python :: 2.6',
         'Programming Language :: Python :: 2.7',
-        #'Programming Language :: Python :: 3',
-        #'Programming Language :: Python :: 3.0',
-        #'Programming Language :: Python :: 3.1',
-        #'Programming Language :: Python :: 3.2',
-        #'Programming Language :: Python :: 3.3',
+        'Programming Language :: Python :: 3',
+        'Programming Language :: Python :: 3.4',
+        'Programming Language :: Python :: 3.5',
+        'Programming Language :: Python :: Implementation :: CPython',
+        'Programming Language :: Python :: Implementation :: PyPy',

```

*依旧不知道他在干什么？ (摊手)*

## Merge pull request #11 from edmoriey/add-travis-testings

将测试合并到分支

## empty commit

空提交

*结束*

## **Reference**

小赖的Python学习记笔记  docopt - 创建漂亮的命令行交互界面

From https://wp-lai.gitbooks.io/learn-python/content/0MOOC/docopt.html

Retrieved 2017-10-12 



……



后续将完善此处的引用，大多都没有申请许可，因为文中没有涉及转载和几乎没有文字引用，如果有涉及版权问题请联系我会及时修改。

— EOF —  