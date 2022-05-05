# Class


## class 默认变量

### \_\_slots__

__slots__允许我们声明并限定类成员，并拒绝类创建__dict__和__weakref__属性以节约内存空间。

Python是动态语言，对于普通的类，可以为类实例赋值任何属性，这些属性会存储在__dict__中:
```
>>> class Student(object):
...     pass
...     
>>> Abey = Student()
>>> Abey.name = 'Abey'
>>> Abey.__dict__
{'name': 'Abey'}
```

这样的特性带来两个问题：
- 数据通过字典(Hash)存储所占用的空间较大
- 如何禁止随意生成类属性


**用法:**
代码示例:
``` python
>>> class Student(object):
...     __slots__ = ('name', 'age')
...     
>>> Abey = Student()
>>> Abey.name = 'Abey'
>>> Abey.gender = 'Female'
Traceback (most recent call last):
  File "<input>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'gender'
>>> Abey.__dict__
Traceback (most recent call last):
  File "<input>", line 1, in <module>
AttributeError: 'Student' object has no attribute '__dict__'
```

**继承:**  
__slots__在继承中有两种表现：
- 子类未声明__slots__时，不继承父类的__slots__，即此时子类实例可以随意赋值属性
- 子类声明__slots__时，继承父类的__slots__，即此时子类的__slots__为其自身+父类的__slots__


