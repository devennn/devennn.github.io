---
layout: post
title: Python logging module
---

One of good debuging practice will be to test the theory. One of the testing method is to see the output. Function like print can be used. However, in large program, using print is not "developer fiendly" since it has to be placed and removed manually. 

Python has a built-in module called """Logging""" that can be used to solve the problem. Common benefits of logging are:
- Able to set logging level. Don't have to delete log function if not used. 
- Saving log messages to file. Useful if program is printing output to console. Preventing mixed message.

### Logging message level

| Level    | Function           |
|----------|--------------------|
| Critical | logging.critical() |
| Warning  | logging.warning()  |
| Error    | logging.error()    |
| Info     | logging.info()     |
| Debug    | logging.debug()    |

I usually use logging.info to confirm the flow of program, checking exit status and for other normal operation monitoring. I will put logging.debug at critical function entry to monitor the input and will only be activated if function is not outputing expected value. Logging.warning is used to monitor unexpected behavior and logging.critical is used to catch fatal that will make the program break or unable to continue.

### Setting up basic Logger

```python
import logging
logging.basicConfig(level=logging.INFO)

def add_this(a, b):
    logging.debug("In function add this debug")
    logging.info("In function add this info")
    logging.error("In function add this error")
    logging.warning("In function add this warning")
    logging.critical("In function add this critical")
    return a + b

logging.info("Start program")
ans = add_this(1, 3)
logging.info(" Ans is {}".format(ans))
```
Running this will output

```
INFO:root:Start program
INFO:root:In function add this info
ERROR:root:In function add this error
WARNING:root:In function add this warning
CRITICAL:root:In function add this critical
INFO:root: Ans is 4
```
if level is changed to logging.CRITICAL it will output

```
CRITICAL:root:In function add this critical
```

This is useful to decide what level of log to show for cleaner and informative output.

### Example of multiple program logging
