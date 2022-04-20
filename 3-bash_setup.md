---
title: Bash course for researchers
permalink: https://nlscelli.github.io/codes/bash_course/1-bash_setup
---

# Defining your own shell setup
Every time you start a terminal session, your shell will start anew, reading a set of preferences and configurations that can be highly customised.

In your own home folder (on Linux and mac OsX) there are hidden files (beginning with an initial `.`) where the setup of your shell is specified. When opening up a terminal, UNIX will read these before anything you do.

Typically, you will have:
1. a .bashrc
2. a .bash_profile (and/or .bash_private)
3. a .bash_alias
4. a .bash_history

Apart from the .bash_history that includes details on your recent bash activity, all other files have in general interchangeable roles. Typically however this is how they are set up:
1. bashrc --> where you want the most specific settings (you do not want to touch often); it also sources the other .bash files
2. bash_private/bash_profile --> where you set up your own variables and programs
3. bash_alias --> where you set up your aliases

You can view the ones currently in your home by typing:
```bash
$my_teminal$: ls ~/.bash*
```

    .bash_alias
    .bash_history
    .bash_profile
    .bashrc


## What do we want to have in our bash setup?
Typically, we want a few basic things:
1. Defining variables that we know we will always need
2. Tell our system where to look for programs/scripts
3. Define our own bash aliases (command shortcuts)

All the above can also be manually typed in each terminal session, however all settings would be lost once we exit it. For this reason, we need to code them into our .bash* setup files.

Now we will go through each of the main tasks and see some examples.

## 1) Setting up variables
To begin, it is useful to set up some variables. These can be anything from very long strings that you will need to use often ("what was the path to that folder on that hard disk again...?") to other names that can be user-dependant for example.

One good example is having a variable with your username and home folder. This can allow you to write, e.g. scripts that would work both on your work machine and your laptop, where these will be different.

After making sure your bash_private/bash_profile is sourced when starting the shell, you can add the following to it:


```bash
# here substitute "my_user" with your local username and "home" with your actual home
export user=my_user
export userpath=home/${user}
```

Now that you have set them, save the file and re-source the file (let's assume is .bash_profile) to let bash read the newly set variables.
```bash
$my_teminal$: source .bash_profile
```
Then we can test this by printing the new variables


```bash
$my_teminal$: echo ${user}
```

    my_user


## 2) Building our program list: the PATH variable
Perhaps the most important of all settings, it tells your system where he should look for programs. This is achieved by appending a list of all possible folders where there are programs to the `$PATH` variable (which can and will become exceedingly long!).

If a program is in a folder not listed in your `$PATH` variable, you will not be able to run it unless you are inside the folder in which it is stored.

We will now try to add the example code `myprogram.sh` found in the `myfolder` directory to our path and execute it. At first, you should not be able to run the code given in the `myfolder` folder:


```bash
$my_teminal$: myprogram.sh
```

    bash: myprogram.sh: command not found




To make it work, first, let's make the program executable (i.e. we can run it as a program)


```bash
$my_teminal$: chmod +x myfolder/myprogram.sh
```

Find out the absolute path of the folder containing the program


```bash
$my_teminal$: realpath myfolder/
```

    home/my_user/Bash_course/myfolder


Now we want to add the folder to our `$PATH` variable; note that in order to APPEND and not OVERWRITE your `$PATH`, you have to append the rest of the path at the end as `:${PATH}`. Also, see how now we can use the variable `$userpath` we set before:


```bash
export PATH=${userpath}/myfolder/:${PATH}
```

Now we should be able to run the program


```bash
$my_teminal$: myprogram.sh
```

    if you see this, you got me in your path correctly!


## 3) Bash aliases
This are the terminal equivalent of shortcuts. After we are sure a file called ".bash_alias" exists and is sourced in one of the other .bash* files, we can add to it some of the most common shortcuts. Here are some of my basic favourites:


```bash
alias home='cd ~/.'                                           # cd back to home
alias ..='cd ./..'                                            # cd one folder back
alias ll='ls -lh'                                             # long list (shows permissions and file sizes)
alias recent='ls -lagitrh | tail'                             # shows most recently edited files
alias resource='source ~/.bash_profile; source ~/.bash_alias' # re-sources the bash session
```

Now save these in the .bash_alias, re-source it and try some of the new aliases!


```bash
$my_teminal$: recent
```

    total 288
        1909618 drwxr-xr-x  6 staff   192B  7 Mar 14:29 ..
    12921857533 drwxr-xr-x  3 staff    96B  7 Mar 15:29 myfolder
    12921855578 -rw-r--r--  1 staff   9.7K  7 Mar 16:36 1-bash_basics.md
    12921855499 drwxr-xr-x  6 staff   192B  7 Mar 16:36 .
