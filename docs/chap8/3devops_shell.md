# **3 Basic Shell Scripting for Devops**

## **1 Shell Scripting Exercises**

### **Hello World**

**Objectives**

* Define a variable with the string 'Hello World'
* Print the value of the variable you've defined and redirect the output to the file "`amazing_output.txt`"

**Solution**


```
#!/usr/bin/env bash

HW_STR="Hello World"
echo $HW_STR > amazing_output.txt
```

### **Basic Date**

**Objectives**

* Write a script that will put the current date in a file called `"the_date.txt"`

**Solution**

```
#!/usr/bin/env bash

echo $(date) > the_date.txt
```

### **Great Day**

**Objectives**

Write a script that will print "Today is a great day!" unless it's given a day name and then it should print "Today is "

Note: no need to check whether the given argument is actually a valid day

```
#!/usr/bin/env bash

echo "Today is ${1:-a great day!}"
```

```
$ bash greatDay.sh 
Today is a great day!

$ bash greatDay.sh 2
Today is 2
```

### **Shell Scripting - Factors**

**Objectives**

Write a script that when given a number, will:

* Check if the number has 2 as factor, if yes it will print "one factor"
* Check if the number has 3 as factor, if yes it will print "one factor...actually two!"
* If none of them (2 and 3) is a factor, print the number itself

**Solution**

```
#!/usr/bin/env bash

(( $1 % 2 )) || res="one factor"
(( $1 % 3 )) || res+="...actually two!"

echo ${res:-$1}
```

### **Argument Check**


**Objectives**

Note: assume the script is executed with an argument

* Write a script that will check if a given argument is the string "pizza"
* If it's the string "pizza" print "with pineapple?"
* If it's not the string "pizza" print "I want pizza!"

**Solution**

```
/usr/bin/env bash

arg_value=${1:-default}

if [ $arg_value = "pizza" ]; then
    echo "with pineapple?"
else
    echo "I want pizza!"
fi
```


### **Files Size**

**Objectives**

* Print the name and size of every file and directory in current path

Note: use at least one for loop!

Solution

```
#!/usr/bin/env bash

for i in $(ls -S1); do
    echo $i: $(du -sh "$i" | cut -f1)
done 
```

### **Count Chars**

**Objectives**

* **Read input from the user until you get empty string**
* For each of the lines you read, count the number of characters and print it

**Constraints**

* You must use a while loop
* Assume at least three lines of input

**Solution**

```
#!/usr/bin/env bash

echo -n "Please insert your input: "

while read line; do
    echo -n "$line" | wc -c
    echo -n "Please insert your input: "
done
```

### **Sum**

**Objectives**

* Write a script that gets two numbers and prints their sum
* Make sure the input is valid (= you got two numbers from the user)
* Test the script by running and passing it two numbers as arguments

**Constraints**

Use functions

**Solution**

```
#!/usr/bin/env bash

re='^[0-9]+$'

if ! [[ $1 =~ $re && $2 =~ $re ]]; then
    echo "Oh no...I need two numbers"
    exit 2
fi

function sum {
    echo $(( $1 + $2 ))
}

sum $1 $2
```

### **Number of Arguments**

**Objectives**

* Write a script that will print "Got it: " in case of one argument
* In case no arguments were provided, it will print "Usage: ./ "
* In case of more than one argument, print "hey hey...too many!"

**Solution**

```
#!/usr/bin/env bash

set -eu

main() {
  case $# in
    0) printf "%s" "Usage: ./<program name> <argument>"; return 1 ;;
    1) printf "%s" "Got it: $1"; return 0 ;;
    *) return 1 ;;
  esac
}

main "$@"
```

### **Empty Files**

**Objectives**

* Write a script to remove all the empty files in a given directory (including nested directories)

**Solution**

```
#! /bin/bash
for x in *
do
    if [ -s $x ]
    then
        continue
    else
        rm -rf $x
    fi
done
```

### **It's Alive!**

**Objectives**

Write a script to determine whether a given host is down or up


**Solution**


```
#!/usr/bin/env bash
SERVERIP=<IP Address>
NOTIFYEMAIL=test@example.com

ping -c 3 $SERVERIP > /dev/null 2>&1
if [ $? -ne 0 ]
then
   # Use mailer here:
   mailx -s "Server $SERVERIP is down" -t "$NOTIFYEMAIL" < /dev/null
fi
```
