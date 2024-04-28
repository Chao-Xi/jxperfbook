# GREP + AWK + SED Practice

```
docker run -it --rm bluerdocker/grep-sed-awk:v1 sh
```

## 1 GREP

```
grepexercises
```

### grep help

```
$ grep --help
usage: grep [-abcdDEFGHhIiJLlMmnOopqRSsUVvwXxZz] [-A num] [-B num] [-C[num]]
        [-e pattern] [-f file] [--binary-files=value] [--color=when]
        [--context[=num]] [--directories=action] [--label] [--line-buffered]
        [--null] [pattern] [file ...]
```

### Exercises

1.Display lines containing **`an`** from the **`sample.txt`** input file.

* **`grep 'an‘ sample.txt`**

output:

```
banana
mango
```

2.Display lines containing **`do`** as a whole word from the sample.txt input file.

* `grep -w 'do' sample.txt`
	* **-w: Match whole word**
	

3.Display lines from sample.txt that satisfy both of these conditions:

* **`he`** matched irrespective of case
* **either `World` or `Hi`** matched case sensitively 

* **`grep -i 'he' sample. txt | grep -e 'World' -e 'Hi'`**
* **`grep -i 'he' sample.txt | grep -E 'World|Hi'`**
	* **`–i`**: Ignores, case for matching
	* **`-E`**: Treats pattern as an extended regular expression (ERE)
	* **`-e exp`**: Specifies expression with this option. Can use multiple times.

Expected output

```
Hello world
Hi there
```

Expected output:

`Just do-it`

4.**Display lines** from **`code.txt`** containing **`fruit[0]`** literally,

* **`grep -F 'fruit[0]' code.txt`**
	* `-f file`: Takes patterns from file, one per line.

**code.txt**

```
fruit = []
fruit[0] = 'apple'
def fruit():
	print ('banana')
```

Expected output

```
fruit[0] = 'apple'
```

5.Display only the **first two matching lines** **containing `t`** from the `sample.txt` input file.


**`grep -m2 't' sample.txt`**

**Output**

```
Hi there
Just do-it
```

6.Display only the **first three matching lines** that **do not contain `he` from the sample.txt** input
file.

**`grep -m3 -V 'he' sample.txt`**

* `-v` : This prints out all the lines that do not matches the pattern

**Output**

```
Hello World

How are you
```

7.Display lines from **`sample.txt`** that contain **`do`** along with **line number prefix**

* **`grep -n 'do' sample.txt`**
	* **`-n` Display the matched lines and their line numbers.**

**Output**

```
6: Just do-it
13: Much ado about nothing
```

8.For the input file `sample.txt`, **count the number of times the string `he` is present**, irrespective of case.

* `grep -io 'he' sample.txt | wc -l`

**Expected output: 5**


9.For the input file sample.txt, **count the number of empty lines**.

* `grep -cx '' sample.txt`
	* `-c` This prints only a count of the lines that match a pattern
	* `-x`	Match only whole lines.
 
**Output: 4**


9.present in the `terms.txt` file. Results should be prefixed with the corresponding input filename.

**`grep -Ff terms.txt sample.txt code.txt`**


```
sample.txt: How are you
sample. txt: mango
sample.txt :Much ado about nothing
sample.txt: Adios amigo
code.txt:fruit[o] = 'apple'
```

* `-f` file Takes patterns from file, one per line.

10.For the input file `sample.txt`, display lines containing `amigo` prefixed by the input filename as well as the line number.

**`grep -Hn 'amigo' sample.txt`**

`-n` Display the matched lines and their line numbers.

**Output:**

```
sample.txt: 15: Adios amigo
```

e You have an Azure AD tenant named contoso.com.
You have two external partner organizations named fabrikam.com and litwareinc.com. Fabrikam.com is Cigured as a connected organization.
You create an access package as shown in the Access package exhibit. (Click the Access package tab.)