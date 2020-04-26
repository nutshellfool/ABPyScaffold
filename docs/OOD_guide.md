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

#### introspection Descriptor

#### Meta-descriptor

### Properties

## Slots

## Meta-programming
 
### __new__ Method

### __metaclass__ Method


## References

* [wikipedia C3 linearization](https://en.wikipedia.org/wiki/C3_linearization)
* [The Python 2.3 Method Resolution Order](https://www.python.org/download/releases/2.3/mro/)
* [Guido van Rossum - Method Resolution Order](http://python-history.blogspot.com/2010/06/method-resolution-order.html)
* [expert python programming](https://www.packtpub.com/application-development/expert-python-programming-third-edition)