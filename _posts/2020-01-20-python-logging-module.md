---
layout: post
title: Python logging module
---

Python built-in module called ```Logging``` can be used to log everything that needs to be recorded. Common benefits of logging are:
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

I usually use logging.info to confirm the flow of program, checking exit status and for other normal operation monitoring. logging.debug at critical function entry to monitor the input and will only be activated if function is not outputing expected value. Logging.warning is used to monitor unexpected behavior and logging.critical is used to catch fatal error that will make the program break or unable to continue.

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
```
.
├── file1.py
├── file2.py
├── logfile.log
└── set_logger.py
```
In this example file1 is the main dirver to run function from file2. set_logger is use to setup the logger.

In file1.py
```python
import logging
from set_logger import set_logger
from file2 import run_file2

logger = set_logger(level=logging.CRITICAL)

# Logs
logger.debug('A debug in file1')
logger.info('An info in file1')
logger.warning('Something is not right in file1')
logger.error('A Major error has happened in file1')
logger.critical('Fatal error. Cannot continue in file1')

run_file2()
```

In file2.py
```python
from set_logger import set_logger

logger = set_logger()

def run_file2():
    # Logs
    logger.debug('A debug in file2')
    logger.info('An info in file2')
    logger.warning('Something is not right in file2')
    logger.error('A Major error has happened in file2')
    logger.critical('Fatal error. Cannot continue in file2')
```
set_logger is called to setup the log settings. 

In set_logger.py
```python
import logging

def set_logger(level=logging.WARNING):
    FORMAT = '%(asctime)s : %(levelname)s : %(name)s : %(message)s'
    logging.basicConfig(format=FORMAT)
    # Gets or creates a logger
    logger = logging.getLogger(__name__)

    # set log level
    logger.setLevel(level=level)

    # define file handler and set formatter
    formatter = logging.Formatter(FORMAT)
    file_handler = logging.FileHandler('logfile.log')
    file_handler.setFormatter(formatter)

    # add file handler to logger
    logger.addHandler(file_handler)

    return logger
```
FORMAT set the logging message format to desired output.

Using ```__name__``` will automatically set the name of logger according to the name of module. In this case, it will be set_logger. This is useful when setting up logger for different module.

To save the output, create file using FileHandler. setFormatter will change the format and addHandler will make the logger channel the output to specified file ```logfile.log```

The output will be 
```
2020-01-20 21:18:46,757 : CRITICAL : set_logger : Fatal error. Cannot continue in file1
2020-01-20 21:18:46,759 : CRITICAL : set_logger : Fatal error. Cannot continue in file2
```
and logfile.log containing the same output is created.

Conclusion:
- Using logging module for debugging or recording message bring more benefits than print
- ```__name__``` can be useful for multi module logging

Refer [logging official docs](https://docs.python.org/3/library/logging.html) for more features and examples
Obtain code used in blogpost [here](https://github.com/devennn/blog-code/tree/master/logger)
