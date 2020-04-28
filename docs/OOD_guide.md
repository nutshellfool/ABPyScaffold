# Object Oriented Design

## Accessing Method from Superclasses

The Python official documentation lists super as a built-in function. 
But it's a built-in type, even if it is used like a function.

```shell
>>> super
<class 'super'>
```

### usage of access superclass method

legacy way:

```python

class Cat:
    def says(self):
        print('mio')

class Tiger(Cat):
    def says(self):
        Cat.says(self)
        print('wow')

```

super usage:



```python

class Cat:
    def says(self):
        print('mio')

class Tiger(Cat):
    def says(self):
        super(Tiger, self).says()
        # super().says()
        print('wow')

```

equivalence way

```python

class Cat:
    def says(self):
        print('mio')

class Tiger(Cat):
    def says(self):
        super().says()
        print('wow')

```


### Method Resolution Order(MRO)

Above case is simple usage of single inheritance schema, As for Python support multiple inheritance,   
super becomes hard to be used. To understanding these problem, understanding how MRO works in Python is important.   

Python 2.3 Added a new MRO base on C3 algorithm, which builds the ordered list (precedence)of the ancestors. 
This list is used to seek an attribute. 

Old MRO take "left-to-right depth" rule to treat multiple inheritance.  
New MRO ([C3 linearization algorithm](https://en.wikipedia.org/wiki/C3_linearization))

C3 solved what old MRO problem?  
"left-to-right depth first" algorithm act as a wired role under this case:  

```python
class A:
    def say(self):
        print('Base A say')

class B(A):
    pass

class C(A):
    def say(self):
        print('C say')

class D(B, C):
   pass

```

As for old MRO, the `D().say()` output will be  

```PlainText
Base A Say
```

wired output, right?   

```PlainText
C say
``` 
seems more reasonable.

C3 linearization take symbolic notation applied to above example:

L[D(B(A), C(A))] = D + merge(L[B(A)], L[C(A)], B, C) = D + merge([B, A], [C, A], B, C) = [D, B, C, A]

As for the MRO result list, you can get it by call `Class.mro()` 
For above example :

```PlainText
>>> D.mro()
[<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>]
```

### super Pitfalls & Best Practices

#### Mix use super and classic call

A mix use super and classic call `__init__(self)` example:  

```python
class A:
    def __init__(self):
        print('A')
        super().__init__()

class B:
    def __init__(self):
        print('B')
        super().__init__()

class C(A, B):
    def __init__(self):
        print(C)
        A.__init__(self)
        B.__init__(self)

```

After call `C()`, the B init will be called twice, like following output:  

```plain Text
>>> C()
C A B B <__main_.c object at 0x0000FFFFF>
```

the mro of C is :

```plain Text
>>> C.mro()

[<class 'C'>, <class 'A'>, <class 'B'>, <class 'object'>]
```

just because the in class C `__init__` method, A.__init__(self) is called,
the super(A, self).__init__() called B.__init__(). 

#### Best practices

* Multiple inheritance should be avoided
* super usage has to be consistent
In a class hierarchy, super should use everywhere or nowhere
* don not mix old-style and new-style  
if you designed class should run both Python2 and Python3, please explicit inherit object.
* Class hierarchy has to be looked over when a parent class is called
call `mro()` method before call parent class

## Descriptors and Properties

### Descriptors

private property definition: 

* use `_` prefix not `__`

prefix `__` will call name mangling algorithm 

As for name mangling example:  
```python

class MyClass(object):
    __private_value = 1
    

instance_myclass = MyClass()
instance_myclass._MyClass__private_value
``` 
`<instance>._CLASSNAME__private_value` will get the name mangling property

### Properties

## Slots

if you want to static attribute list, 
and skip the creation of the `__dict__` list in each instance of the class.

The answer is `__slot__` property

Example :

```python
class WeekDay(object):
    __slots__ = ['monday', 'tuesday', 'wednesday', 'tuesday', 'friday', 'saturday', 'sunday']

instance_weekday = WeekDay()
instance_weekday.friday = 1
instance_weekday.whateverday = 1

```

```PlainText

Traceback (most recent call last):
  File "<input>", line 6, in <module>
AttributeError: 'WeekDay' object has no attribute 'whateverday'
```

## Meta-programming
 
Meta programming involve two level operation on instance and class:  

* introspection
* reflection

### type introspection

The ability of a program to examine the type or properties of an object at runtime. 

#### introspection method

* `dir()`

* `type()`

* `hasattr()`

* `isinstance()` 

### reflection

Step further than introspection, 
the ability of a program to manipulate the values, metadata, properties and the function of an object at runtime.

### __new__ Method

A static method that called before `__init__()` initialized method,
 this feature is commonplace for subclass immutable build-in class(for example: int float str frozenset ...).
 
```python
class NoneZero(int):
    def __new__(cls, value):
        return super().__new__(cls, value) if value != 0 else None
        
    def __init__(self, value):
        # Here you do NOT need to override __init__ method,
        # this is just for illustrate the __init__ call lifecycle
        print('__init__ called {}'.format(value) )
        super().__init__()

```

```PlainText
>>> type(NoneZero(6))
__init__ called 6
<class 'NoneZero'>
>>> type(NoneZero(0))
<class 'NoneType'>
```

As you can see, when `__new__` method do NOT return the Type, the `__init__` method do NOT call.


Actually the `__new__` method feature can be implemented by factory design pattern.

### __metaclass__ Method

#### what is meta class

in short : "class' class" 

As you know, a instance is `instanceof` of class, and a class is `instanceof` what?

In Python the answer is `type`.  

Here is an example code showing how to define a class in other way.

the ordinary way of define a class

```python

class MyClass:
    data = 1

```
```PlainText
>>> instance = MyClass()
>>> MyClass, instance
(<class 'MyClass'>, <MyClass object at 0x10e5a9978>)
>>> type(MyClass)
<class 'type'>
>>> type(instance)
<class 'MyClass'>
>>> instance.data
1
```


```python
MyClass1 = type('MyClass1', (), {'data': 1})

```

```PlainText
>>> instance = MyClass1()
>>> MyClass1, instance
(<class 'MyClass1'>, <MyClass1 object at 0x10ebaa080>)
>>> instance.data
1
```

#### metaclass inherit type

```python

class Metaclass(type):
    def __new__(cls,name, bases, namespace):
        return super().__new__(cls, name, )

```

#### `__metaclass__` in Python3



## References

* [Wikipedia C3 linearization](https://en.wikipedia.org/wiki/C3_linearization)
* [The Python 2.3 Method Resolution Order](https://www.python.org/download/releases/2.3/mro/)
* [Guido van Rossum - Method Resolution Order](http://python-history.blogspot.com/2010/06/method-resolution-order.html)
* [expert python programming](https://www.packtpub.com/application-development/expert-python-programming-third-edition)
* [Wikipedia type introspection](https://en.wikipedia.org/wiki/Type_introspection)
* [Wikipedia reflection]()
