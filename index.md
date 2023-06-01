# Part 1 – Debugging Scenario

### Original Post:
**How do I fix this bug?**

Hi! I have encountered this bug while writing the grading script during week 6's lab (https://ucsd-cse15l-s23.github.io/week/week6/#grading-script). My bash script is supposed to report the grade based on the JUnit output, but it fails. Here's my code and output:
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
Overall, your code looks good, except some minor issues. The -d option in javac is used to specify where to place generated class files. If no directory is specified, the compiler does not report an error, instead it just doesn't generate the class files.


# Part 2: Reflection

The most important takeaway from the second half of the quarter is learning how to write bash scripts. I get to know that tutors use bash scripts to grade our code on Gradescope. Writing bash scripts on my own make me understand how to run a series of commands and display certain output by running a single bash file.
