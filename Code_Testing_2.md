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

## 3. Given an integer a, b, print its first  multiples. Each multiple  (where ) should be printed on a new line in the form: N x i = result.

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

**Solution:**

```java
    int N = Integer.parseInt(bufferedReader.readLine().trim());
    
    for(int i=1;i<=10;i++){
        int result = N*i;
        System.out.printf("%d x %d = %d", N, i, result);
        System.out.println();
    }
```

----

## 4. We use the integers a, b, and n to create the following series:
<img width="728" height="167" alt="image" src="https://github.com/user-attachments/assets/069319d9-902f-4c24-9aad-9b9f61d076fb" />

**Solution:**

```java
     public static void main(String []argh){
        Scanner in = new Scanner(System.in);
        int t=in.nextInt();
        for(int i=0;i<t;i++){
            int a = in.nextInt();
            int b = in.nextInt();
            int n = in.nextInt();
            
            // System.out.print(a +" "+ b +" "+ n);
            for(int j=1;j<=n;j++){
              int frsl = a + calcus(1, b, j);
                System.out.print(frsl + " ");
            }
            System.out.println();
        }
        in.close();
    }
    
    private static int calcus(int k, int b, int n){
        int i = 0; //looping starting element
        int r = 0; //storing total value
        while(i < n){
            int result = k * b;
            //System.out.printf("%d . %d = %d", k, b, result);
            k = k + k; // squaring element
            i++; //increment element until n equal
            r = r + result; // add the result.
        }
        return r;
    }
    
```

Output Format

For each query, print the corresponding series on a new line. Each series must be printed in order as a single line of  space-separated integers.

Sample Input

      2
      0 2 10
      5 3 5
      
Sample Output

      2 6 14 30 62 126 254 510 1022 2046
      8 14 26 50 98

----

## 5. Java has 8 primitive data types; char, boolean, byte, short, int, long, float, and double. For this exercise, we'll work with the primitives used to hold integer values (byte, short, int, and long):

A byte is an 8-bit signed integer.
A short is a 16-bit signed integer.
An int is a 32-bit signed integer.
A long is a 64-bit signed integer.
Given an input integer, you must determine which primitive data types are capable of properly storing that input.

To get you started, a portion of the solution is provided for you in the editor.

**Solution:**

```java

        for(int i=0;i<t;i++)
        {
            try
            {
                long x=sc.nextLong();
                System.out.println(x+" can be fitted in:");
                if(x>=-128 && x<=127)System.out.println("* byte");
                if(x>=Short.MIN_VALUE && x<=Short.MAX_VALUE)System.out.println("* short");
                if(x>=Integer.MIN_VALUE && x <= Integer.MAX_VALUE)System.out.println("* int");
                if(x>=Long.MIN_VALUE && x <= Long.MAX_VALUE)System.out.println("* long");
                //Complete the code
            }
            catch(Exception e)
            {
                System.out.println(sc.next()+" can't be fitted anywhere.");
            }
        }

```

Sample Input

        5
        -150
        150000
        1500000000
        213333333333333333333333333333333333
        -100000000000000
        
Sample Output

        -150 can be fitted in:
        * short
        * int
        * long
        150000 can be fitted in:
        * int
        * long
        1500000000 can be fitted in:
        * int
        * long
        213333333333333333333333333333333333 can't be fitted anywhere.
        -100000000000000 can be fitted in:
        * long

----

## 6. Java: Encryption and Decryption

Decrypt a message that was encrypted using the following logic:

First the words in the sentence are reversed. For example, "welcome to hackerrank" becomes "hackerrank to welcome".

For each word, adjacent repeated letters are compressed in the format <character><frequency>. For example, "mississippi" becomes "mis2is2ip2i" or "baaa" becomes "ba3". Note the format is not applied for characters with frequency 1. Also, the frequency will be no greater than 9.

Return the decrypted string.

Example

encryptedMessage = 'world hel2o'

Expand each word to get 'world hello'. Now reverse the words to get 'hello world', the return value.

**Solution:**

```java
      String decMsg = "world hel2o";

      // 1. Seperate the character.
      char[] ch = decMsg.toCharArray();
      StringBuilder sb = new StringBuilder();

      // 2. declare temp value.
      char temp = 0;
      for(char c:ch){
          try{
              // 3. input c convert into Integer, if not get an exception repeat the Last character using while loop until end. 
              int i = Integer.parseInt(String.valueOf(c));

              // 5. the frequency will be no greater than 9
              if(i > 9){
                  i = 9;
              }
              while(1<i){
                  sb.append(temp);
                  i--;
              }
          } catch (Exception e) {
              // 4. if you get an exception add the value in below varriable, temp used for repeat the character.
              temp = c;
              sb.append(c);
          }
      }

      String result = sb.toString();
      // 6. split the words by " ".
      List<String > rr = Arrays.asList(result.split(" "));
      System.out.println(rr);

      // 7. reverse the words (not character) to print the output.
      for(int i = rr.size()- 1; i>=0;i--){
          if(i != rr.size()){
              System.out.print(" ");
          }
          System.out.print(rr.get(i));
      }

```
