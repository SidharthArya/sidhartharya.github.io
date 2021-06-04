# A quicker way to reach localhost with qutebrowser


## Qutebrowser {#qutebrowser}

[Qutebrowser](https://qutebrowser.org/) is the best web browser for developers, hands down. Nothing can compare to the flexibility it offers. Now, ofcourse other web browser must have ways of enabling and disabling various fancy features, but it's much harder to achieve.

I have a number of scripts that i use on a daily basis in qutebrowser which i maintain here ([dotfiles/Browser/Qutebrowser/userscripts at master · SidharthArya/dotfiles](https://github.com/SidharthArya/dotfiles/tree/master/Browser/Qutebrowser/userscripts)) on a daily basis.


## The Problem {#the-problem}

One of the issues i have sometimes is to reach localhost on various ports quickly. For example, i may have a hugo server running on port 1313 accessed as <https://localhost:1313> and i may have an [org-roam-server](https://github.com/org-roam/org-roam-server) running on port 8080 accessed as <https://localhost:8080>.

Using the regular history matching of a web browser may work, nonetheless, some developing environments have dynamic allocation of ports. So it may associate a port number at random with its services. In this specific case history matching does not work for me. Also history matching feels a lot slower in every browser compared to what we can do in qutebrowser.

So, what i do is i write a simple [qutebrowser userscript](https://qutebrowser.org/doc/userscripts.html) as follows:


## Solution {#solution}

1.  Create a file called localhost at ~/.local/share/qutebrowser/userscripts  or ~/.config/qutebrowser/userscripts.
2.  Write the code below in that file:

    ```bash
    #!/bin/bash
    if [ -z $QUTE_COUNT ];
    then
        QUTE_COUNT=8080
    fi
    echo open localhost:$QUTE_COUNT > $QUTE_FIFO
    ```
3.  Save the file and add a key combination for the script in your `config.py` usually placed at ~/.config/qutebrowser/config.py.

    ```python
    config.bind('zl', 'spawn --userscript localhost')
    ```


## Usage {#usage}

If you need to go to <https://localhost:8080>. Type 8080zl in normal mode in qutebrowser.
8000 is the default port and so just typing zl would take you to <https://localhost:8000>.


## References {#references}

-   [qutebrowser | qutebrowser](https://qutebrowser.org/)
-   [Writing qutebrowser userscripts | qutebrowser](https://qutebrowser.org/doc/userscripts.html)

