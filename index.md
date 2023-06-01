# Part 1 – Debugging Scenario

### Original Post:
**How do I fix this bug?**

Hi! I have encountered this bug while writing the grading script during week 6's lab (https://ucsd-cse15l-s23.github.io/week/week6/#grading-script). My bash script is supposed to report the grade based on the JUnit output, but it fails. Specifically, when I run this command: bash grade.sh <student-submission-from-some-group, a github link>.

Here's the directory structure:

<img width="979" alt="截屏2023-05-31 20 53 06" src="https://github.com/kevinnie2003/cse15l_lab5/assets/122497019/6f198d81-57b9-4e88-a266-a6f9d5196feb">


Here's my code and output:
```
CPATH='.:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar'

rm -rf student-submission
rm -rf grading-area

mkdir grading-area

git clone $1 student-submission
echo 'Finished cloning'

if [ $? -ne 0 ]; then
    echo 'Failed to clone'
    exit 1
fi

cd grading-area
cp -r ../student-submission/* .
cp -r ../TestListExamples.java .
cp -r ../lib .
echo 'Finished copying'

set +e
javac -d -cp $CPATH *.java
if [ $? -ne 0 ]; then
    echo 'Failed to compile'
    exit 1
fi

echo 'Finished compiling'

java -cp $CPATH org.junit.runner.JUnitCore TestListExamples > output.txt
echo 'Finished grading'
tail -n 3 output.txt
```
Terminal Output:
<img width="907" alt="截屏2023-05-31 20 43 02" src="https://github.com/kevinnie2003/cse15l_lab5/assets/122497019/eb9aa185-0273-4ee6-bda8-95196d1b8cae">

Why does it say "error: invalid flag"? Thank you for answering!

### Response from TA:
Overall, your code looks good, except some minor issues. 

The -d option in javac is used to specify where to place generated class files. If no directory is specified, the compiler does not report an error, instead it just doesn't generate the class files. This is why you're seeing the "error: invalid flag" message in your terminal output - it's coming from the javac command because it's not being used correctly. Can you think of a way to use javac correctly?

### Response from student:
Thank you so much! I have tried to remove '-d' from line 23, now it works fine. Here's the output:

<img width="897" alt="截屏2023-05-31 20 52 23" src="https://github.com/kevinnie2003/cse15l_lab5/assets/122497019/234e4bf3-8ab0-42e0-95ea-d6cab328e8b7">

To fix this bug, I should either remove the "-d" flag entirely, or provide a directory after the "-d" flag where the .class files will be placed. I want to keep the .class files in the current directory, so I simply remove the "-d" flag. I was originally planning to use "-d" flag to make sure it is placed in the correct directory but I forgot to implement that correctly.

Here's the fixed code:
```
CPATH='.:lib/hamcrest-core-1.3.jar:lib/junit-4.13.2.jar'

rm -rf student-submission
rm -rf grading-area

mkdir grading-area

git clone $1 student-submission
echo 'Finished cloning'

if [ $? -ne 0 ]; then
    echo 'Failed to clone'
    exit 1
fi

cd grading-area
cp -r ../student-submission/* .
cp -r ../TestListExamples.java .
cp -r ../lib .
echo 'Finished copying'

set +e
javac -cp $CPATH *.java # Remove "-d"
if [ $? -ne 0 ]; then
    echo 'Failed to compile'
    exit 1
fi

echo 'Finished compiling'

java -cp $CPATH org.junit.runner.JUnitCore TestListExamples > output.txt
echo 'Finished grading'
tail -n 3 output.txt
```

# Part 2: Reflection

The most important takeaway from the second half of the quarter is learning how to write bash scripts. I get to know that tutors use bash scripts to grade our code on Gradescope. Writing bash scripts on my own make me understand how to run a series of commands and display certain output by running a single bash file.
