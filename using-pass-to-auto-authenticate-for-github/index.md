# Using pass to auto authenticate for github


People often use SSH to avoid entering their username and password while pushing commits to github or gitlab. I like to use this excellent unix password utility called [Pass](https://www.passwordstore.org/).


## Why use pass instead of SSH {#why-use-pass-instead-of-ssh}

-   Anyone who can gain access to your computer (physical, or through some form of hacking) can push changes to your github.
-   the gpg-agent locks itself after some time has elapsed
-   You can kill the gpg-agent when you are not not using the machine
-   Github recommends using personal access tokens

A disadvantage though is, you will have to enter your password store password from time to time. I usually sync all my github repositories at once. So i just have to enter password once most of the time.


## Github Personal Access Token {#github-personal-access-token}

Github has deprecated usage of the github passwords for pushing changes to the repository.&nbsp;[^fn:1]
Please create one personal access token as mentioned by Github here [Creating a personal access token - GitHub Docs](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)


## Pass {#pass}

"With pass, each password lives inside of a gpg encrypted file whose filename is the title of the website or resource that requires the password."
I have simply copied the above statement from pass's website, which best describes what it is.

If you are new to pass, refer [Pass: The Standard Unix Password Manager](https://www.passwordstore.org/)
In a nutshell,

-   You have to initialize a password store using `pass init`
-   Create a new pass, say we save it as 'Github/PAT' `pass insert Github/PAT`
-   Enter your Github Personal Access Token here. And reenter when it asks again.


## Script to fetch the password {#script-to-fetch-the-password}

Now that we have stored the Personal Access Token in our password store. All we have to do is write a small script that we can call to get this password.

```sh
#!/bin/sh
pass "Github/PAT" | head -n 1
```

Say we save it as `git_pass.sh`

What the script would do get the github personal access token. If the password store is locked ask for the unlock password from the user and then unlock it and get the password.


## Environment Variables {#environment-variables}

Basically, there is this special environment variable that github provides, called `GIT_ASKPASS`. What it simply does is execute whatever has been assigned to it and use the output of that execution as the github password.

In your `.bashrc` or `.zshrc` place something as shown below. In windows you would probably need to use the Environment Variables.

```sh
export GIT_ASKPASS="path_to_script_defined/git_pass.sh"
```


## Further Securing {#further-securing}

The Password Store may still be unlocked, even if you have locked your screen. You may want to hook up the command `gpgconf --kill gpg-agent` to when your screen is locked.
I execute a `lock.sh` for locking the screen, as given below. In order to make this execute for locking screen i use `xss-lock lock.sh`.

```bash
#!/bin/sh
gpgconf --kill gpg-agent
XDG_SEAT_PATH="/org/freedesktop/DisplayManager/Seat0" DISPLAY=:0 slock
sleep 3
```


## References {#references}

-   [Git - gitcredentials Documentation](https://git-scm.com/docs/gitcredentials#:~:text)
-   [Pass: The Standard Unix Password Manager](https://www.passwordstore.org/)
-   [gpgconf (Using the GNU Privacy Guard)](https://www.gnupg.org/documentation/manuals/gnupg/gpgconf.html)

[^fn:1]: [Token authentication requirements for Git operations | The GitHub Blog](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)
