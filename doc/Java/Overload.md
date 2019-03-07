# 重载（overload）和重写（override）
## 什么是重载？
在同一个类中，如果多个方法由相同的名字、不同的参数，即称为重载。一个类中有多个构造方法也是重载。

## JVM 如何执行？
在编译器的眼里方法名称+参数类型+参数个数，组成一个唯一键，称为方法签名，JVM 通过这个唯一键决定调用哪种重载的方法。

假如有以下代码，当我们执行 method(8) 时，该如何执行呢？
```Java
public class OverloadMethods {
    public void overlaodMethod() {}

    public void method(int param) {}

    public void method(Integer param) {}

    public void method(Integer... param) {}

    public void method(Object param) {}
}
```
## 解释
JVM 在重载方法中，选择合适的目标方法的顺序如下：
	1. 精确匹配
	2. 如果是基本数据类型，自动转换成更大的表示范围的基本类型
	3. 通过自动拆箱与装箱
	4. 通过子类向上转型继承路线一次匹配
	5. 通过可变参数匹配

由此可知，method(8) 会执行 method(int param) 方法，因为规则第一条即是精确匹配。如果是 method(new Integer(8)) 的话，method(Integer param) 则会被执行。

## 什么是重写？
