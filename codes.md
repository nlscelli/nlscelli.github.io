---
title: Codes
permalink: https://nlscelli.github.io/codes
---

# Codes
A repository of (hopefully) useful codes and programming lectures that I developed organically during my academic career.

> **NOTE:** all codes provided have been written on mac OSX and tested on mac OSX and Ubuntu. 
>
> Microsoft users may encounter "ghost characters" problems due to the different encoding in the form of weird character appearing in the text as well as problems with the shebang at the beginning of the code.
> 
> To check whether you have encoding issues, you can display the code with `cat -v filename.sh`; you should see weird characters appearing at, e.g. line ends. To fix the issue, you can remove all these characters by running `sed -e "s/\r//g" filename.sh > newfile` and `mv newfile filename.sh`.
> 
> To fix the shebang issue, using `#!/bin/bash` works in my experience. If in doubt, you can find the path to your bash by typing `echo $0`.


## BASH course for researchers
A beginner's bash course for researchers that I gave at DIAS. Targeted to teach how to understand and harness the power of interconnected computer infrastructure.

1. [**BASH basics**](1-bash_basics.md)
2. [**BASH intermediate**](2-bash_parsing.md)
3. [**BASH setup**](3-bash_setup.md)
4. [**Connectivity and remote access**](4-accessing_computers.md)
5. [**BASH advanced**](5-bash_advanced.md)


## Connectivity codes
A set of codes I developed to ease the accessibility and communication in between computers. They can all be seen [on my github, in the "Utilities" section](https://github.com/nlscelli/Utilities).

Highlight codes:
- [jptremote.sh](https://github.com/nlscelli/Utilities/blob/main/jptremote.sh), a tool to run a jupyter notebook remotely on a server
- [rcmd.sh](https://github.com/nlscelli/Utilities/blob/main/rcmd.sh), a tool to run any command on a remote server
- [chrome_remote.sh](https://github.com/nlscelli/Utilities/blob/main/chrome_remote.sh), a tool to run google chrome through a remote proxy server

The main purpose is to be able to execute command remotely, often over an ssh tunnel to jump a proxy. While each code serves a different purpose, in [this page](connectivity_example.md) I break down one of the codes to explain the core components of this kind of BASH programming.

## Utility codes
In addition to connectivity codes, I added several codes that perform useful actions, such as:
- [bmark.sh](https://github.com/nlscelli/Utilities/blob/main/bmark.sh), a tool to create rapid bookmarks for quick access to folders 
- [vicheck](https://github.com/nlscelli/Utilities/blob/main/vicheck), a simple program to instantly open in `vim` any program that is in your `$PATH`
