# Installing a custom emacs package in nix


Recently, i decided to go into full nix mode. So i boot my laptop. Created a 500MB Partition for nixos's root. Added a new dataset to my already existing zfs pool called pool/nix. And i made the switch. For those who don't know what nix is, beware the hacker inside you may be drawn to it.

To be honest though, I never loved nix. The first time i tried it was in a 11 year old laptop, and the experience was really bad. Not that nixos was bad but my laptop was really really bad. Not even Arch Linux ran well, only gentoo (funtoo sandybridge actually, since i didn't want to compile my initial setup) could somehow magically adopt to my laptop.

John Wiegley has some nix config, so i thought if he is on nix then this better be really good. So, here we are. Back on nix. And the experience has been so good for me. It makes perfect sense now.

The reason why i love it now is because my system is reproducible. And it's extensibility is evident by the following:


## Why do it? {#why-do-it}

So abruptly ended the last section and now I am asking questions too!
Melpa has most emacs packages but not all. Personally, I sometimes fork some packages and make some modifications, which are not necessarily always accepted but i love my modifications, and I want to use them.
A really great way of doing so would be use `straight.el`. But I found another way of doing it on nix.
Also, please note that i am using `home-manager`.


## What did I modify? {#what-did-i-modify}

First of all there's `org roam` which forms all sorts of connections between your notes. Then there's `org-roam-server` which makes beautiful visualization out of these connection and gives a good interactive interface. What i really needed was not leaving emacs while filtering org-roam-server's visualization. So i added a way to add and remove tags from filters from within emacs so that it would be reflected on org roam server as well.
But let's get back to how to install a custom emacs package in nix.


## Code {#code}

Here's the code:

```nix

programs.emacs = {
  enable = true;
  extraPackages = epkgs: [
    epkgs.simple-httpd
    epkgs.org-roam-server
  ];
};


programs.emacs.overrides = self: super: {

  org-roam-server = self.melpaBuild {
    pname = "org-roam-server";
    version = "0.0.1";
    src = fetchTarball {
      url = "https://github.com/SidharthArya/org-roam-server/archive/master.tar.gz";
    };
    recipe = pkgs.writeText "recipe" ''
  (org-roam-server :fetcher github :repo "SidharthArya/org-roam-server" :files (:defaults "index.html" "assets"))
   '';
  };
};
```


## A little disection {#a-little-disection}

You can also use fetchGit function instead of fetchTarball.
All we are doing is giving it a different src URL and a different recipe than the one that is present on melpa.
Annndd.. `home-manager switch` would magically compile everything for you.
I won't speak much about the nix syntax since i am relatively new to nix. What I wrote here was just something i thought would work going through the code for melpaPackages at nixpkgs.


## Conclusion {#conclusion}

If there are any issues/suggestions please write them in the comment section below or on reddit.
