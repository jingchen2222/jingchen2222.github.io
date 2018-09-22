---
layout: "post"
title: "Scala_for_impatients_Ch3"
date: "2017-03-16 17:23"

tags:
  - 读书笔记
  - book:Scala for impatients
  - scala

categories:
  - Study
  - Scala
---

## Note

- Use an Array if the length is fixed, and an ArrayBuffer if the length can vary.
- Don’t use new when supplying initial values.
- Use () to access elements.
- Use for (elem <- arr) to traverse the elements.
    - elem <- 0 until s.length
    - elem <- 0 until s. length reverse
    - elem <- 0 untial (s.length, 2) reverse // visit every second element


- Use for (elem <- arr if . . . ) . . . yield . . . to transform into a new array.
    - The for (...) yield loop creates a new collection of the same type as the original collection.
    - The result contains the expressions after the yield, one for each iteration of the loop.
    - Keep in mind that the result is a new collection—the original collection is not affected.
- Scala and Java arrays are interoperable; with ArrayBuffer, use scala.collection. JavaConversions.
    - The Scala buffer is wrapped into an object of a Java class that implements the java.util.List interface.

<!--more-->
## Exercises
1. Write a code snippet that sets a to an array of n random integers between 0 (inclusive) and n (exclusive).


    ```
    def randNum(n:Int):Array[Int] = {
      val a = scala.util.Random
      val b = new Array[Int](n)
      for(i <- 0 until b.length) {
        b(i) = a.nextInt(n)
      }
      b
    }

    val n = 10
    println(randNum(n).mkString(" "))


    ```
2. Write a loop that swaps adjacent elements of an array of integers. For example, Array(1, 2, 3, 4, 5) becomes Array(2, 1, 4, 3, 5).


    ```


    def swapsArray(a:Array[Int]) = {

      val b = new ArrayBuffer[Int]()

      for (i <- 0 until a.length if i%2 == 0) {

        if (i+1 < a.length) {
          b += a(i+1)
        }
        b += a(i)
      }
      b

    }
    val a = Array(1,2,3,4,5,6,7,8,9,10)
    val b = swapsArray(a)
    println(b.mkString(","))
    ```

3. Repeat the preceding assignment, but produce a new array with the swapped values. Use for/yield.

    ```

      def swapsArray2(a:Array[Int]) = {
          val temp = for(i <- 0 until (a.length, 2)) yield {
            if (i < a.length-1) Array(a(i+1), a(i))
            else Array(a(i))
          }
          val b = temp.flatten.toArray
          println(b.mkString(","))
          b
      }

      val a = Array(1,2,3,4,5,6,7,8,9,10)
      val b = swapsArray(a)
      println(b.mkString(","))
    ```
4. Given an array of integers, produce a new array that contains all positive values of the original array, in their original order, followed by all values that are zero or negative, in their original order.


    ```
    def generate(a:Array[Int]) = {
        val pos = for(i <- 0 until a.length if a(i)>0) yield {
          a(i)
        }
        val neg = for(i <- 0 until a.length if a(i)<=0) yield {
          a(i)
        }
        val b = Array(pos, neg).flatten.toArray
        b
    }

    val a = Array(1,2,-4,-1,0,3,4,5,-8,6,7,8,9,10,-4,-5,-6)
    val b = generate(a)
    println(b.mkString(","))

    ```
5. How do you compute the average of an Array[Double]?


    ```
      def avg(d: Array[Double]) = {
        if (d.length <=0) 0
        else {
          d.sum/d.length
        }
      }
    ```
6. How do you rearrange the elements of an Array[Int] so that they appear in


    ```
    for(i <- 0 until h.length/2) {
     val tmp = h(i)

     h(i) = h(h.length-i-1)
     h(h.length-i-1) = tmp
     }
    ```
reverse sorted order? How do you do the same with an ArrayBuffer[Int]?
7. Write a code snippet that produces all values from an array with duplicates
removed. (Hint: Look at Scaladoc.)

    ```
      val h = Array(1, 2, 2.3, 4.4,5.6,1,2,9,3,2)
      println(h.distinct.mkString(","))
    ```


> def distinct: Array[T]
>
> Builds a new mutable indexed sequence from this mutable indexed sequence without any duplicate elements.






8. Rewrite the example at the end of Section 3.4, “Transforming Arrays,” on page 34 using the drop method for dropping the index of the first match. Look the method up in Scaladoc.


9. Make a collection of all time zones returned by java.util.TimeZone.getAvailableIDs that are in America. Strip off the "America/" prefix and sort the result.


      ```
        val zonetime= java.util.TimeZone.getAvailableIDs
        val timeList = zonetime.filter(_.take(8)=="America/")
        val sortedAmericaTime = timeList.map(_.drop(8)).sorted

      ```
10. Import java.awt.datatransfer._ and make an object of type SystemFlavorMap with the call
val flavors = SystemFlavorMap.getDefaultFlavorMap().asInstanceOf[SystemFlavorMap]
Then call the getNativesForFlavor method with parameter DataFlavor.imageFlavor and get the return value as a Scala buffer. (Why this obscure class? It’s hard to find uses of java.util.List in the standard Java library.)
