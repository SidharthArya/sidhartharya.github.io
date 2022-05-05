# Linux


## Init Systems {#init-systems}


### Systemd {#80fc7554-7d85-4d9f-925a-1c61ddf8aec1}


#### Tricks {#tricks}

-   Enable user services on boot

    ```bash
    sudo loginctl enable-linger username
    ```
-   If a file changes execute a command

    ```bash
    while inotifywait -e close_write *.tex;
    do pdflatex main.tex && bibtex main && pdflatex main.tex;
    done
    ```
