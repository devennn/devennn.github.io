---
layout: post
title: Using Python Schedule library for readable cron job
description: "Using Python Schedule library for readable cron job"
keywords: "Python, Scheduler, Crontab, Cron"
---

Somehow in everyday development, there are some times when spinning up airflow is overkill to run a simple job. That is when cron jobs do the trick. However, using cron-related expressions is sometimes quite hard to understand. Schedule library aims to solve that by using a more human-readable way of scheduling. This is how to get started.

## Install
```bash
pip install schedule
```

## Basic structure
It requires 3 things to get started
1. Job to run
2. Scheduler trigger
3. Scheduler checker

2 and 3 have been provided by the library. 1 is the program that you want to run.

```python
import schedule
import time
from datetime import datetime

# Job to run
def job():
  print('This job run on {}'.format(datetime.now()))
  
if __name__ == "__main__":
  # Schedule trigger
  schedule.every(3).seconds.do(job)
  
  while True:
  
    # Scheduler checker
    schedule.run_pending()
    
    time.sleep(1)
```

Done! That's it. You can also run it with decorators. 

Explore various use cases from the documentation https://schedule.readthedocs.io/en/stable/examples.html
