---
title: Java基础复习二
date: 2020-09-23 21:22:00
tag: Java SE
categories: Java
toc: true
thumbnail: /thumbnails/Java/java.jpg
---

Java基础复习二

<!--more-->

---

> **Java基础复习**
>
> * [Java基础复习一](https://crazy-sky.github.io/2020/09/21/Java基础复习一/)
> * [Java基础复习二](https://crazy-sky.github.io/2020/09/23/Java基础复习二/)

# 12、**面向对象设计的基本原则**

> * **OCP（开闭原则，Open-Closed Principle）**：一个软件的实体应当对扩展开放，对修改关闭。
> * **LSP（里氏替换原则，Liskov Substitution Principle）**：继承必须确保超类所拥有的性质在子类中仍然成立。
> * **DIP（依赖倒转原则，Dependence Inversion Principle）**：要针对接口编程，不要针对实现编程。
> * **SRP（单一职责原则，Single Responsibility Principle）**：一个类应该有且仅有一个引起它变化的原因，否则类应该被拆分。
> * **ISP（接口隔离原则，Interface Segregation Principle）**：一个类对另一个类的依赖应该建立在最小的接口上。
> * **CRP（合成复用原则，Composite Reuse Principle）**：在软件复用时，要尽量先使用组合或者聚合等关联关系来实现，其次才考虑使用继承关系来实现。
> * **LoD（迪米特法则，Law of Demeter）**：只与你直接的朋友通信，而避免和陌生人通信。

# 13、对象与导入（import）

**对象创建的四步**

1. 分配对象空间，并将对象成员变量初始化为0或空。
2. 执行属性值的显示初始化。
3. 执行构造方法。
4. 返回对象的地址给相关的变量。

**导入概述**

如果我们要使用其他包的类，需要使用import导入，从而可以在本类中直接通过类名来调用，否则就需要书写类的完整包名和类名。import后，便于编写代码，提高可维护性。Java会默认导入java.lang包下所有的类，因此这些类我们可以直接使用。

**导入同名类**

当通过“import java.sql.Date;“导入了Date类，如果在同源文件中有自己写的Date类，使用时优先使用自己写的Date类，如果是在同包但不同源文件中自己写的Date类，则优先使用通过“import java.sql.Date;“导入的Date类。

当通过“import java.sql.Date;“导入了Date类，又通过“import java.util.*;”导入了util下的所有类，其中util中也包含Date类，此时使用时优先使用通过“import java.sql.Date;“导入的Date类，即优先使用精确导入的类。

当需要使用非优先使用的同名类时，需要通过“java.util.Date  now2 = new java.util.Date();”这样的方式进行初始化。

```java
import java.sql.Date; // 精确导入
import java.util.Scanner; // 导入该包下所有的类。会降低编译速度，但不会降低运行速度。

public class Test{
    
    public static void main(String[] args) {
        
        // 这里指的是java.sql.Date
        Date now; 
        
        // java.util.Date因为和java.sql.Date类同名，由于java.util.Date是非优先使用的类，需要完整路径
        java.util.Date  now2 = new java.util.Date();
        System.out.println(now2);      
        
        // java.util包的非同名类不需要完整路径
        Scanner input = new Scanner(System.in);    
    }
}
```

**静态导入**

静态导入(static import)是在JDK1.5新增加的功能，其作用是用于导入指定类的静态属性，这样我们可以直接使用静态属性。

```java
import static java.lang.Math.*; // 导入Math类的所有静态属性
import static java.lang.Math.PI; // 导入Math类的PI属性
 
public class Test2{
    
    public static void main(String [] args){
        
        System.out.println(PI);
        System.out.println(random());
    }
}
```

# 14、继承

**instanceof 运算符**

instanceof是二元运算符，左边是对象，右边是类；当对象是右面类或子类所创建对象时，返回true；否则，返回false。

```java
public class Test{
    
    public static void main(String[] args) {
        
        System.out.println(new Object() instanceof Object); // 结果为true
        
        System.out.println(new Test() instanceof Object); // 结果为true
    }
}
```

**继承要点**

1. 父类也称作超类、基类、派生类等。

2. Java中只有单继承，没有像C++那样的多继承。多继承会引起混乱，使得继承链过于复杂，系统难于维护。

3. Java中类没有多继承，接口有多继承。

4. 子类继承父类，可以得到父类的全部属性和方法 (除了父类的构造方法)，但不见得可以直接访问(比如，父类私有的属性和方法)。

5. 如果定义一个类时，没有调用extends，则它的父类是：java.lang.Object。

**方法重写要点**

1. “==”：方法名、形参列表相同。

2. “≤”：返回值类型和声明异常类型，子类小于等于父类。

3. “≥”： 访问权限，子类大于等于父类。

```java
public class Student extends Person{
    
    public Student(String name) {
        this.name = name;
    }
    
    public Student() {}
    
    // 重写父类study方法，方法名、形参列表相同
    @Override
    public void study() {
        System.out.println("学习编程。。。");
    }
    
    // 重写父类getSelf方法，方法名、形参列表相同，返回类型为父类返回类型的子类
    @Override
    public Student getSelf() { 
        return new Student("李四");
    }
    
    public static void main(String[] args) {
        
        Student student = new Student();
        student.study(); // 结果为：学习编程。。。
        System.out.println(student.getSelf().name);// 结果为：李四
    }

}

class Person {
    
    String name;
    
    public Person(String name) {
        this.name = name;
    }
    
    public Person() {}
    
    public void study() {
        System.out.println("学习。。。");
    }
    
    public Person getSelf() {
        return new Person("张三");
    }
}
```

# 15、封装

需要让用户知道的才暴露出来，不需要让用户知道的全部隐藏起来，这就是封装。说的专业一点，封装就是把对象的属性和操作结合为一个独立的整体，并尽可能隐藏对象的内部实现细节。

 我们程序设计要追求“高内聚，低耦合”。 高内聚就是类的内部数据操作细节自己完成，不允许外部干涉;低耦合是仅暴露少量的方法给外部使用，尽量方便外部调用。

**编程中封装的具体优点：**

1. 提高代码的安全性。

2. 提高代码的复用性。

3. “高内聚”：封装细节，便于修改内部代码，提高可维护性。

4. “低耦合”：简化外部调用，便于调用者使用，便于扩展和协作。

**封装的实现—使用访问控制符**

Java是使用“访问控制符”来控制哪些细节需要封装，哪些细节需要暴露的。 Java中4种“访问控制符”分别为private、default、protected、public，它们说明了面向对象的封装性，所以我们要利用它们尽可能的让访问权限降到最低，从而提高安全性。

**访问权限范围**

| 修饰符    | 同一个类 | 同一个包 | 子类  | 所有类 |
| --------- | -------- | -------- | ----- | ------ |
| private   | true     | false    | false | false  |
| default   | true     | true     | false | false  |
| protected | true     | true     | true  | false  |
| public    | true     | true     | true  | true   |

1. private表示私有，只有自己类能访问

2. default表示没有修饰符修饰，只有同一个包的类能访问

3. protected表示可以被同一个包的类以及其他包中的子类访问

4. public表示可以被该项目的所有包中的所有类访问

---

**学习所得，资料、图片部分来源于网络，如有侵权，请联系本人删除。**

**才疏学浅，若有错误或不当之处，可批评指正，还请见谅！**

