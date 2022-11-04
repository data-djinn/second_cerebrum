[[bash]] [[linux]] [[Data Engineering]]

# From CLI to bash scripts
- ease of execution of shell commands (no need to copy & paste every time!)
- powerful programming constructs
#### regex
- used often to filter files/data, match arguments, and a variety of other uses
`cat two_cities.txt | grep 'Charles Darnay\|Sydney Carton' | wc -l`
## Bash script anatomy
- begins with `#!/usr/bash`
    - so your interpreter knows it is a bash script & to use bash located in `/usr/bash`
    - This could be a different path if you installed Bash somewhere else 
        - use `which bash` to check
- has a `.sh  `
```shell
#!/bin/bash

  

# Create a sed pipe to a new file

cat soccer_scores.csv | sed 's/Cherno/Cherno City/g' | sed 's/Arda/Arda United/g' >> soccer_scores_edited.csv
```

in Linux, there are 3 streams for your program:
1. STDIN (standard input) - stream of data into the program
2. STOUT (standard output) - stream of data *out* of the program
3. STERR - errors in your program

by default, these streams will come from and write out to the terminal
- redirect to deletion by `2> /dev/null` in script call (or `1>` for STDOUT)
![[Pasted image 20211212144400.png]]

#### STDIN vs ARGV
- bash scripts can take *arguments* to be used inside by adding a space after the script execution call
- ARGV is the array of all the arguments given to the program
- each argument con be accessed via the `$` notation
    - `$1`, `$2`, etc give first, second arguments
    - `$@` & `$*` give all the arguments in ARGV
    - `$#` gives the length (number) of arguments
    - ``some text`` = shell runs the command and captures STOUT back into a variable (shell within a shell)
        - can also use `$()` notation (more modern & has advantages)
   
# Variables and data types in bash
- `var1='Moon'`
- `'sometext'` --> shell interprets what is between literally
- `""` --> shell interprets literally *except* $ and backticks

## Numeric values in bash
- `bc` is a basic calculator
- pipe expressions to bc to calculate
- pass `scale` argument to define number of decimal places
- e.g. `echo "scale=3; 10 / 3" | bc` --> `3.333`
- assign numeric variables like string variables
    - adding quotes (`"6"`) makes it a string!
- use `$((5 + 7))` for quick integer calculations (no decimals!)

## Arrays in bash
#### numerical-indexed array (python list, or vector)
- either create without adding elements:
    - `declare -a my_first_array`
- or create array & add elements at the same time
    - `my_first_array=(1 2 3)`
        - no spaces around equals sign!
        - no commas between elements!

- all array elements can be returned with `${array[@]}`
    - length can be accessed by `${#array[@]}`
    - access array elements with `${array[0]}`
        - zero-indexed!

- set array elements using index notation: `array[0]=100`

- Slice array with `array[@]:N:M`
    - `N` is starting index
    - `M` is how many elements to return

- append to array with `array+=(elements)`

#### Associative arrays (python dictionary)
- key-value pairs
- declare with `declare -A my_second_array`
    - surround keys with [], then associate values after the equals sign
```shell
declare -A city_details=([city_name]="New York" [population]=1400000)
echo ${city_details[city_name]} 
```
- access the keys of an associative array with `!`
    - `echo ${!city_details[city_name]}`

```shell
# Create variables from the two data files
temp_b="$(cat temps/region_B)"
temp_c="$(cat temps/region_C)"

# Create an array with these variables as elements
region_temps=($temp_b $temp_c)

# Call an external program to get average temperature
average_temp=$(echo "scale=2; (${region_temps[0]} + ${region_temps[1]}) / 2" | bc)

# Append average temp to the array
region_temps+=($average_temp)

# Print out the whole array
echo ${region_temps[@]}
```

# Control statements
### IF statements
```shell
if [ condition ]; then
    # some code
else
    # some other code
fi
```
- spaces between square bracket & conditional element
- semi-colon after close bracket `];`
- flags:
    - eq: equal to
    - -ne: not equal to
    - -lt: less than
    - -gt: greater than
    - -ge: greater than or equal to
    - -e: if the file exists
    - -s if the file exists & has a size greater than 0
    - -r if the file exists & is readable
    - -w if the file exists & is writable
```shell
# Extract Accuracy from first ARGV element
accuracy=$(grep Accuracy $1 | sed 's/.* //')

# Conditionally move into good_models folder
if [ $accuracy -ge 90 ]; then
    mv $1 good_models/
fi

# Conditionally move into bad_models folder
if [ $accuracy -lt 90 ]; then
    mv $1 bad_models/
fi
```
- use `||` for OR
- use `&&` for AND
- can also use built in functions, like `grep`
    - use with shell-within-a-shell 

# Functions and script automation