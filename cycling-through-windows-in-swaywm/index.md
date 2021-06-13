# Cycling through Windows in SwayWM


Some time ago i tried [Swaywm](https://swaywm.org/), which is one of the top tiling window managers for linux and the only tiling window manager that works well enough on [Wayland](https://wayland.freedesktop.org/) (for my use case at the very least).

The only issue with it for me, was that i cannot cycle through windows like i would in any other window manager. Which was an issue for me only because

-   i planned to use multiple window managers from time to time, and the default behavior for most was cycling with super+Tab
-   I would occasionally use other systems that do not belong to me, and they have the same cycling behaviour.

Since Swaywm is very extensible. I wrote a little script to acheive the desired behaviour, as mentioned below.


## Script {#script}

Write the script below to a file called `~/.config/sway/alttab` and make it executable as `chmod +x ~/.config/sway/alttab`.

```python
#!/usr/bin/env python3

import sys
import json
import subprocess

direction=bool(sys.argv[1] == 't' or sys.argv[1] == 'T')
swaymsg = subprocess.run(['swaymsg', '-t', 'get_tree'], stdout=subprocess.PIPE)
data = json.loads(swaymsg.stdout)
current = data["nodes"][1]["current_workspace"]
workspace = int(data["nodes"][1]["current_workspace"])-1
roi = data["nodes"][1]["nodes"][workspace]
temp = roi
windows = list()

def getNextWindow():
    if focus < len(windows) - 1:
        return focus+1
    else:
        return 0

def getPrevWindow():
    if focus > 0:
        return focus-1
    else:
        return len(windows)-1

def makelist(temp):
    for nodes in "floating_nodes", "nodes":
        for i in range(len(temp[nodes])):
            if temp[nodes][i]["name"] is None:
                makelist(temp[nodes][i])
            else:
                windows.append(temp[nodes][i])

def focused(temp_win):
    for i in range(len(temp_win)):
        if temp_win[i]["focused"] == True:
           return i

makelist(temp)
# print(len(windows))
focus = focused(windows)
if str(sys.argv[1]) == 't' or str(sys.argv[1]) == 'T':
    print(windows[getNextWindow()]["id"])
else:
    print(windows[getPrevWindow()]["id"])
```


## Config {#config}

Add the lines given below to your `~/.config/sway/config`

```bash
bindsym $mod+tab exec swaymsg [con_id=$(swaymsg -t get_tree | ~/.config/sway/alttab t)] focus
bindsym $mod+shift+tab exec swaymsg [con_id=$(swaymsg -t get_tree | ~/.config/sway/alttab f)] focus
```

Now restart sway.
Cycling through windows should be working now.


## Note {#note}

There have been some excellent contributions to this, which can be found here: <https://gist.github.com/SidharthArya/f4d80c246793eb61be0ae928c9184406>


## References {#references}

-   [Sway](https://swaywm.org/)
