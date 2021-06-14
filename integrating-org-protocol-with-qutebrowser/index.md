# Integrating Org Protocol with Qutebrowser


I don't have a good introduction today. So let's just get to the post.

Org mode provides something called org protocol in order to let other applications be able to pass their data into the emacs server instance. We exploit this feature of emacs to write a little script which can capture stuff in org mode and store links from the browser.


## Org Protocol {#org-protocol}

_"org-protocol intercepts calls from emacsclient to trigger custom actions without external dependencies."_ From org protocol documentation

Basically we can send some information through the org protocol to emacs server.

Make sure you start an emacs server in your config somewhere, since org protocol requires a server.

```emacs-lisp
(server-start)
(require 'org-protocol)
```

`org-protocol` is not loaded by default with org. So make sure you require it explicitely too.


## Adding a handler for org protocol {#adding-a-handler-for-org-protocol}

You need to add the config given below to `~/.local/share/applications/org-protocol.desktop`.

```conf
[Desktop Entry]
Name=Org-Protocol
Exec=emacsclient %u
Icon=emacs-icon
Type=Application
Terminal=false
MimeType=x-scheme-handler/org-protocol
```

All it says is, if there is an address we want to open that begins with `org-protocol:/` redirect it to the emacsclient. But in case emacs server is not open or there is no handler for the requested protocol, the url will be handled by the next defined handler which would be a web browser. So make sure your emacs server is running. You can find the available org protocols in the variable `org-protocol-protocol-alist` or `org-protocol-protocol-alist-default`.


## Org Protocol Script {#org-protocol-script}

Save the script given below to `~/.config/qutebrowser/userscripts/org-protocol` or `~/.local/share/qutebrowser/userscripts/org-protocol`. And change permission to executable by `chmod +x ~/.config/qutebrowser/userscripts/org-protocol`.

```bash
#!/bin/sh
PROTOCOL="$1"
TEMPLATE="$2"
QUTE_URL=$(echo $QUTE_URL |python -c "import sys;from urllib.parse import quote;print(quote(sys.stdin.readline()))")

OUT="org-protocol:/$PROTOCOL"
OUT+="?"
if [[ "$PROTOCOL" == "capture" ]];
then
   OUT+="template=$TEMPLATE"
   OUT+="&"
fi

OUT+="url=$QUTE_URL&title=$QUTE_TITLE&body=$QUTE_SELECTED_TEXT"
WINDOW=$(xdo id -N Emacs)
#bspc node $WINDOW -g hidden=off
xdo activate $WINDOW
xdg-open "$OUT"
# notify-send "$OUT"
```

This script does require `xdo` by baskerville. You should probably uncommend bspc line if you are using `bspwm`.


## Emacs Configuration {#emacs-configuration}

I recommend adding a separate category for protocol in capture templates if you are going to be using org-capture protocol. One capture template that i have for org-protocol is given below.

```emacs-lisp
(setq org-capture-templates
'(("P" "Protocol")
("Pi" "Important")
("Pit" "Today" entry (file+headline "~/Documents/Org/Agenda/notes.org" "Websites")
 "* TODO %:annotation \t:important:\n\tSCHEDULED:%(org-insert-time-stamp (org-read-date nil t \"\"))\n:PROPERTIES:\n:Effort: 1h\n:SCORE_ON_DONE: 30\n:END:\n  %i\n  %a")))
```


## Browser Configuration {#browser-configuration}

Now that we have setup everything for the userscript to work. All we have to do is add the corresponding keybindings to qutebrowser.

```python
config.bind('cit', 'spawn --userscript org-protocol capture Pit')
config.bind('cl', 'spawn --userscript org-protocol store-link')
```


## Conclusion {#conclusion}

Now capturing to org mode from qutebrowser should be a breeze. Please leave your comments below if you face any issues.


## References {#references}

-   [qutebrowser | qutebrowser](https://qutebrowser.org/)
-   [Writing qutebrowser userscripts | qutebrowser](https://qutebrowser.org/doc/userscripts.html)
-   [Org mode for Emacs](https://orgmode.org/)
-   [GNU Emacs - GNU Project](https://www.gnu.org/software/emacs/)
-   [org-protocol.el – Intercept calls from emacsclient to trigger custom actions](https://orgmode.org/worg/org-contrib/org-protocol.html)
-   [MIME types (IANA media types) - HTTP | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics%5Fof%5FHTTP/MIME%5Ftypes)
