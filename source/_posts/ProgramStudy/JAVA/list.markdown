---
layout: "post"
title: "List study"
date: "2018-09-01 17:01"

tags:
  - java
  - collection

categories:
  - java


---
## List 


### Overview
##### List族最重要的几个特点：
* 有序
* 允许重复元素
* 支持add, remove, set, get
* 可以随机访问元素


##### Lis族集合类：


![java-collection-hierarchy.jpeg](https://www.programcreek.com/wp-content/uploads/2009/02/java-collection-hierarchy.jpeg)

* List族中，最主要的三种集合是`ArrayList`,`Vector`和`LinkedList`，后面将对这三种类进行分析和比较。
* 可以简单对比一下List和Set

|  |List  |Set  |
| --- | --- | --- |
|有序 | Y |N  |
|重复元素 |Y  |N  |
|随机访问 |Y  |N  |

<!--more-->
### ArrayList

Java Platform SE 8的描述：

> Resizable-array implementation of the List interface. Implements all optional list operations, and permits all elements, including null. In addition to implementing the List interface, this class provides methods to manipulate the size of the array that is used internally to store the list. (This class is roughly equivalent to Vector, except that it is unsynchronized.)

* 可变长数组
* 实现所有list的操作
* 允许null
* 和Vector相似，但是unsynchronized

##### <span style="color:#ab4642">ArrayList是一种能够自动扩充长度的数组。</span>


#### 性能
> The size, isEmpty, get, set, iterator, and listIterator operations run in constant time.
> The add operation runs in amortized constant time, that is, adding n elements requires O(n) time. 

* **ArrayList**的性能可以类比<span style="color:#ab4642">数组</span>的性能，在随机访问性能好。增、删操作性能差。


#### 线程安全
> <span style="color:#ab4642">Note that this implementation is not synchronized...</span>

`Collections.synchronizedList`: 
> Returns a synchronized (thread-safe) list backed by the specified list. In order to guarantee serial access, it is critical that all access to the backing list is accomplished through the returned list.*
* 不能依赖fail-fast进行程序同步控制，应该对ArrayList进行包装（`Collections.synchronizedList`），合法的ArrayList同步操作demo：
```java
 List list = Collections.synchronizedList(new ArrayList());
      ...
  synchronized (list) {
      Iterator i = list.iterator(); // Must be in synchronized block
      while (i.hasNext())
          foo(i.next());
  }
```

### Vector
> The Vector class implements a growable array of objects. Like an array, it contains components that can be accessed using an integer index.

> The iterators returned by this class's iterator and listIterator methods are fail-fast:...

> ..., If a thread-safe implementation is not needed, it is recommended to use ArrayList in place of Vector.

* Vector和ArrayList的性能相似，最重要的<span style="color:#ab4642">区别:</span>是:
    * `Vector`是<span style="color:#ab4642">线程安全</span>的。
    * 扩容机制不一样，具体可以看后面的**扩容代码解读：**

### LinkedList
> Doubly-linked list implementation of the List and Deque interfaces. Implements all optional list operations, and permits all elements (including null).
All of the operations perform as could be expected for a doubly-linked list. Operations that index into the list will traverse the list from the beginning or the end, whichever is closer to the specified index.

#### 性能：
* 具有链表的特点，对增加和删除节点的操作效率高（特别是表头或者表尾的操作）。但是，随机访问效率低。
* 
#### 同步：
> The iterators returned by this class's iterator and listIterator methods are fail-fast
>
* LinkedList是线程不安全的，需要包装：
```java
List list = Collections.synchronizedList(new LinkedList(...));
```

### ArrayList vs Vector vs LinkedList

|  | ArrayList | Vector  | LinkedList  |
| --- | --- | --- | --- |
| list接口 | Y |Y  | Y |
| deque接口 |N |N  | Y |
|elements 允许null|Y|Y|Y|
| growable | Y |Y  | N |
| get |O(1)  |O(1)  | O(n) |
| set |O(1) |O(1) | O(n)|
| remove| O(n)| O(n)| O(1)|
| add| O(n)| O(n)| O(1)|
| synchronized| N| Y|N|
| synchronized| N| Y|N|
|fail-fast|Y|Y|Y|


## 几个值得深入思考的问题

### fail-fast 
 fail-fast 机制是java集合(Collection)中的一种错误机制。`ArrayList`,`Vector`, `LinkedList` 都满足fail-fast机制。官方文档中对fail-fast的解释如下：
> fail-fast: if the list is structurally modified at any time after the iterator is created, in any way except through the iterator's own remove or add methods, the iterator will throw a `ConcurrentModificationException`. Thus, in the face of concurrent modification, the iterator fails quickly and cleanly, rather than risking arbitrary, non-deterministic behavior at an undetermined time in the future.


* fail-fast产生的原因就在于程序在对collection（如: ArrayList）进行迭代时，某个线程对该 collection **在结构上对其做了修改**，这时迭代器就会抛出 `ConcurrentModificationException` 异常信息，从而产生 **fail-fast**
> 要了解fail-fast机制，我们首先要对ConcurrentModificationException 异常有所了解。当方法检测到对象的并发修改，但不允许这种修改时就抛出该异常。同时需要注意的是，该异常不会始终指出对象已经由不同线程并发修改，如果单线程违反了规则，同样也有可能会抛出改异常。

#### ConcurrentModificationException

* 容器使用迭代器来进行统一个容器访问操作。迭代器本质上只是容器的一个视图，迭代器里存放容器访问的游标，以及expectedModCount
* `expectedModCount`是迭代器fail-fast机制的关键，
* 迭代器在操作容器元素前，会`checkForComodification`，其实就是检查`expectedModCount==modCount`
* 迭代器在对容器作结构性后，会刷新`expectedModCount = modCount`。
* 换言之，如果在迭代器同步到最新的modCount后，有其他操作修改了容器的modCount，那么`checkForComodification`就会校验失败，于是就抛出`ConcurrentModificationException`

``` java
 private class Itr implements Iterator<E> {
        /**
         * Index of element to be returned by subsequent call to next.
         */
        int cursor = 0;

        /**
         * Index of element returned by most recent call to next or
         * previous.  Reset to -1 if this element is deleted by a call
         * to remove.
         */
        int lastRet = -1;

        /**
         * The modCount value that the iterator believes that the backing
         * List should have.  If this expectation is violated, the iterator
         * has detected concurrent modification.
         */
        int expectedModCount = modCount;

        public boolean hasNext() {
            return cursor != size();
        }

        public E next() {
            checkForComodification();
            try {
                int i = cursor;
                E next = get(i);
                lastRet = i;
                cursor = i + 1;
                return next;
            } catch (IndexOutOfBoundsException e) {
                checkForComodification();
                throw new NoSuchElementException();
            }
        }

        public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                AbstractList.this.remove(lastRet);
                if (lastRet < cursor)
                    cursor--;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException e) {
                throw new ConcurrentModificationException();
            }
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
    
        private class ListItr extends Itr implements ListIterator<E> {
        ListItr(int index) {
            cursor = index;
        }

        public boolean hasPrevious() {
            return cursor != 0;
        }

        public E previous() {
            checkForComodification();
            try {
                int i = cursor - 1;
                E previous = get(i);
                lastRet = cursor = i;
                return previous;
            } catch (IndexOutOfBoundsException e) {
                checkForComodification();
                throw new NoSuchElementException();
            }
        }

        public int nextIndex() {
            return cursor;
        }

        public int previousIndex() {
            return cursor-1;
        }

        public void set(E e) {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                AbstractList.this.set(lastRet, e);
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }

        public void add(E e) {
            checkForComodification();

            try {
                int i = cursor;
                AbstractList.this.add(i, e);
                lastRet = -1;
                cursor = i + 1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
    }

```


值得一提的时候，当不适用迭代器访问和操作容器的时候，不会抛出`ConcurrentModificationException`，但是并不意味着程序正确。即使，是迭代器访问的程序，也有恰好未发生`ConcurrentModificationException`的情况。

> Note that the fail-fast behavior of an iterator cannot be guaranteed as it is, generally speaking, impossible to make any hard guarantees in the presence of unsynchronized concurrent modification. Fail-fast iterators throw ConcurrentModificationException on a best-effort basis. Therefore, it would be wrong to write a program that depended on this exception for its correctness: the fail-fast behavior of iterators should be used only to detect bugs.
> 注意，迭代器的快速失败行为无法得到保证，因为一般来说，不可能对是否出现不同步并发修改做出任何硬性保证。快速失败迭代器会尽最大努力抛出 ConcurrentModificationException。因此，为提高这类迭代器的正确性而编写一个依赖于此异常的程序是错误的做法：迭代器的快速失败行为应该仅用于检测 bug。

* 不能依赖fail-fast机制来保证程序的正确性。`ConcurrentModificationException`只适合用来查bug。

##### fast-fail的测试代码

* `failFastWorkTest`的Thread t1使用迭代器来访问容器，在访问过程中，Thread t2对容器调用了`ArrayList.remove(element)`操作，此后，当t1迭代到下一个元素时候，（`next()`会检查modCount, 即`checkForComodification`）这将引发fast-fail.
* 
根据上面描述，我们可以写个demo来测试ArrayList的fast-fail：
```java
    @Test
    public void failFastWorkTest() throws InterruptedException, ConcurrentModificationException     {
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                Iterator<Integer> iterator = arrayList.iterator();
                while(iterator.hasNext()) {
                    int i = iterator.next();
                    logger.info("thread t1 get item : {}", i);
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                }
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 10; i++) {
                    if (i == 3) {
//                        arrayList.remove(i);
                        arrayList.add(i);
                    }

                }
            }
        });

        runThreads(t1, t2);
    }
    private void runThreads(Thread t1, Thread t2) throws InterruptedException {
        logger.info("start thread 1 ");
        t1.start();
        logger.info("start thread 2 ");
        t2.start();
        t1.join();
        logger.info("end thread1 ");
        t2.join();
        logger.info("end thread2 ");
        logger.info("fail fast test done");

        logger.info("finnal array List", arrayList);
    }
```

* `failFastNoWorkTest`的Thread t1使用直接通过ArrayList.get()来访问容器，在访问过程中，Thread t2对容器调用了`ArrayList.remove(element)`操作。未使用迭代器，所以没有modCount校验，所以不会发生fast-fail。当实际上，这样的程序是线程不安全的。


```java
 
    @Test
    public void failFastNoWorkTest() throws InterruptedException {
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                int size = arrayList.size();
                for (int i = 0; i < size; i++) {
                    int value = arrayList.get(i);
                    logger.info("thread 1 run : {}", value);
                    printPrivateField(AbstractList.class, "modCount", arrayList);

                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < RUN_TIMES; i++) {

                    if (i == 3) {
                        arrayList.remove(3);
                    }
                    printPrivateField(AbstractList.class, "modCount", arrayList);
                    addElementByList(arrayList, 100);
                }
            }
        });

        logger.info("before start thread ... ");
        printPrivateField(AbstractList.class, "modCount", arrayList);
        new ListTest().runThreads(t1, t2);

    }
```

### capacity
另一个值得研究的知识点是List的自动扩容，主要是ArrayList和Vector。
> ArrayList和Vector都是使用数组(Array)来控制集合中的对象。当你向这两种类型中增加元素的时候，如果元素的数目超出了内部数组目前的长度它们都需要扩展内部数组的长度，Vector缺省情况下自动增长原来一倍的数组长度，ArrayList是原来的50%,所以最后你获得的这个集合所占的空间总是比你实际需要的要大。所以如果你要在集合中保存大量的数据那么使用Vector有一些优势，因为你可以通过设置集合的初始化大小来避免不必要的资源开销。


Vector
```java

   /**
     * This implements the unsynchronized semantics of ensureCapacity.
     * Synchronized methods in this class can internally call this
     * method for ensuring capacity without incurring the cost of an
     * extra synchronization.
     *
     * @see #ensureCapacity(int)
     */
    private void ensureCapacityHelper(int minCapacity) {
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
   
ArrayList:
```java

    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

```

所以，如果需要存储的数据量比较大，可以使用`Vector`，以减少扩容次数。另外，`Vector`可以设置`capacityIncrement`，来配置每次扩容的增长量，比较灵活。




## 参考链接
[ArrayList](https://docs.oracle.com/javase/8/docs/api/?java/util/Collection.html)
[simpleJava](https://www.programcreek.com/simple-java/)
[fail-fast](https://blog.csdn.net/chenssy/article/details/38151189)
[SynchronizedList和Vector的区别, by Hollis](http://www.hollischuang.com/archives/498)