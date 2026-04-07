## 1. you must read an integer, a double, and a String from stdin, then print the values according to the instructions in the Output Format section below. To make the problem a little easier, a portion of the code is provided for you in the editor.

Input Format

There are three lines of input:
  
  The first line contains an integer.  
  The second line contains a double.  
  The third line contains a String.  

Output Format

There are three lines of output:

  On the first line, print String: followed by the unaltered String read from stdin.
  On the second line, print Double: followed by the unaltered double read from stdin.
  On the third line, print Int: followed by the unaltered integer read from stdin.
  To make the problem easier, a portion of the code is already provided in the editor.

Note: _If you use the nextLine() method immediately following the nextInt() method, recall that nextInt() reads integer tokens; because of this, the last newline character for that line of integer input is still queued in the input buffer and the next nextLine() will be reading the remainder of the integer line (which is empty)._

**Solution:**

```java
        Scanner scan = new Scanner(System.in);
        int i = scan.nextInt();

        // Write your code here.
        double d = scan.nextDouble();
        scan.nextLine();
        String s = scan.nextLine();

        System.out.println("String: " + s);
        System.out.println("Double: " + d);
        System.out.println("Int: " + i);
        
        scan.close();
```

Sample Input

    42
    3.1415
    Welcome to HackerRank's Java tutorials!

Sample Output

    String: Welcome to HackerRank's Java tutorials!
    Double: 3.1415
    Int: 42

----

## 2. Java's System.out.printf function can be used to print formatted output. The purpose of this exercise is to test your understanding of formatting output using printf.

To get you started, a portion of the solution is provided for you in the editor; you must format and print the input to complete the solution.

Input Format

    Every line of input will contain a String followed by an integer.
    Each String will have a maximum of  alphabetic characters, and each integer will be in the inclusive range from  to .

Output Format

    In each line of output there should be two columns:
    The first column contains the String and is left justified using exactly  characters.
    The second column contains the integer, expressed in exactly  digits; if the original input has less than three digits, you must pad your output's leading digits with zeroes.

**Solution:**

```java
    System.out.printf("%-14s %03d", s1, x);
```

Sample Input

    java 100
    cpp 65
    python 50

Sample Output

      ================================
      java           100 
      cpp            065 
      python         050 
      ================================

----

## 3. Given an integer, , print its first  multiples. Each multiple  (where ) should be printed on a new line in the form: N x i = result.

Input Format

      A single integer.

Output Format

    Print  lines of output; each line  (where ) contains the  of  in the form:
    N x i = result.

Sample Input

    2

Sample Output

    2 x 1 = 2
    2 x 2 = 4
    2 x 3 = 6
    2 x 4 = 8
    2 x 5 = 10
    2 x 6 = 12
    2 x 7 = 14
    2 x 8 = 16
    2 x 9 = 18
    2 x 10 = 20

```java
    int N = Integer.parseInt(bufferedReader.readLine().trim());
    
    for(int i=1;i<=10;i++){
        int result = N*i;
        System.out.printf("%d x %d = %d", N, i, result);
        System.out.println();
    }
```

----

