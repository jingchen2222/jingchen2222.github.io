---
layout: "post"
title: "Scala_for_impatients_Ch4"
date: "2017-03-17 22:22"

tags:
  - 读书笔记
  - book:Scala for impatients
  - scala

categories:
  - scala
---
# Chapter 4: maps and tuples
## Note


- Scala has a pleasant syntax for creating, querying, and traversing maps.
    - the call map.get(key) returns an Option object that is either Some(value for key) or None.
    - for ((k, v) <- map) process k and v
    - for (v <- scores.values) println(v)
- You need to choose between mutable and immutable maps.
    - updating map values only when it is mutable
    - You can’t update an immutable map, but you can do something that’s just as useful—obtain a new map that has the desired update:
- By default, you get a hash map, but you can also get a tree map.
    -  You might want a tree map if you need to visit the keys in sorted order.
- You can easily convert between Scala and Java maps.
    - First: import related class
        - e.g: `import scala.collection.JavaConversions.mapAsScalaMap`
        -
    - Second: use java class
        - e.g: `val scores: scala.collection.mutable.Map[String, Int] = new java.util.TreeMap[String, Int]`
- Tuples are useful for aggregating values.
    - Zipping is used to bundle together values so that they can be pro- cessed together
      - If you have a collection of keys and a parallel collection of values, then zip them up and turn them into a map like` this:keys.zip(values).toMap`

<!--more-->
## Exercises
1. Set up a map of prices for a number of gizmos that you covet. Then produce a second map with the same keys and the prices at a 10 percent discount.


    ```
    val prices = Map("A" -> 10, "B" -> 20, "C" -> 8)
    val secondPrices = for((k,v) <- prices) yield (k,0.9*v)
    ```
2. Write a program that reads words from a file. Use a mutable map to count how often each word appears. To read the words, simply use a java.util.Scanner:
val in = new java.util.Scanner(java.io.File("myfile.txt")) while (in.hasNext()) process in.next()
Or look at Chapter 9 for a Scalaesque way.
At the end, print out all words and their counts.


    ```
    // 1. reads words from a File
    val source = Source.fromFile("../README.md").mkString
    val words = source.split("\\s+")

    // 2. Use a mutable map to count how often each word appears.
    val wordsCount = new HashMap[String, Int]()
    for(k <- words) {
      wordsCount(k) = wordsCount.getOrElse(k,0) + 1
    }
    println(wordsCount.mkString("\n"))
    //


    ```
3. Repeat the preceding exercise with an immutable map.

    ```
      var wordMap2 = Map[String,Int]()
      val in = new java.util.Scanner(new java.io.File("../README.MD"))

      while(in.hasNext()) {
        val key = in.next()
        wordMap2 += (key -> (wordMap2.getOrElse(key,0)+1))
      }
      println(wordMap2)
    ```
4. Repeat the preceding exercise with a sorted map, so that the words are printed in sorted order.


    ```
    var wordMap3 = scala.collection.immutable.SortedMap[String,Int]()
    val in = new java.util.Scanner(new java.io.File("../README.MD"))

    while(in.hasNext()) {
      val key = in.next()
      wordMap3 += (key -> (wordMap3.getOrElse(key,0)+1))
    }
    println(wordMap3)
    ```

5. Repeat the preceding exercise with a java.util.TreeMap that you adapt to the Scala API.


    ```
    val wordMap4:scala.collection.mutable.Map[String,Int] = new java.util.TreeMap[String,Int]
    val in = new java.util.Scanner(new java.io.File("../README.MD"))

    while(in.hasNext()) {
      val key = in.next()
      wordMap4 += (key -> (wordMap4.getOrElse(key,0)+1))
    }
    println(wordMap4)

    ```
6. Define a linked hash map that maps "Monday" to java.util.Calendar.MONDAY, and similarly for the other weekdays. Demonstrate that the elements are visited in insertion order.


    ```
    import java.util.Calendar
    import scala.collection.mutable.LinkedHashMap


    val map = new LinkedHashMap[String,Int]  

    map += ("Monday"->Calendar.MONDAY)  
    map += ("Tuesday"->Calendar.TUESDAY)  
    map += ("Wednesday"->Calendar.WEDNESDAY)  
    map += ("Thursday"->Calendar.THURSDAY)  
    map += ("Friday"->Calendar.FRIDAY)  
    map += ("Saturday"->Calendar.SATURDAY)  
    map += ("Sunday"->Calendar.SUNDAY)  


    println(map.mkString(","))  

    ```

7. Print a table of all Java properties, like this:
49

    ```
    java.runtime.name sun.boot.library.path java.vm.version java.vm.vendor java.vendor.url path.separator java.vm.name
    | Java(TM) SE Runtime Environment
    | /home/apps/jdk1.6.0_21/jre/lib/i386 | 17.0-b16
    | Sun Microsystems Inc.
    | http://java.sun.com/
    | :
    | Java HotSpot(TM) Server VM

    ```
You need to find the length of the longest key before you can print the table.


    ```
      import scala.collection.JavaConversions.propertiesAsScalaMap
      val pros:scala.collection.Map[String,String] = System.getProperties()
      val keys = pros.keySet()
      val keys = pros.keySet
      val keyLens = for(key <- keys) yield key.length
      val maxLen = keyLens.max
      for (key <- keys) {
        print(key)
        print(" " * (maxLen - key.length))
        print(" | ")
        println(pros(key))
      }
    ```
8. Write a function minmax(values: Array[Int]) that returns a pair containing the smallest and largest values in the array.

    ```

      val values = Array(1,2,3,4,-7,-5,3,-1,6,0)

      def minmax(values: Array[Int]) = {
        val minV = values.min
        val maxV = values.max
        (minV, maxV)
      }

      minmax(values)
    ```
9. Write a function lteqgt(values: Array[Int], v: Int) that returns a triple containing the counts of values less than v, equal to v, and greater than v.


    ```
    def iteqgt(values:Array[Int],v:Int)={
      (values.count(_<v),values.count(_==v),values.count(_>v))
    }
    ```
10. What happens when you zip together two strings, such as "Hello".zip("World")? Come up with a plausible use case.
