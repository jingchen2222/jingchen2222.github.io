---
title: serialization
date: 2018-09-18 18:45:44
tags:
- tech
- Java
- 基础
- Serializable
categories:
- Study
- Java
---

### 深入理解序列化
#### 背景

> 序列化是一种对象持久化的手段，普遍应用于网络传输对象，RMI等场景

本文以Hollis博客中[深入分析Java的序列化与反序列化](http://www.hollischuang.com/archives/1140)一文为蓝本，学习序列化需要关注的几个重要问题

> 怎么实现Java的序列化
> 为什么实现了`java.io.Serializable`接口，才能序列化
> `transient`的作用是什么
> 怎么自定义序列化策略
> 自定义的序列化策略是如何被调用的
> ArrayList对序列化的实现有什么好处？

#### 如何实现序列化

> 在Java中，只要一个类实现了java.io.Serializable接口，那么它就可以被序列化

- 如果去掉Serializable接口,序列化操作时候会抛出`java.io.NotSerializableException`

> Serializable明明就是一个空的接口，它是怎么保证只有实现了该接口的方法才能进行序列化与反序列化的呢？

`writeObject` ---> `writeObject0` ---> `writeOrdinaryObject` ---> `writeSerialData` ---> `invokeWriteObject`

`writeObject0`方法中有这么一段代码：

<!--more-->
```java
if (obj instanceof String) {
                writeString((String) obj, unshared);
            } else if (cl.isArray()) {
                writeArray(obj, desc, unshared);
            } else if (obj instanceof Enum) {
                writeEnum((Enum<?>) obj, desc, unshared);
            } else if (obj instanceof Serializable) {
                writeOrdinaryObject(obj, desc, unshared);
            } else {
                if (extendedDebugInfo) {
                    throw new NotSerializableException(
                        cl.getName() + "\n" + debugInfoStack.toString());
                } else {
                    throw new NotSerializableException(cl.getName());
                }
            }
```

所以当Object继承了`Serializable`的时候，就会调用`writeOrdinaryObject`(就是上文的那个调用路径）


### 单例模式与序列化
##### 阅读材料：

[单例与序列化的那些事儿](http://www.hollischuang.com/archives/1144)

> 单例模式，是设计模式中最简单的一种。通过单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。关于单例模式的使用方式，可以阅读[单例模式的七种写法](http://www.hollischuang.com/archives/205)





```java、
Object obj;
try {
    obj = desc.isInstantiable() ? desc.newInstance() : null;
} catch (Exception ex) {
    throw (IOException) new InvalidClassException(
        desc.forClass().getName(),
        "unable to create instance").initCause(ex);
}
```

>isInstantiable：如果一个serializable/externalizable的类可以在运行时被实例化，那么该方法就返回true。针对serializable和externalizable我会在其他文章中介绍。
desc.newInstance：该方法通过反射的方式调用无参构造方法新建一个对象。

由此可见，
> 序列化会通过反射调用无参数的构造方法创建一个新的对象。

#### 防止序列化破坏单例
> 先给出解决方案，然后再具体分析原理：
> 只要在Singleton类中定义readResolve就可以解决该问题：

```java
  @Test
    public void singletonTestOk() throws IOException, ClassNotFoundException {
        //Write Obj to file
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("tempFile"));
        oos.writeObject(SingletonOk.getSingleton());
        //Read Obj from file
        File file = new File("tempFile");
        ObjectInputStream ois =  new ObjectInputStream(new FileInputStream(file));
        SingletonOk newInstance = (SingletonOk) ois.readObject();
        //判断是否是同一个对象
        Assert.assertTrue(newInstance == SingletonOk.getSingleton());
        logger.info("the same is : {} ", newInstance == SingletonOk.getSingleton());
    }
```

具体实现原理，需要继续看源码：
```java
/**
 * Reads and returns "ordinary" (i.e., not a String, Class,
 * ObjectStreamClass, array, or enum constant) object, or null if object's
 * class is unresolvable (in which case a ClassNotFoundException will be
 * associated with object's handle).  Sets passHandle to object's assigned
 * handle.
 */
private Object readOrdinaryObject(boolean unshared)
    throws IOException
{
    ...

    Object obj;
    try {
        obj = desc.isInstantiable() ? desc.newInstance() : null;
    } catch (Exception ex) {
        throw (IOException) new InvalidClassException(
            desc.forClass().getName(),
            "unable to create instance").initCause(ex);
    }
    ... 

    if (obj != null &&
        handles.lookupException(passHandle) == null &&
        desc.hasReadResolveMethod())
    {
        Object rep = desc.invokeReadResolve(obj);
        if (unshared && rep.getClass().isArray()) {
            rep = cloneArray(rep);
        }
        if (rep != obj) {
            handles.setObject(passHandle, obj = rep);
        }
    }

    return obj;
}
```

> `hasReadResolveMethod`:如果实现了`serializable` 或者 `externalizable`接口的类中包含readResolve则返回true
> `invokeReadResolve` :通过反射的方式调用要被反序列化的类的`readResolve`方法。

**所以，原理也就清楚了，主要在Singleton中定义readResolve方法，并在该方法中指定要返回的对象的生成策略，就可以防止单例被破坏。**


##### 总结
在涉及到序列化的场景时，要格外注意他对单例的破坏。

### Google Protocol Buffer 的使用和原理

本章节通过阅读[Google Protocol Buffer 的使用和原理](https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/)学习Google protocal buffer

##### 什么事Google Protocal Buffer
> Google Protocol Buffer( 简称 Protobuf) 是 Google 公司内部的混合语言数据标准，目前已经正在使用的有超过 48,162 种报文格式定义和超过 12,183 个 .proto 文件。他们用于 RPC 系统和持续数据存储系统。

> Protocol Buffers 是一种轻便高效的结构化数据存储格式，可以用于结构化数据串行化，或者说序列化。它很适合做数据存储或 RPC 数据交换格式。可用于通讯协议、数据存储等领域的语言无关、平台无关、可扩展的序列化结构数据格式。目前提供了 C++、Java、Python 三种语言的 API。


#### 任务：动手安装protobuf且成功生成java类

清单 1. proto 文件
```proto
package dovet22; 
message helloworld 
{ 
   required int32     id = 1;  // ID 
   required string    str = 2;  // str 
   optional int32     opt = 3;  //optional field 
}
```
**关键：**
> 一个比较好的习惯是认真对待 proto 文件的文件名。比如将命名规则定于如下：`packageName.MessageName.proto`

##### 编译 .proto 文件
写好 proto 文件之后就可以用 Protobuf 编译器将该文件编译成目标语言了。本例中我们将使用 Java。
``` shell 
> protoc -I=$SRC_DIR --cpp_out=$DST_DIR $SRC_DIR/addressbook.proto
```

命令将生成文件：`Dovet22Helloworld.java`

```java
package dovet22;

public final class Dovet22Helloworld {
  private Dovet22Helloworld() {}
  public static void registerAllExtensions(
      com.google.protobuf.ExtensionRegistry registry) {
  }
  public interface helloworldOrBuilder extends
      // @@protoc_insertion_point(interface_extends:dovet22.helloworld)
      com.google.protobuf.MessageOrBuilder {

    /**
     * <code>required int32 id = 1;</code>
     *
     * <pre>
     * ID 
     * </pre>
     */
    boolean hasId();
    /**
     * <code>required int32 id = 1;</code>
     *
     * <pre>
     * ID 
     * </pre>
     */
    int getId();

    /**
     * <code>required string str = 2;</code>
     *
     * <pre>
     * str 
     * </pre>
     */
    boolean hasStr();
    /**
     * <code>required string str = 2;</code>
     *
     * <pre>
     * str 
     * </pre>
     */
    java.lang.String getStr();
    ...
  
}
```


##### Protobuf 的优点
Protobuf 有如 XML，不过它更小、更快、也更简单。你可以定义自己的数据结构，然后使用代码生成器生成的代码来读写这个数据结构。你甚至可以在无需重新部署程序的情况下更新数据结构。只需使用 Protobuf 对数据结构进行一次描述，即可利用各种不同语言或从各种不同数据流中对你的结构化数据轻松读写。

>它有一个非常棒的特性，即“向后”兼容性好，人们不必破坏已部署的、依靠“老”数据格式的程序就可以对数据结构进行升级。这样您的程序就可以不必担心因为消息结构的改变而造成的大规模的代码重构或者迁移的问题。因为添加新的消息中的 field 并不会引起已经发布的程序的任何改变。

>Protobuf 语义更清晰，无需类似 XML 解析器的东西（因为 Protobuf 编译器会将 .proto 文件编译生成对应的数据访问类以对 Protobuf 数据进行序列化、反序列化操作）。

>使用 Protobuf 无需学习复杂的文档对象模型，Protobuf 的编程模式比较友好，简单易学，同时它拥有良好的文档和示例，对于喜欢简单事物的人们而言，Protobuf 比其他的技术更加有吸引力。

##### Protobuf 的不足
> Protbuf 与 XML 相比也有不足之处。它功能简单，无法用来表示复杂的概念。
> XML 已经成为多种行业标准的编写工具，Protobuf 只是 Google 公司内部使用的工具，在通用性上还差很多。
> 由于文本并不适合用来描述数据结构，所以 Protobuf 也不适合用来对基于文本的标记文档（如 HTML）建模。另外，由于 XML 具有某种程度上的自解释性，它可以被人直接读取编辑，在这一点上 Protobuf 不行，它以二进制的方式存储，除非你有 .proto 定义，否则你没法直接读出 Protobuf 的任何内容【 2 】。


## 高级话题：序列化你可能不知道的五件事

### 序列化支持重构
##### 阅读材料
[Java 对象序列化：你不知道的5件事](https://www.ibm.com/developerworks/cn/java/j-5things1/)
##### 背景

> 大约一年前，一个负责管理应用程序所有用户设置的开发人员，决定将用户设置存储在一个 Hashtable 中，然后将这个 Hashtable 序列化到磁盘，以便持久化。当用户更改设置时，便重新将 Hashtable 写到磁盘。
> 这是一个优雅的、开放式的设置系统，但是，当团队决定从 Hashtable 迁移到 Java Collections 库中的 HashMap 时，这个系统便面临崩溃。
> Hashtable 和 HashMap 在磁盘上的格式是不相同、不兼容的。除非对每个持久化的用户设置运行某种类型的数据转换实用程序（极其庞大的任务），否则以后似乎只能一直用 Hashtable 作为应用程序的存储格式。

>团队感到陷入僵局，但这只是因为他们不知道关于 Java 序列化的一个重要事实：Java 序列化允许随着时间的推移而改变类型。当我向他们展示如何自动进行序列化替换后，他们终于按计划完成了向 HashMap 的转变。

#### 1. 序列化允许重构


#### 参考文献
[《成神之路-基础篇》Java基础知识——序列化（已完结）](http://www.hollischuang.com/archives/1158)
[来自 Hollis 的博客:深入分析Java的序列化与反序列化](http://www.hollischuang.com/archives/1140)

[来自 Hollis 的博客:](https://www.zhihu.com/question/278968863)

[单例与序列化的那些事儿](http://www.hollischuang.com/archives/1144)
[Google Protocol Buffer 的使用和原理](https://www.ibm.com/developerworks/cn/linux/l-cn-gpb/)
