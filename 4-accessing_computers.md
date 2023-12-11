---
title: Remote access
permalink: https://nlscelli.github.io/codes/4-accessing_computers
---
# Accessing computers through the terminal
In UNIX, we can access separate computers through the terminal. This allows us to e.g. perform large-scale computations on dedicated servers and then move the results to our laptop to insert the results into the papers we are writing.

## Accessing the system remotely: SSH
We can connect to any UNIX computer using `ssh` (once we know it's IP address).

```bash
$my_terminal$: ssh user@mymachine.localnetwork
```

This will ask you for your password to enter `mymachine.localnetwork` as `user` ; after entering it, the terminal you are working on will effectively be the terminal of the machine you connected to.

### Skip writing passwords - SSH keys
Everybody hates to type in passwords and so should you. When accessing a computer, like for most laptops, SSH needs you to type the password of the computer you are connecting to. Fortunately, we can skip entering the passwords for accessing computers and servers using `ssh keys`. These are configuration files that save and authorise your ssh ID upon login.

The ssh keys are stored in your home folder, in a hidden folder `.ssh` and are named `id_rsa` and `id_rsa.pub`. if you don't have them, you need to generate your ssh keys first.

On your machine (laptop or office workstation):

```bash
$my_terminal$: ssh-keygen
```
Then, you have to copy them to the server you want to connect to, e.g. `proxyserver`. By doing this you are telling the remote server that it can trust the passwordless access from your local machine. You can do this 2 ways:

#### automatically
from your local machine, simply type:
```bash
$my_terminal$: ssh-copy-id user@proxyserver.localnetwork
```

#### manually
you have to append the text from your PUBLIC key `id_rsa.pub` of your local machine to the file `.ssh/authorized_keys` on the remote machine you want to connect to (`proxyserver` in our previous examples).

Either way, this will allow your local machine to passwordless access to the remote server.

### Connecting to a machine passing trough another one: ssh tunnels
In some cases, you have no direct access to a machine and the only way to connect to it is through another, third machine. This is typically an issue when connecting to intranet networks protected by a proxy.

This can be easily done with ssh using the `-J` option. In our case, the server we need to go through is `proxyserver`
```bash
$my_terminal$: ssh -J user@proxyserver.localnetwork user@mymachine.localnetwork
```
This will connect you directly to `mymachine.localnetwork`.

### Copying files over ssh
Now that we can connect to all our favorite servers, we may want to copy files from or to them. We can do this with `scp`. `scp` uses the same syntax as `cp`, but you can add from/to what computer you want to copy.

For example, let's make an empty file `banana.txt`, copy it to `proxyserver` and back to your machine renaming it as `banana_comeback.txt`:

```bash
$my_terminal$: touch banana.txt
$my_terminal$: scp banana.txt user@proxyserver.localnetwork:home/user/.
$my_terminal$: scp user@proxyserver.localnetwork:home/user/banana.txt banana_comeback.txt
$my_terminal$: ls
banana.txt banana_comeback.txt
```

And if we need to pass through a third machine (see section above):
```bash
$my_terminal$: scp -o ProxyJump=user@proxyserver.localnetwork banana.txt user@mymachine.localnetwork:home/user/.
```
Unfortunately only the most recent versions of `scp` have the same option `-J` as `ssh`, so I suggest the option `-o ProxyJump=` as above for portability.

### Graphics over ssh
If you want to connect to a computer and use a program that has some graphics (for instance, opening an image or pdf or running `Matlab` in visual mode), using `ssh` will not suffice. If you are on mac, first you will need to install Xquartz. After that, we can use `ssh -X` or `-Y` to view graphics.

In slow connections, the weight of graphics can slow down the process, so I suggest to also always use the `-C` option to compress slightly the graphics and improve performance.

Let's copy the image about the file permissions to our remote machine and open it:
```bash
$my_terminal$: scp Permissions_explanation.png user@mymachine.localnetwork:home/user/.
$my_terminal$: ssh -Y -C user@mymachine.localnetwork
$my_terminal$: nomacs Permissions_explanation.png
```
On mac, this will open XQuartz and then show the picture. On Ubuntu the picture should open directly. If your connection is bad, this can take some time, even with the `-C` option.
