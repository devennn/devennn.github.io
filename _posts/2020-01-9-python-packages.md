---
layout: post
title: Python package and the usage of __init__.py
---

Python packages make project more organized. Whenever a folder need to be declared as package, \_\_init\_\_.py need to be included. Take example of directory below.

```python
main/
├── run_this.py
├── first/
│   ├── a.py
│   ├── b.py
│   ├── __init__.py
│   └── d.py
└── second/
    ├── c.py
    └── __init__.py
```
The example shown use run_this.py as the main script. It use few components from package first/ and second/

In run_this.py
~~~python
# run_this.py
import first
from second import c

def main():
    ans = first.calc_divide(a=4, b=3)
    print('calc_divide %d' %ans)

    ans = first.calc_plus(a=4, b=3)
    print('calc_plus %d' %ans)

    ans = c.calc_minus(a=5, b=4)
    print('calc_minus %d' %ans)

    ans = first.calc_add_minus(a=5, b=1)
    print('calc_add_minus %d' %ans)

if __name__ == '__main__':
    main()

~~~

In a.py
```python
# a.py
def calc_plus(a, b):
    ans = a + b
    return ans
```
 
In b.py
```python
# b.py
import math

def calc_divide(a, b):
    ans = math.ceil(a / b)
    return ans
```

In d.py
```python
# d.py
import sys
sys.path.append("..")
from second.c import calc_minus

def calc_add_minus(a, b):
    ans = a + b
    ans = calc_minus(a=ans, b=0)

    return ans
```

In first/ \_\_init\_\_.py
 ```python
from .a import *
from .b import *
from .d import *
 ```
Note: .module_name need to be present to statisfy Python 3 relative imports rule

In c.py
```python
def calc_minus(a, b):
    ans = a - b
    return ans
```

second/ \_\_init\_\_.py is empty. Run ```python run_this.py``` from main/ and the outputs are
```python
calc_divide 2
calc_plus 7
calc_minus 1
calc_add_minus 6
```

### Explanation
To use functions from a.py, b.py and d.py, I kind of 'skip' the usual import such as

> from first.a import *

or

> from first import a

> a.calc_plus(a=4, b=3)

Instead, I imported them the init. This is common to do inside \_\_init\_\_.py. It will make the package first/ to be treated as a module having all the functions.

Conclusion:
- In bigger package, Classes are commonly imported at \_\_init\_\_.py to avoid messy import from another script and improve readibility.
- It can be empty. Having empty \_\_init\_\_.py will declare the Folder as package.

Obtain code used in this blogpost [here](https://github.com/devennn/blog-code/tree/master/package)
