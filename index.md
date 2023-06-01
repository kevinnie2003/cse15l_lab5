# Part 1 – Debugging Scenario

### Original Post:
**How do I fix this bug?**

Hi! I have encountered this bug while writing the grading script during week 6's lab (https://ucsd-cse15l-s23.github.io/week/week6/#grading-script). My bash script is supposed to report the grade based on the JUnit output, but it fails. Specifically, when I run this command: bash grade.sh <student-submission-from-some-group, a github link>.

Here's the directory structure:

<img width="987" alt="截屏2023-05-31 21 09 25" src="https://github.com/kevinnie2003/cse15l_lab5/assets/122497019/558bb307-89a5-4f9e-94a7-0f47d0f66531">

Here's my code and output:

**grade.sh**
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
**TestListExamples.java**
```
import static org.junit.Assert.*;
import org.junit.*;
import java.util.Arrays;
import java.util.List;

class IsMoon implements StringChecker {
  public boolean checkString(String s) {
    return s.equalsIgnoreCase("moon");
  }
}

public class TestListExamples {
  @Test(timeout = 500)
  public void testMergeRightEnd() {
    List<String> left = Arrays.asList("a", "b", "c");
    List<String> right = Arrays.asList("a", "d");
    List<String> merged = ListExamples.merge(left, right);
    List<String> expected = Arrays.asList("a", "a", "b", "c", "d");
    assertEquals(expected, merged);
  }
}
```
Terminal Output:

<img width="902" alt="截屏2023-05-31 21 09 39" src="https://github.com/kevinnie2003/cse15l_lab5/assets/122497019/88a85280-8547-4d88-9cb8-5c2e03ca0a1c">

Why does it say "error: invalid flag"? Thank you for answering!

### Response from TA:
Overall, your code looks good, except some minor issues. 

The -d option in javac is used to specify where to place generated class files. If no directory is specified, the compiler does not report an error, instead it just doesn't generate the class files. This is why you're seeing the "error: invalid flag" message in your terminal output - it's coming from the javac command because it's not being used correctly. Can you think of a way to use javac correctly?

### Response from student:
Thank you so much! I have tried to remove '-d' from line 23, now it works fine. Here's the output:

<img width="924" alt="截屏2023-05-31 21 10 01" src="https://github.com/kevinnie2003/cse15l_lab5/assets/122497019/c9443c50-d143-4356-b8fe-954771419c9b">

The bug is that I does not specify after the "-d" flag while using the javac command. To fix this bug, I should either remove the "-d" flag entirely, or provide a directory after the "-d" flag where the .class files will be placed. I want to keep the .class files in the current directory, so I simply remove the "-d" flag. I was originally planning to use "-d" flag to make sure it is placed in the correct directory but I forgot to implement that correctly.

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
Here's the final directory structure:
<img width="675" alt="截屏2023-05-31 21 10 41" src="https://github.com/kevinnie2003/cse15l_lab5/assets/122497019/a0ad19ce-532c-44c7-96ff-26ab82aa107c">

# Part 2: Reflection

The most important takeaway from the second half of the quarter is learning how to write bash scripts. Bash scripting is a powerful tool, enabling us to automate repetitive tasks, run series of commands, and manage files and processes. I get to know that tutors also use bash scripts to grade our code on Gradescope. Writing bash scripts on my own make me understand how to run a series of commands and display certain output by running a single bash file. I believe writing bash scripts will be useful in future.
