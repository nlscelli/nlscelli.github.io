---
title: Bash intermediate
permalink: https://nlscelli.github.io/codes/2-bash_intermediate
---

# Intermediate bash: manipulating files and outputs
In the previous chapter of this course, we got an overview on files, directories, programs and how to run them manually or in loops. In this section we will learn how to redirect the outputs of a program into the input of another one and go more "hands on" and see how we can further manipulate files.

## Input, output and concatenating commands
In shell, most programs need an input and produce an output. These can be either printed to screen (standard in/out aka `stdin`, `stdout`) or redirected to a file.

Here we can for instance list the content of a directory with some files in it and write it to a new file using the operator `>`, then print to the screen (`stdout`) the content of the new file using `cat`:
```bash
$my_teminal$: ls my_directory > my_dirlist.txt
$my_teminal$: cat my_dirlist.txt
file1.txt
file2.txt
file3.txt
```

Similarly, we can take the output of a command and redirect the output as input for another command using the operator `|`, in this case `wc` (word count):
```bash
$my_teminal$: ls my_directory | wc
      3       3      30
```
Which lists the numer of lines, words and bytes of the list of directories.

Here another, more complex example:
```bash
$my_teminal$: echo "1 2 3" | awk '{print "sum of first two is:", $1+$2}'

sum of first two is: 3
```

What we did here is just print the numbers "1 2 3" as a text string and then redirect these as input for the program `awk`, that we used to parse the result, sum only the first two elements of the string and print the result with some text explanation.

If we wanted, we could append another `|` at the end and further manipulate the result of our `awk` command.

Output redirection becomes particularly useful when manipulating files. As we have shown, programs like `awk` can perform math, so they can be very useful to perform basic operations (or complex actually!) directly in bash:

At first, let's create a file `text.txt` with some numbers in two columns:
```bash
1 1
3 1
5 1
7 1
2 1
4 1
6 1
```

Now let's sort it and write the sum of the first and second column into a new file `results.txt`:
```bash
$my_teminal$: sort text.txt | awk '{print $1+$2}' > results.txt
```

We can now view the results in a lot of ways, but a quick way to do it is to to use the `cat` command we encountered before. `cat` will print the content of one or more files to the terminal:

```bash
$my_teminal$: cat results.txt
2
3
4
5
6
7
8
```

## Searching a file
Often in research, we will want to extract a piece of information that is buried inside a file. This can be done in an enormous number of ways.

Being a seismologist, in this chapter we will use as an example an earthquake catalog from the Galapagos Islands(that you can download [here](https://zenodo.org/record/4389190#.YmLQiNPMI2w)) .

If we look at the first lines of the catalog by using `head`:
```bash
$my_terminal$: head IGUANA_Cat_V1.eventlist
EVENT_ID DATE ORIGIN_TIME LATITUDE LAT_ERROR LONGITUDE LON_ERROR DEPTH DEP_ERROR RMS(s) AZGAP(deg) MAG NUM_OF_PHASE_PICKS
dias2018hwhk 2018-04-22 19:28:52.486 -0.79289 0.843 -91.13652 0.406 1.111 1.346 0.072 178.1 0.83 9
dias2018hwkr 2018-04-22 21:08:35.601 -0.79544 0.396 -91.14171 0.254 0.515 0.668 0.048 119.3 1.31 13
dias2018hwkv 2018-04-22 21:12:56.308 -0.79395 0.434 -91.13336 0.266 0.759 0.807 0.034 118.5 1.43 10
dias2018hwul 2018-04-23 02:04:35.917 -0.80915 0.253 -91.10180 0.271 0.475 0.669 0.060 67.6 2.55 11
dias2018hxfp 2018-04-23 07:42:53.778 -0.82752 0.913 -91.15709 1.918 -0.669 1.651 0.077 76.2 1.28 9
dias2018hynz 2018-04-24 01:05:25.477 -0.79984 0.303 -91.13310 0.235 0.515 0.779 0.047 60.4 1.83 13
dias2018hypp 2018-04-24 01:53:38.980 -0.82453 0.223 -91.10312 0.215 0.446 0.583 0.058 89.2 1.59 14
dias2018hyrf 2018-04-24 02:42:27.250 -0.82910 0.361 -91.15727 0.386 0.485 0.771 0.081 71.4 1.84 12
dias2018hzgf 2018-04-24 10:18:17.184 -0.99891 1.246 -91.24834 0.698 2.115 1.288 0.113 261.6 1.02 11
```
We can see that the file is composed of a first line that tells us what each column represents (the <em>header</em>), followed by the real data. However, running `wc` quickly reveals that the catalog is fairly long to search manually:

```bash
$my_terminal$: wc IGUANA_Cat_V1.eventlist
    1988   25844  197745 IGUANA_Cat_V1.eventlist
```
Tells us we have 1988 lines, one for each event.

### Pattern matching
the easiest way to find a snippet of information in a large file is to search for a desired pattern in the file. This can be done by `grep`.

If we know the event ID (e.g. dias2018puxc) and want to know all info about it, we can:
```bash
$my_terminal$: grep "dias2018puxc" IGUANA_Cat_V1.eventlist
dias2018puxc 2018-08-13 21:25:37.344 -0.82673 0.268 -91.16158 0.278 -0.073 0.599 0.079 77.8 3.21 17
```
Will print to screen the line in which the pattern "dias2018puxc" was found.

Similarly, we can find all events that happened on 12/08/2018 by typing:

```bash
$my_terminal$: grep "2018-08-12" IGUANA_Cat_V1.eventlist
dias2018prwx 2018-08-12 05:55:43.932 -0.83385 0.224 -91.13951 0.260 -0.356 0.534 0.051 85.5 1.73 9
dias2018prwy 2018-08-12 05:56:02.947 -0.83613 0.181 -91.13617 0.212 0.759 0.402 0.073 91.8 3.07 17
dias2018psfg 2018-08-12 10:08:43.792 -0.82638 0.192 -91.11288 0.298 0.671 0.624 0.066 105.0 2.80 16
```

## Parsing a file
Most of the times, we only want part of the information in the file (e.g. the coordinates of the earthquake). In order to do this, we need to parse the contents of each line, breaking it into its elements. This can be done in different ways.

### Method 1: shell loops
We can use shell to directly loop over the contents of the file, line by line, read the line as an array of strings and then select to print to screen only some columns from the array. If we want the 3rd and 5th column (lat, lon) for instance:
```bash
while read -r line; do
  array=(${line})
  echo ${array[3]} ${array[5]}
done < IGUANA_Cat_V1.eventlist
```

With IGUANA_Cat_V1.eventlist used as an input of our while loop.

### Method 2: awk
My favourite way to do the same task is however `awk`. `awk` is a programming language of its own, but can be easy implemented in a bash command or script.

In `awk`, the same task as before can be written in a single, concise line:
```bash
$my_terminal$: awk '{print $4,$6}' IGUANA_Cat_V1.eventlist
```
This will however include the header into our output, which could be a problem if we wanted to use it to e.g. plot the locations on a map. In `awk`, we can easily skip the first line by only selecting lines with row number>1:
```bash
$my_terminal$: awk 'NR>1 {print $4,$6}' IGUANA_Cat_V1.eventlist
```

Note that in the shell loop, array items were counted from `0`, so that lat, lon were columns line 3,5; while in `awk`, the counter starts with `1`, so lat, lon are columns 4,6!

Another convenience of `awk` is that it is also capable of conditional statements. If we wanted for instance to print the coordinates of all event rows that have been analysed by at least 10 picks and with a depth error < 0.5 km:
```bash
$my_terminal$: awk '{if($13>=10 && $9<=0.5) print $4,$6}' IGUANA_Cat_V1.eventlist
```
With `&&` in `awk` meaning the "and" operator between two conditions.

`awk` can be used to perform column-wise mathematical operations, from simple additions to square roots and arctangents. We can for instance convert the longitude from +-180 to 0-360 degrees:
```bash
$my_terminal$: awk '{if($6<0) print $4,$6+360; else print $4,$6}' IGUANA_Cat_V1.eventlist
```

You can plot any file with 3 columns by running [this notebook](Plot_catalog_BASH_course.md)

### Which method should I use?
In general, `awk` has a lot of advantages. The bash loop example for instance will not be able to replicate the `awk` results because `if` statements in bash do not support floating point numbers, only integers and strings.

If however, we want to feed our column data to an external program, the bash loop will allow to do so line by line, while with `awk` the same can be more complicated to achieve.

# Useful commands
```bash
grep                      # finds all lines with a pattern
grep -v                   # finds all lines without a pattern
sort                      # sorts the file (has interesting options)
uniq                      # prints only the non-repeated lines
head -n n                 # prints the first nlines
tail -n n                 # prints the last nlines
cat                       # prints one or more files
tac                       # prints one or more files in reverse
awk                       # file manipuilation column-row-wise
```
