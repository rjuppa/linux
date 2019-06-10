### bash
```

vim script.sh
chmod +x script.sh
./script.sh


#!/bin/bash
# print text
echo "Hello World!"

echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

VARIABLES
$0 - The name of the Bash script.
$1 - $9 - The first 9 arguments to the Bash script. (As mentioned above.)
$# - How many arguments were passed to the Bash script.
$@ - All the arguments supplied to the Bash script.
$? - The exit status of the most recently run process.
$$ - The process ID of the current script.
$USER - The username of the user running the script.
$HOSTNAME - The hostname of the machine the script is running on.
$SECONDS - The number of seconds since the script was started.
$RANDOM - Returns a different random number each time is it referred to.
$LINENO - Returns the current line number in the Bash script.

#!/bin/bash
# A simple copy script
cp $1 $2


#!/bin/bash
# A simple variable example
myvariable=Hello
anothervar=Fred
echo $myvariable $anothervar

newvar="More $myvariable"   # var in string -> Double will do variable substitution, single will not.

sampledir=/etc
ls $sampledir
------------------------

# Save the output of a command into a variable
myvar=$( ls /etc | wc -l )

#!/bin/bash
# User input 
echo Hello, who am I talking to?
read varname
echo "It\'s nice to meet you $varname"

read car1 car2 car3


#!/bin/bash
# Length of a variable
a='Hello World'
echo ${#a} # 11
b=4953
echo ${#b} # 4


#!/bin/bash
# IF statement
if [ $1 -gt 100 ]
then
  echo Hey that\'s a large number.
else
  echo Hey that\'s a small number.
fi

also elif
not - !
and - &&
or - ||
for integer use: -eq, -gt, -lt
for string use: !=  ,  =
-d FILE	exists and is a directory.
-e FILE	exists.
-r FILE	exists and the read permission is granted.
-s FILE	exists and it's size is greater than zero (ie. it is not empty).
-w FILE	exists and the write permission is granted.
-x FILE	exists and the execute permission is granted.

if [ -r $1 ] && [ -s $1 ]
then
  echo This file is useful.
fi
---------------------------------------

#!/bin/bash
# while loop
counter=1
while [ $counter -le 10 ]
do
  echo $counter
  ((counter++))
done
echo All done


#!/bin/bash
# for loop
names='Stan Kyle Cartman'
for name in $names
do
  echo $name
done
echo All done


#!/bin/bash
# range loop
for value in {1..5}
do
  echo $value
done
echo All done

In loops you can use: break, continue


#!/bin/bash
# function
print_something () {
  echo Hello I am a function
}
print_something


#!/bin/bash
# params and return value in functions
lines_in_file () {
  cat $1 | wc -l
}

NAME="Hello world!"
num_lines=$( lines_in_file $NAME )
echo The file $NAME has $num_lines lines in it.

```
