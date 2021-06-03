# Running ipython inside a python script


Imagine you are writing a very convoluted program with all sorts of threading magic. You want to continuously test various sections of your program before moving on with the next task. Would you rather write all the functions in the file at the same time and start debugging the code for errors, or would you rather fire up an ipython shell with your program loaded up and available to you?


## An interactive shell within your script {#an-interactive-shell-within-your-script}

I would rather do the later. IPython makes it incredibly simple to test your code from time to time.
This is a fairly basic thing to do but i find it incredibly useful. In my case, i like having a shell which i can interact, if for example i am algotrading. A simple pause function can be triggered within ipython, and manual trading can be done before switching back to algotrading.

```python
# Some Threaded Code running in the background
import IPython
iPython.embed()
```

And you have an ipython shell running.

On a similar note, your problem may require to run the code sequentially, rather than in parallel. In that case you can start ipython first, in a thread.

```python
# Put this at the top of your program
import IPython
import threading
ipython = threading.Thread(target=IPython.embed)
ipython.start()

# Follow up with rest of your program
```

`embed` launch the shell in a particular calling scope, such as inside a function or method.


## References {#references}

-   [IPython — IPython 7.24.1 documentation](https://ipython.readthedocs.io/en/stable/api/generated/IPython.html)

