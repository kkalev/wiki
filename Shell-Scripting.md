# Basic Scripts
* Echo: `echo`
  - No new line: `echo -n`
* Command output variable assignment: `var=$(command)`
* Arithmetic operations: `var=$[ <operation> ]`
* Exit: `exit <exit code>`
  - Normal exit code: `0`
# Structured Commands
* If statement:
```
if command; then
  commands
fi
```
* If-then-else statement:
```
if command; then
  commands
else
  commands
fi
```
* If-then-elif-else statement:
```
if command; then
  commands
elif command2; then
  commands
else
  commands
fi
```
* In all the above cases we are just evaluating the command exit code
* With **condition** (the spaces are important)
```
if [ condition ]; then
  commands
fi
```
* Numeric comparisons
  - `n1 -eq n2`: n1 is equal to n2
  - `n1 -ge n2`: n1 is greater or equal to n2
  - `n1 -gt n2`: n1 is greater then n2
  - `n1 -le n2`: n1 is less than or equal to n2
  - `n1 -lt n2`: n1 is less than n2
  - `n1 -ne n2`: n1 is not equal to n2
 * String comparisons
   - `str1 = str2`: str1 is the same as str2
   - `str1 != str2`: str1 is not the same as str2
   - `-n str1`: Check is str1 has a legnth greater than zero
   - `-z str1`: Check that str1 has zero length
 * File comparisons
   - `-d file`: File exists and is a directory
   - `-e file`: File exists
   - `-f file`: File exists and is a file
   - `-r file`: File is readable
   - `-w file`: File is writable
 * Compound Testing:
   - AND: `[ condition1 ] && [ condition2 ]`
   - OR:  `[ condition1 ] || [ condition2 ]`
 * Double brackets: `[[ expression ]]`
  - Allows for pattern matching
 * Case statement
  ```
 case variable in
 pattern1 | pattern2) commands1;;
 pattern3) commands2;;
 *) default commands;;
 esac
 ```
 # More Structured Commands
 * for loop
 ```
 for var in list
 do
   commands
 done
```
* List
  - Command: `for var in $(command)`
  - Read a directory: `for file in /home/user/*`
* While loop
```
while test command
 do
   other commands
 done
```
* Until loop
```
until test commands
 do
   other commands
 done
```
# User Input
* `$0`: Script name
* `$1 ... $N`: Nth parameter
* `$#`: Parameter count (without the scrit name)
* `$*`: All parameters supplied in a **single word**
* `$@`: Parameters are separate words in a string
* `read var`: Read user input into variable `var`
  - `-p "prompt"`: Read user input into variable `var` while displaying `prompot`
  - `-s`: Dont show input
  - `<file>` Read a line from the `file` in each call to `read`
# Functions
```
function name {
 commands
}
```
* Just as you can capture the output of a command to a shell variable, you can also capture the output of a function to a shell variable.
* You can pass parameters just as you pass them to a shall script
# Sed and Awk
## Sed
* The sed editor is called a stream editor. A stream editor edits a stream of data based on a set of rules you supply ahead of time, before the editor processes the data.
* The sed editor does these things:
  - Reads one data line at a time from the input
  - Matches that data with the supplied editor commands
  - Changes data in the stream as specifi ed in the commands
  - Outputs the new data to STDOUT
* Simple example: ` echo "This is a test" | sed 's/test/big test/'`
* Multiple commands: ` echo "This is a test" | sed 's/test/big test/'`
  - The commands must be separated with a semicolon, and there shouldn’t be any spaces between the end of the command and the semicolon.
* if you have lots of sed commands you want to process, it is often easier to just store them in a separate file. Use the `-f` option to specify the fi le in the sed command:
```
$ cat script1.sed
s/brown/green/
s/fox/elephant/
s/dog/cat/
$
$ sed -f script1.sed data1.txt
```
* Substitution flags:
  - A number, indicating the pattern occurrence for which new text should be substituted
  - `g`, indicating that new text should be substituted for all occurrences of the existing text
  - `i`, to be case insensitive
* The delete command, `d` deletes any text lines that match the addressing scheme supplied
* The transform command `y` is the only sed editor command that operates on a single character. The transform command uses the format: `[address]y/inchars/outchars/`
## Awk
* A gawk program script is defined by opening and closing braces. You must place script commands between the two braces ({}).
* Simple example: `gawk '{print "Hello World!"}'`
* By default, gawk assigns the following variables to each data field it detects in the line of text:
  - `$0`: Entire line of text.
  - `$1`: First data field in the line of text
  - `$n`: Nth data field
* Each data field is determined in a text line by a **field separation character**. `gawk -F: '{print $1}' /etc/passwd`
* To use multiple commands in the program script specified on the command line, just place a semicolon between each command: `echo "My name is Rich" | gawk '{$4="Christine"; print $0}'`
* The `BEGIN` keyword forces awk to execute the program script specified after the `BEGIN` keyword, before gawk reads the data: `gawk 'BEGIN {print "Hello World!"}'`
* Like the `BEGIN` keyword, the `END` keyword allows you to specify a program script that gawk executes after reading the data
# Regular Expressions
## Special Characters
* `^`: Start at the beginning
* `$`: Marks the end
* `.`: The `dot` special character matches any **signle character**
* `[]`: Character class
* `[^]`: Negate character class
* `[0-9], [a-z]`: Ranges
* `*`: The previous character should appear zero or more times
* `?`: The previous character should appear zero or one times
* `+`: The previous character should apper one or more times
* Braces can be used to set limits on repeatable regular expressions
  - `{m}`: Exactly `m` times
  - `{m,n}`: At least `m` times but no more than `n` times
* `|`: Specify two or more patterns to match in a logical OR
### Special classes
* `[[:alpha::]]`: Alphabetical characters, upper and lower case
* `[[:alnum::]]`: Alphanumeric
* `[[:digit:]]`: Numerical digits
* `[[:lower:]]`: Lower case
* `[[:upper::]]`: Upper case
* `[[:space:]]`: Any white space
