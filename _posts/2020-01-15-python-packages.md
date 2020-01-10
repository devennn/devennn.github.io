---
layout: post
title: Getting the python import right for packaging
publihsed: false
---

Whenever a directory need to be declared as package, \_\_init\_\_.py need to be included. Take example of directory below.

```
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
