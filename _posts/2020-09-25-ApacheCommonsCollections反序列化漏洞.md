---
title: ApacheCommonsCollections反序列化漏洞
tags: 安全
---

[toc]

# 简述

Apache Commons  Collections反序列化漏洞必然是2015年影响重大的漏洞之一，同时也开启了各类java反序列漏洞的大门，这几年大量各类java反序列化漏洞不断出现。java反序列化漏洞基本一出必高危，风险程度极大，最近研究了一些反序列化漏洞，本篇记录apache commons collections反序列化漏洞。

# 序列化与反序列化

  java程序在运行时，会产生大量的数据。有些时候，我们需要将内存中的对象信息存储到磁盘或者通过网络发送给第三者，此时，就需要对对象进行序列化操作。当我们需要从磁盘或网络读取存储的信息时，即为反序列化。简单理解，序列化即将内存中的对象信息转换为字节流并存储在磁盘或通过网络发送。反序列化，即从磁盘或网络读取信息，直接转换为内存对象。

  如果一个对象需要进行序列化，需要注意一下两点：

​    1.必须实现Serializable接口

​    2.序列化的是实例对象，故static修饰的属性不会序列化。transient修饰的属性也不会被序列化

## 例子

- Person类

```java
import java.io.*;

public class Person implements Serializable {
    private  static final long serialVersionUID = 2484848939485859L;
    private String name;
    private String sex;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", sex='" + sex + '\'' +
                ", age=" + age +
                '}';
    }
}
```

- test1

```java
import java.io.*;

public class test1 {
    public static void main(String[] args) throws IOException {
        Person person = new Person();
        person.setName("abc");
        person.setAge(12);
        person.setSex("男");
        ObjectOutputStream oo = new ObjectOutputStream(new FileOutputStream(new File("/home/admin233/IdeaProjects/test/123.txt")));
        oo.writeObject(person);
        System.out.println("Person对象序列化成功");
        oo.close();
    }
}
```

用16进制编辑器查看123.txt文件,可以看到序列化，即是将对象通过ObejctOutPutStream类的writeObject()方法写入文件即可

- test2

```java
import java.io.*;

public class test2 {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(new File("/home/admin233/IdeaProjects/test/123.txt")));
        Person person  = (Person) ois.readObject();
        System.out.println("Person对象反序列化成功");
        System.out.println(person.toString());
        ois.close();
    }
}
```

反序列化即通过ObjectInputStream类中的readObject()方法读取文件流，即可直接在内存中还原序列化的对象，包括其中的属性值

# readObject方法

反序列化过程中关键的就是readObject方法，通过readObject将文件流转换为内存对象。因此，反序列化漏洞的关键就是在readObject()方法。在序列化后的文件中，可以看到是哪个对象被序列化到文件中的。在反序列化过程中如果该对象的类中重写的readObject()方法，在反序列化中会调用该类中的readObject()方法

- person2

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.Serializable;

public class Person2 implements Serializable {
    private static final long serialVersionUID = 248484898547362356L;
    private String name;
    private String sex;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    private void readObject(ObjectInputStream in) throws ClassNotFoundException, IOException {
        Runtime.getRuntime().exec("gnome-calculator");
    }

    @Override
    public String toString() {
        return "Person2{" +
                "name='" + name + '\'' +
                ", sex='" + sex + '\'' +
                ", age=" + age +
                '}';
    }
}
```

- test3

```java
import java.io.*;

public class test3 {
    public static void main(String[] args) throws IOException {
        Person2 person2 = new Person2();
        person2.setName("abc");
        person2.setAge(12);
        person2.setSex("男");
        ObjectOutputStream oo = new ObjectOutputStream(new FileOutputStream(new File("/home/admin233/IdeaProjects/test/123.txt")));
        oo.writeObject(person2);
        System.out.println("Person对象序列化成功");
        oo.close();
    }
}
```

- test4

```java
import java.io.*;

public class test4 {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(new File("/home/admin233/IdeaProjects/test/123.txt")));
        Person2 person2 = (Person2) ois.readObject();
        System.out.println("Person对象反序列化成功");
        System.out.println(person2.toString());
        ois.close();
    }
}
```

![反序列化](/img/反序列化.png)

通过以上分析，如果我们能够控制重写反序列化类的readObject方法，就可以制造反序列化漏洞，从而达到攻击效果。`然而，我们自己写个类，随后进行序列化，再发送给远程服务器，服务器反序列化的时候也是不成功的，因为服务器端根本没有我们自己写的类`。只能考虑，如果服务器已经存在的某个库中的某个类，类本身就重写了readObject方法，是否能通过构造该类的序列化对象，以达到在反序列化时，触发特定操作，实现攻击,满足重写readObject方法的类有非常多AnnotationInvocationHandler、BadAttributeValueExpException等类均满足条件

# 反射链

我们的目标是通过反序列化运行`Runtime.getRuntime().exec(new String[]{"gnome-calculator"})`，没有任何类可以直接提供运行条件