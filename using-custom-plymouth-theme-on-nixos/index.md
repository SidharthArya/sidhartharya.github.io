# Using custom plymouth theme on nixos


Almost anything you can think of to do with your linux system, can possibly be done in nixos. And it would also be reproducible at that. So i set out to get a custom theme for my boot up screen which is usually provided by plymouth in linux. Since we need to add a new theme to the system we should do that by creating a new package for the same. Nixos doesn't allow you to write to the nix store, since we want reproducible system, which would not happen if you could go ahead and modify the store files.


## Write an overlay {#write-an-overlay}

First thing you need is an overlay. An overlay is a concept i got familiar with on gentoo linux. An overlay is, as the name suggests, an addon set of packages for your system. So here let's define a simple overlay with just one package.
I would be using the lore theme from [adi1090x/plymouth-themes](https://github.com/adi1090x/plymouth-themes). Also he produces some amazing designs, do check him out.  This is what the lore theme looks like.

![](/ox-hugo/2021-07-13_06-57-30_53.gif)
 Anyway, let's get back to the code. I create a directory at `/etc/nixos/packages` for my overlay, where i write this file at `default.nix`.

```nix
self: super:
{
  adi1090x-plymouth = super.callPackage ./adi1090x-plymouth { };
}
```

All it says is, there is a package at the directory `./adi1090x-plymouth`.

In the `/etc/nixos/configuration.nix` file add the following line to activate the overlay.

```nix
nixpkgs.overlays = [ (import ./packages) ];
```


## Package Recipe {#package-recipe}

Now let's define a simple package at `/etc/nixos/packages/adi1090x-plymouth`. We need to create this directory and add a file called `default.nix` with the following contents.

```nix
{
  pkgs ? import <nixpkgs> {}
}:
pkgs.stdenv.mkDerivation rec {
  pname = "adi1090x-plymouth";
  version = "0.0.1";

  src = builtins.fetchGit {
    url = "https://github.com/adi1090x/plymouth-themes";
  };

  buildInputs = [
    pkgs.git
  ];

  configurePhase = ''
mkdir -p $out/share/plymouth/themes/
  '';

  buildPhase = ''
  '';

  installPhase = ''
  cp -r pack_3/lone $out/share/plymouth/themes
cat pack_3/lone/lone.plymouth | sed  "s@\/usr\/@$out\/@" > $out/share/plymouth/themes/lone/lone.plymouth
  '';
}
```

This simple packages derivations says get the source files from github. Make a directory `$out/share/plymouth/themes` (this is where plymouth expects your theme to be at). Then copy the `lone` theme folder to this directory. Rewrite the `lone.plymouth` file, adjusted for nixos store path rather than `/usr/`.
And that's it for our package.


## Applying in configuration.nix {#applying-in-configuration-dot-nix}

Now in the `configuration.nix` file, add the following lines. Make sure you do not have any other `boot.plymouth` line or adjust according to what you need.

```nix
boot.plymouth = {
  enable = true;
  themePackages = [ pkgs.adi1090x-plymouth ];
  theme = "lone";
};
```


## Conclusion {#conclusion}

Now finally execute the command `nixos-rebuild switch` and reboot your system. Is your theme working? Please write your comments in the comment section.
