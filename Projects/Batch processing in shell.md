[[bash]] [[Data Engineering]] [[Cloud]]

# How does the shell store information?
Like other programs, the shell stores information in variables. Some of these, called **environment variables**, are available all the time. Environment variables' names are conventionally written in upper case, and a few of the more commonly-used ones are shown:

| Variable | Purpose                           | Value               |
| -------- | --------------------------------- | ------------------- |
| `HOME`     | User's hom directory              | `/home/user`          |
| `PWD`      | Present working directory         | Same as pwd command |
| `SHELL`    | which shell program is being used | `/bin/bash`           | 
| `USER`     | User's ID                         | `user`              |

type `set` in the shell for a complete list

to get the value of any variable called `var`, you must write `$var`


## shell variables
like a local variable in a programming language

to assign, use `=` with no spaces, e.g. `report=data.csv`

`head -n 1 $report`


# Loops
Shell variables are also used in **loops**, which repeat commands many times. If we run this command:

```
for filetype in gif jpg png; do echo $filetype; done
```

it produces:

```
gif
jpg
png
```

Notice these things about the loop:

1.  The structure is `for` …variable… `in` …list… `; do` …body… `; done`
2.  The list of things the loop is to process (in our case, the words `gif`, `jpg`, and `png`).
3.  The variable that keeps track of which thing the loop is currently processing (in our case, `filetype`).
4.  The body of the loop that does the processing (in our case, `echo $filetype`).

Notice that the body uses `$filetype` to get the variable's value instead of just `filetype`, just like it does with any other shell variable. Also notice where the semi-colons go: the first one comes between the list and the keyword `do`, and the second comes between the body and the keyword `done`.


### assign set of files to a variable to save typing & make errors less likely
```
datasets=seasonal/*.csv
```

you can display the files' names later using:
```
for filename in $datasets; do echo $filename; done
```

pipe commands together to do things with each file:
```
for file in seasonal/*.csv; do head -n 2 $file | tail -n 1; done
```

**separate commands by semicolons**

# Create shell scripts!
since the commands you type in are just text, you can store them in files for the shell to run over and over again. To start exploring this powerful capability, put the following command in a file (via vim or nano) called `headers.sh`:

```
head -n 1 seasonal/*.csv
```

This command selects the first row from each of the CSV files in the `seasonal` directory. Once you have created this file, you can run it by typing:

```
bash headers.sh
```

This tells the shell (which is just a program called `bash`) to run the commands contained in the file `headers.sh`, which produces the same output as running the commands directly.


A file full of shell commands is called a **shell script**, or sometimes just a "script" for short. Scripts don't have to have names ending in `.sh`, but this lesson will use that convention to help you keep track of which files are scripts.

Scripts can also contain pipes. For example, if `all-dates.sh` contains this line:

```
cut -d , -f 1 seasonal/*.csv | grep -v Date | sort | uniq
```

then:

```
bash all-dates.sh > dates.out
```

will extract the unique dates from the seasonal data files and save them in `dates.out`:

### pass filenames to scripts
A script that processes specific files is useful as a record of what you did, but one that allows you to process any files you want is more useful. To support this, you can use the special expression `$@` (dollar sign immediately followed by at-sign) to mean "all of the command-line parameters given to the script".

For example, if `unique-lines.sh` contains `sort $@ | uniq`, when you run:

```
bash unique-lines.sh seasonal/summer.csv
```

the shell replaces `$@` with `seasonal/summer.csv` and processes one file. If you run this:

```
bash unique-lines.sh seasonal/summer.csv seasonal/autumn.csv
```

it processes two data files, and so on.


### process shell arguments one by one
As well as `$@`, the shell lets you use `$1`, `$2`, and so on to refer to specific command-line parameters. You can use this to write commands that feel simpler or more natural than the shell's. For example, you can create a script called `column.sh` that selects a single column from a CSV file when the user provides the filename as the first parameter and the column as the second:

```
cut -d , -f $2 $1
```

and then run it using:

```
bash column.sh seasonal/autumn.csv 1
```

==*Notice how the script uses the two parameters in reverse order.*==

---

The script `get-field.sh` is supposed to take a filename, the number of the row to select, the number of the column to select, and print just that field from a CSV file. For example:

```
bash get-field.sh seasonal/summer.csv 4 2
```

should select the second field from line 4 of `seasonal/summer.csv`. Which of the following commands should be put in `get-field.sh` to do that?


# Notes on bash optimization 
from [SO](https://unix.stackexchange.com/questions/67057/bash-script-optimization-of-processing-speed)

**It's rare that performance is a concern in shell scripts.** The list below is purely indicative; it's perfectly fine to use “slow” methods in most cases as the difference is often a fraction of a percent.

Usually the point of a shell script is to get something done fast. You have to gain a lot from optimization to justify spending extra minutes writing the script.

-   For internal processing in the shell, ATT ksh is fastest. If you do a lot of string manipulations, use ATT ksh. Dash comes second; bash, pdksh and zsh lag behind.
-   If you need to invoke a shell frequently to perform a very short task each time, dash wins because of its low startup time.
-   Starting an external process costs time, so it's faster to have one pipeline with complex pieces than a pipeline in a loop.
-   `echo $foo` is slower than `echo "$foo"`, because with no double quotes, it splits `$foo` into words and interprets each word as a filename wildcard pattern. More importantly, that splitting and globbing behavior is rarely desired. So remember to always put double quotes around variable substitutions and command substitutions: `"$foo"`, `"$(foo)"`.
-   Dedicated tools tend to win over general-purpose tools. For example, tools like `cut` or `head` can be emulated with `sed`, but `sed` will be slower and `awk` will be even slower. Shell string processing is slow, but for short strings it largely beats calling an external program.
-   More advanced languages such as Perl, Python, and Ruby often let you write faster algorithms, but they have a significantly higher startup time so they're only worth it for performance for large amounts of data.
-   On Linux at least, pipes tend to be faster than temporary files.
-   Most uses of shell scripting are around I/O-bound processes, so CPU consumption doesn't matter.
