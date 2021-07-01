# Automatically fetching newly added feeds in elfeed org file


The best Rss or Atom feed reader is [elfeed](https://github.com/skeeto/elfeed). There is no competition at all in comparison to it, thanks to the excellent programming done by [skeeto (Christopher Wellons)](https://github.com/skeeto). I urge my readers to go through his blog at [null program](https://nullprogram.com/).
[elfeed-org](https://github.com/remyhonig/elfeed-org) is a package that makes it simpler to write the list of elfeed feeds you need with tags and categories, etc in an org file.

WIth that said, let's dive in.

I have tried all sorts of rss feed readers, commercial and non commercial, but none compare to the flexibility and robustness of elfeed. Tagging. Filtering. Extensibility. What does it not have? It can do all sorts of things. You can tag an entry based on what keywords it contains. For example, if a news item contains the keyword "Bitcoin" then it's probably a good idea to tag it as crypto.

But this article is not about all the glorious things elfeed can do. It is about one of my quick dirty hacks to make my life slightly easier.

A very particular nuisance for me was to add a feed in the elfeed-org and manually update that feed, and run hooks. Either i did that, or i directly ran `elfeed-update` command which updates all my feeds.
That is a really bad idea. Since the more requests you make to a server, the more the chance that you will get blocked for misusing it. Many websites don't block you though, but some do, either permanently or partially.

The function i wrote is given below:

```emacs-lisp
(require 'dash)
(defun my-elfeed-org-update ()
  "AUtomatically Update the feeds from feeds.org when updated"
   (setq my-elfeed-org-last (or (and (boundp 'elfeed-feeds) elfeed-feeds) nil))
  (elfeed)
  (setq my-elfeed-org-current elfeed-feeds)

  (let ((elfeed-feeds (-difference my-elfeed-org-current my-elfeed-org-last)))
;; You don't need the line below if you don't want to see a message about what feeds are being updated
    (message "%s"  elfeed-feeds)
    (mapc #'elfeed-update-feed (elfeed--shuffle (elfeed-feed-list))))
  (setq elfeed-feeds my-elfeed-org-current))
```

I add the given lines to the top of my `feeds.org` file.

```org
# Local Variables:
# after-save-hook: my-elfeed-org-update
# END:
```

You will get an unsafe variable prompt every time you save the file thereafter. But, now your feeds will be immediately fetched when you update the feeds.org file.
