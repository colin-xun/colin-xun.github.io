---
title: java值传递和引用传递
date: 2018-01-23 14:09:58
tags: [java,面试,基础]
category: java基础
---
这是一道面试题
之前被问到过，但是过后也没好好想想，今天有朋友问到这个，查过资料以后总结下
先看一段代码：
<!-- more -->
```java
public class Test{
    public static void main(String[] args) {
        String str = "A";  
        change(str);
        System.out.println(str);
        
        Integer i = new Integer(10);
        changeb(i);
        System.out.println(i);
        
        int j = 10;
        changed(j);
        System.out.println(j);
        
        Stu stu = new Stu("colin");
        changec(stu);
        System.out.println(stu);

    }

    private static void changed(Integer j) {
        j = 20;
    }
    
    private static void changec(Stu stu) {
        stu.name = "libra";
//        stu = new Stu("libra");
    }
    
    private static void changeb(Integer i) {
        i = new Integer(20);
    }
    
    private static void change(String str) {
        str = "B";
    }
}
class Stu {
    String name;
    
    public Stu(String name) {
        this.name = name;
    }
    
    @Override
    public String toString() {
        return "Stu{" +
                "name='" + name + '\'' +
                '}';
    }
}
```
<h5>结果</h5>
```java
A
10
10
Stu{name='wtx'}
```
<h5>个人理解：</h5>
java中传递方式分为值传递和引用传递
值传递：基本类型(byte--short--int--long--float--double--boolean--char)
引用传递：类类型，接口类型，数组
<h6>例子一：String</h6>
{% asset_img 1.png java内存图 %}
String是在常量池中的，这些无所谓，有所谓的是引用传递传递的是地址，A的地址是0x1101现在把0x1101传递过去，让实参str接受，这时候str也指向了0x1101，这时候修改实参str的指向，只是修改了实参的指向，没有修改外面str的指向，所有输出为A
<h6>例子二：Integer，Double包装类</h6>
这些包装类里面没有修改值的操作，而且是final类型的，所以我们修改值的时候不是在原来的基础上修改，所以输出10
<h6>例子三：基本类型</h6>
基本类型就是值传递，不涉及引用，不修改外面值
<h6>例子四：修改对象值</h6>
对于例子四，如果使用第一行代码的话，我们是在原对象中修改，故会改变外面的值，但是如果我们是用第二种，那只是改变了内部引用，不影响外面值的变化


