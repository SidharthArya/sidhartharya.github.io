# Python


## [Threading]({{< relref "20210727104426-threading.md" >}}) {#threading--20210727104426-threading-dot-md}

Python threading allows you to have different parts of your program
run concurrently and can simplify your design.

```python
import logging
import threading
import time

def thread_function(name):
    logging.info("Thread %s: starting", name)
    time.sleep(2)
    logging.info("Thread %s: finishing", name)

if __name__ == "__main__":
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO,
                        datefmt="%H:%M:%S")

    logging.info("Main    : before creating thread")
    x = threading.Thread(target=thread_function, args=(1,))
    logging.info("Main    : before running thread")
    x.start()
    logging.info("Main    : wait for the thread to finish")
    # x.join()
    logging.info("Main    : all done")

```


## Core {#core}


### Static Variables inside functions {#static-variables-inside-functions}

```python
def somefunction():
    somefunction.x = 1;  # A static variable
```


## Tricks {#tricks}


### Import module form file {#import-module-form-file}

```python
from pydoc import importfile
module = importfile('/path/to/module.py')
```
