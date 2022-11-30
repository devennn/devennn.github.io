---
layout: post
title: Make Python list process faster with threading with return value
---

Let's define a long process. This function will sleep according to the time specified and return the value.

```python
def long_process(arg):
  time.sleep(arg)
  return "Sleep time {}".format(arg)
```

If we run 3 times using for loop

```python
sleep_time_list = (5, 5, 5)

for arg in sleep_time_list:
  output.append(long_process(arg))
```
or list comperhension

```python
output = [long_process(arg) for arg in sleep_time_list]
```
we get around 15 seconds. That's predictable. Since both processes are sequential.

How to make a loop faster? Use threading. All process run concurrently which make it faster. 

```python
import concurrent.futures

with concurrent.futures.ThreadPoolExecutor() as executor:
  futures = [executor.submit(long_process, (arg)) for arg in sleep_time_list]
  output = [f.result() for f in futures]
```

The result is 5 seconds for the whole process. Awesome! 
concurrent.futures is a standard Python library that uses threads to execute processes.

The interface is easy to apply, and you can get the return value without lots of setup.
