---
layout: "post"
title: "Scala_for_impatients_Ch2"
date: "2017-03-15 18:01"

tags:
  - 读书笔记
  - book:Scala for impatients
  - scala

categories:
  - scala


---

## Note
-  An if expression has a value.
- A block has a value—the value of its last expression.
- The Scala for loop is like an “enhanced” Java for loop.
    - for ( i -> 0 to n)
    - for ( i -> 0 until n)
- Semicolons are (mostly) optional.
    - Need when: more than one statements on a single line
- The void type is Unit.
- Avoid using return in a function.
    - With a recursive function, you must specify the return type.
- Beware of missing = in a function definition.
    - Because the procedure doesn’t return any value, we omit the = symbol
    - It is a common error to accidentally omit the = in a function definition.You then get an error message at the point where the function is called, and you are told that Unit is not acceptable at that location.
- Exceptions work just like in Java or C++, but you use a “pattern matching” syntax for catch.
- Scala has no checked exceptions.
- Variable Arguments
    -  The remedy is to tell the compiler that you want the parameter to be considered an argument sequence. Append : _*, like this:
        - `val s = sum(1 to 5: _*) // Consider 1 to 5 as an argument sequence`

<!--more-->
## Exercises

1. The signum of a number is 1 if the number is positive, –1 if it is negative, and 0 if it is zero. Write a function that computes this value.

    Answer:
    ```
    def signum(i:Int)={
      if (i>0) 1 else {
        if (i<0) -1 else 0
      }
    }
    ```

2. What is the value of an empty block expression {}? What is its type?
  Answer: Unit

3. Come up with one situation where the assignment x = y = 1 is valid in Scala.
(Hint: Pick a suitable type for x.)

    Answer: x is type of Unit
    ```
      var y = 1
      var x = {}
      x = y = 1
    ```
4. Write a Scala equivalent for the Java loop
for (int i = 10; i >= 0; i--) System.out.println(i);

    Answer:
    ```
    for(i<- 0 to 10) println(10-i)

    // another version of Answer

    for(i <- 0 to 10 reverse)print(i)
    ```

5. Write a procedure countdown(n: Int) that prints the numbers from n to 0.

    Answer:
    ```
    def countdown(n:Int) {
      for(i<- 0 to n reverse) print(i)
    }

    // another version:  

    def countdown(n:Int) {
      0 to n reverse foreach print
    }
    ```
6. Write a for loop for computing the product of the Unicode codes of all letters in a string. For example, the product of the characters in "Hello" is 825152896.


    Answer:
    ```
      def productUcode(str: String):Long = {
        var result: Long = 1
        for(c<-str) result *= c.toLong
        result
      }
    ```
7. Solve the preceding exercise without writing a loop. (Hint: Look at the StringOps Scaladoc.)


    Answer:
    ```
      def productUcode(str: String):Long = {
        var result: Long = 1
        str.foreach(result *= _.toLong)
        result
      }
    ```
8. Write a function product(s : String) that computes the product, as described in the preceding exercises.

    same with above

9. Make the function of the preceding exercise a recursive function

    Answer:
    ```
      def productUcode2(str:String):Long = {
        if(str.length == 1) return str.head.toLong
        else str.head.toLong * productUcode2(str.tail)
      }
    ```


10. Write a function that computes xn, where n is an integer. Use the following recursive definition:
• xn = y2 if n is even and positive, where y = xn / 2.
• xn = x· xn – 1 if n is odd and positive.
• x0 = 1.
• xn = 1 / x–n if n is negative.
Don’t use a return statement.


    Answer:
    ```
    def computesXn(x:Double, n:Int):Double = {
      if (n==0) 1.toDouble else {
        if (n < 0) 1/computesXn(x, -n) else {
          if (n%2 == 0) {
            val y = computesXn(x, n/2); y*y
            } else {
            x*computesXn(x, n-1)
          }
        }
      }
    }

    //

    def computesXn(x:Double, n:Int):Double = {
      if (n==0) 1.toDouble
      else if (n>0 && n%2==0) {
        val y = computesXn(x, n/2); y*y
      }
      else if (n>0 && n%2==1) {
        x*computesXn(x, n-1)
      }
      else 1/computesXn(x, -n)
    }
    ```
