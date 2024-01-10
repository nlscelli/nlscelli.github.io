# Advanced BASH

in the previous sections, we learned how to run default programs in a shell, create our own, and manipulate and redirect their input and output.

In this section, we will discuss how to best manage our processes to make the most of today's powerful machines, safely.

## Background & Foreground programs
---
Often programs can take long time to complete, e.g.: copying large amount of files with `cp`, or making complex figures or calculations. In these cases, we want to free our terminal window so that we can work on something else in the meanwhile.

This can be done by sending the processes to background.

### Foreground processes
If we run any program in the terminal window, it will stop us from typing more lines until it finishes---because it's running ***in the foreground***.

E.g. if we write a program to wait 5 s and then print a message (technically 2 sequential programs), we won't be able to use the terminal until we see the print message:

my_program.sh:
```bash
#!/usr/bin/env bash
sleep 5
echo "the wait is over"
```

Execution:
```bash
$my_teminal$: my_program.sh
the wait is over
```
This is the "standard" way to run a program.


### Background processes
In order to be able to type something else while a program runs, we need to send program ***to the background*** by adding a final "&":

```bash
$my_teminal$: my_program.sh &
[1] 12345
$my_teminal$: echo "banana"
banana
$my_teminal$:
$my_teminal$:
$my_teminal$: the wait is over
[1]+  Done                    my_program.sh
```
In the above example, we ran the program in the background, entered another command (`echo "banana"`) while the first one was running, and finally got its output ("the wait is over").

Importantly, in the example above, the shell gave us two extra lines:
1. `[1] 12345` -> info  to identify our program in the background.
2. ``[1]+  Done my_program.sh`` -> when the program finishes, it notifies us.

## Detaching a program from the shell
---
Both fore- and background programs are tied to our terminal window or shell, which means that they will stop if we accidentally close it.

For long, non-interactive programs, we may want to make sure that they cannot be accidentally closed.

There are many ways to do it, but we will focus on **detaching a process from the shell** using `nohup`.

The program `nohup` ("NoHangUP") detaches the program from the current terminal, making independent from it. A program ran with `nohup` will not stop even if we close all terminal windows. If we use `nohup` in conjunction with the background character `&` in the example from above:
```bash
$my_teminal$: nohup my_program.sh &
[1] 12345
appending output to nohup.out
$my_teminal$:
$my_teminal$:
[1]+  Done                    nohup my_program.sh
```
As you can see the results are similar to putting `my_program.sh` in the background, but we don't see the output! This is because nohup automatically redirects the standard output to a file called `nohup.out`:

```bash
$my_teminal$: cat nohup.out
the wait is over
```
This is necessary because the program is not attached to our terminal anymore, so it cannot output to it.

When we use `nohup`, we can close all terminal windows and our program will run until the end or until the computer shuts down.

## Identifying and monitoring background processes
---
For both background and detached processes, the user looses direct control over them, so that they cannot be stoppped with `Ctrl+C` for instance.

Processes in UNIX are uniquely identified by a unique number consisting of multiple digits: the Process ID or **PID**.

In the examples above, for each process we sent to the background the terminal gave us the PID of our program, "12345".

### Jobs
In order to list all background processes (a.k.a. "jobs"), run `jobs -l`, which will give us the job number, PID, status and name of the program we ran:

```bash
$my_teminal$: jobs -l
[1]+ 12345 Running                    my_program
```

If we do it while our example is running, we will get:

```bash
$my_teminal$: my_program.sh &
[1] 12345
$my_teminal$: jobs -l
[1]+ 12345 Running                    my_program.sh
$my_teminal$:
$my_teminal$:
$my_teminal$:
$my_teminal$: the wait is over
[1]+  Done                    my_program.sh
```

Jobs however only reports the programs **that were started in the current terminal**. If we started a `nohup` program in another terminal, it will not be listed.

### ps
The command `ps` or "Process Status", will return exactly that. With the `aux` option to inlcude processes for all users and the ones with no controlling terminal, we can repeat our example:
```bash
$my_teminal$: nohup my_program.sh &
[1] 12345
appending output to nohup.out
$my_teminal$: ps aux | grep my_prog
user    12345 0.0 0.0 34150324 1408 s002 S  3:52pm 0:00.02 bash my_program.sh
user    12346 0.0 0.0 34122828  824 s002 R+ 3:52pm 0:00.00 grep my_prog
$my_teminal$:
$my_teminal$:
$my_teminal$:
[1]+  Done                    nohup my_program.sh
```
`ps aux` will list the PID as the second item of the string.

### htop
The program `htop` allows a comprehensive view of all processes currently ongoing on a machine,

## Terminating background programs
---
Once we identified the PID of the program we are interested in, we are able to terminate it.

To terminate a program with a certain PID `PIDofprogram` even if it's hung up, run:
```bash
$my_teminal$: kill -9 PIDofprogram
```

The "-9" option will kill also a stuck program.

For processes with a known name, we can also use `killall`, which will kill all subprocesses as well. Useful to tame problematic apps, e.g.:
```bash
$my_teminal$: killall Firefox
```
