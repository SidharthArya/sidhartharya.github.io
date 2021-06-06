# Exporting Org Roam notes to hugo


Before i start talking gibrish let's give special credits to [jethrokuan (Jethro Kuan)](https://github.com/jethrokuan) for writing the [org-roam](https://github.com/org-roam/org-roam) package and [kaushalmodi (Kaushal Modi)](https://github.com/kaushalmodi) for [ox-hugo](https://github.com/kaushalmodi/ox-hugo) exporter.


## Introduction {#introduction}

[GNU Emacs](https://www.gnu.org/software/emacs/) is truly an amazing software. It can work as a text editor, a news feed reader, email reader, IDE, and much more. One of the truly amazing things i found on emacs was [Org mode](https://orgmode.org/). Org mode is really a markup language which has all sorts of amazing features. These amazing features can also be extended and hacked on very easily.

[Hugo](https://gohugo.io/) is a static site generator. It would basically take your content and based on some rules as defined by itself and a theme, it would generate a neat website.

[ox-hugo](https://github.com/kaushalmodi/ox-hugo) implements an exporter, which turns your org mode files into a markdown file, which is then used by hugo to generate the website.

Finally, [org-roam](https://github.com/org-roam/org-roam) extends the standard org mode to have a zettelkasten method like feel to it. Where you basically concentrate on writing your notes and you can make the necessary connections to other notes as and when you need to. You can also tag your files with various keywords. It's an amazing package, i urge you to visit its documentation since i may not have explained it very well. But, it's the most useful package for me.


## Disclaimer {#disclaimer}

I will assume that you already know how to use org roam and ox hugo here. Also, a better solution to the same problem can be found here: [jethrokuan/braindump: knowledge repository managed with org-mode and org-roam.](https://github.com/jethrokuan/braindump). And this one is from the original author of org-roam too, so kindly check that out too.
Please ensure you have the latest `ox-hugo`.


## Goal {#goal}

The goal is to create a framework which makes it simpler to export org roam files


## Configuring ox-hugo variables {#configuring-ox-hugo-variables}

Place a `.dir-locals.el` file in `org-roam-directory` with the content as given below.

```emacs-lisp
((nil . ((org-hugo-base-dir . "~/.blog")
         (org-hugo-section . "braindump"))))
```

-   `org-hugo-base-dir` is where you have your hugo project initialized
-   `org-hugo-section` defines in which section do you want to export your files to.
    For me i use two sections, one for blog posts and one form braindump from org-roam.


## Tag Processing {#tag-processing}

Adding the code given below adds the capability for ox-hugo to process your org-roam tags.

```emacs-lisp
(defun org-hugo--tag-processing-fn-roam-tags(tag-list info)
  "Process org roam tags for org hugo"
  (if (org-roam--org-roam-file-p)
      (append tag-list
              '("braindump")
              (mapcar #'downcase (org-roam--extract-tags)))
    (mapcar #'downcase tag-list)
    ))

(add-to-list 'org-hugo-tag-processing-functions 'org-hugo--tag-processing-fn-roam-tags)
```

Please note that you can skip line 5 if you don't want to add a braindump tag to it. But i do recommend adding some tag for classifying your roam files separately as well.
I use this tag in my hugo config to make sure i do not provide rss feeds for my roam files, since they are my unstructured personal notes. I downcase all tags usually to make sure i do not create extra tags because of capitalization issues. You can safely remove the `(mapcar #'downcase` bit of line 6 and 7 along with one closing bracket from the end of the line, if you prefer not to downcase.


## Backlinks Processing {#backlinks-processing}

```emacs-lisp
(defun org-hugo--org-roam-backlinks (backend)
  (when (equal backend 'hugo)
  (when (org-roam--org-roam-file-p)
    (replace-string "{" "")
    (replace-string "}" "")
    (end-of-buffer)
    (org-roam-buffer--insert-backlinks))))
(add-hook 'org-export-before-processing-hook #'org-hugo--org-roam-backlinks)
```

You can probably remove line 4 and 5, i use them since i use org-fc at places, on exporting those files, there are certain issues. But still particularly curly braces, either need to be escaped or removed.


## Auto export on save {#auto-export-on-save}

This is a simplified version of the function I use. If you do not want to export all its linked files as well everytime a file is updated, this much is sufficient.

```emacs-lisp
(defun org-hugo--org-roam-save-buffer(&optional no-trace-links)
  "On save export to hugo"
  (when (org-roam--org-roam-file-p)
      (org-hugo-export-wim-to-md)))
(add-to-list 'after-save-hook #'org-hugo--org-roam-save-buffer)
```


## Sync all org roam files {#sync-all-org-roam-files}

I used this function just once so far. But it is a needed function nonetheless.

```emacs-lisp
(defun my-org-hugo-org-roam-sync-all()
  ""
  (interactive)
  (dolist (fil (org-roam--list-files org-roam-directory))
    (with-current-buffer (find-file-noselect fil)
      (org-hugo-export-wim-to-md)
      (kill-buffer))))
```


## Misc {#misc}


### My Auto export on save {#my-auto-export-on-save}

It has a check for whether the org roam file i am saving is in the top directory of org-roam-directory or not. If it is then only it will be exported. This gives me a little privacy for stuff inside folders. This makes sure that my personal data or organizations data is not shared on my braindump.
no-trace-link is true if we do not want to trace the link of the file being exported. Since if you add links to an org roam file, you only need to update the backlinks to it's linked files, So that's exactly what we do.

```emacs-lisp
(defun org-hugo--org-roam-save-buffer()
  ""
  (when (org-roam--org-roam-file-p)
    (when (<= (length
               (split-string
                (replace-regexp-in-string (expand-file-name org-roam-directory) ""
                                          (expand-file-name (buffer-file-name org-roam-buffer--current))) "/")) 2)
      (unless no-trace-links
        (dolist (links (org-roam--extract-links))
          (with-current-buffer (find-file-noselect (aref links 1))
            (org-hugo--org-roam-save-buffer t)
            (kill-buffer))))
      (org-hugo-export-wim-to-md))))
(add-to-list 'after-save-hook #'org-hugo--org-roam-save-buffer)
```


### Redefining the backlinks function {#redefining-the-backlinks-function}

As i mentioned i keep the subdirectories private, and so i don't want them to be exported to hugo. This causes some issues. For now, i don't really need to create a link from top level to any subdirectory files. So the only thing i need to worry about are backlinks in top level directories. I work around them by tweaking the backlinks variable in the `org-roam-buffer--insert-backlinks`.
I prepend a my to it for the sake of not breaking the original function.

```emacs-lisp
(defun my-org-roam-buffer--insert-backlinks ()
  "Insert the org-roam-buffer backlinks string for the current buffer."
  (let (props file-from)
    (if-let* ((file-path (buffer-file-name org-roam-buffer--current))
              (titles (with-current-buffer (find-file-noselect file-path)
                        (org-roam--extract-titles)))
              (backlinks (delete 'nil
                                 (mapcar
                                  #'(lambda (a)
                                      (if (<= (length
                                               (split-string
                                                (replace-regexp-in-string
                                                 (concat
                                                  (expand-file-name org-roam-directory)) "" (car a)) "/")) 2) a))
                                  (org-roam--get-backlinks (push file-path titles)))))
              (grouped-backlinks (--group-by (nth 0 it) backlinks)))
        (progn
          (insert (let ((l (length backlinks)))
                    (format "\n\n* %s\n"
                            (org-roam-buffer--pluralize "Backlink" l))))
          (dolist (group grouped-backlinks)
            (setq file-from (car group))
            (setq props (mapcar (lambda (row) (nth 2 row)) (cdr group)))
            (setq props (seq-sort-by (lambda (p) (plist-get p :point)) #'< props))
            (insert (format "** %s\n"
                            (org-roam-format-link file-from
                                                  (org-roam-db--get-title file-from)
                                                  "file")))
            (dolist (prop props)
              (insert "*** "
                      (if-let ((outline (plist-get prop :outline)))
                          (-> outline
                              (string-join " > ")
                              (org-roam-buffer-expand-links file-from))
                        "Top")
                      "\n"
                      (if-let ((content (funcall org-roam-buffer-preview-function file-from (plist-get prop :point))))
                          (propertize
                           (s-trim (s-replace "\n" " " (org-roam-buffer-expand-links content file-from)))
                           'help-echo "mouse-1: visit backlinked note"
                           'file-from file-from
                           'file-from-point (plist-get prop :point))
                        "")
                      "\n\n"))))
      (insert "\n\n* No backlinks!"))))
```


### Sync only top level files {#sync-only-top-level-files}

```emacs-lisp
(defun my-org-hugo-org-roam-sync-all()
  ""
  (interactive)
  (dolist (fil (split-string (string-trim (shell-command-to-string (concat "ls " org-roam-directory "/*.org")))))
    (with-current-buffer (find-file-noselect fil)
      (org-hugo-export-wim-to-md)
      (kill-buffer))))
```


## Conclusion {#conclusion}

Please write your feedbacks in the comment sectin below. I feel the need to break down the coding bits further. Let me know if i should or shouldn't. My exported braindump can be found at [braindump](https://sidhartharya.me/braindump/index.html). Please note that my braindump is highly unstructured and most importantly it is not a knowledge repository yet. Some of the information there may be inaccurate or outdated.

{{< figure src="/ox-hugo/2021-06-07_01-46-18_2021-06-07-014539_740x797_scrot.png" >}}


## References {#references}

-   [Org-roam](https://www.orgroam.com/)
-   [kaushalmodi/ox-hugo](https://github.com/kaushalmodi/ox-hugo)
-   [Ox-hugo export all roam to Hugo | Ben Mezger](https://seds.nl/notes/ox%5Fhugo%5Fexport%5Fall%5Froam%5Fto%5Fhugo/)
-   [jethrokuan/braindump: knowledge repository managed with org-mode and org-roam.](https://github.com/jethrokuan/braindump)

