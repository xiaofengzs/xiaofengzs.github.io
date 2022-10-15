---
title: Java中的泛型
catalog: true
date: 2022-10-11 19:27:12
subtitle:
header-img:
tags: 泛型 Java泛型 翻译
categories: Java
---

> 这篇文章是在学习泛型的过程中发现的一篇不错的文章，翻译一下，并增加一点内容

Java泛型是Java5中引入的最重要的的特性之一。如果你使用过Java5或者更高版本的`Collections`，我相信您已经使用过它。带有集合类的 Java 泛型非常简单，但它提供了更多的功能，而不仅仅是创建集合的类型。 我们将在本文中尝试学习泛型的特性。 如果我们使用行话，理解泛型有时会变得混乱，所以我会尽量保持简单易懂。

## 1.Generics in Java

Java 5 中添加了泛型， 可以在使用集合类时，在编译时期做类型检查并消除常见的 ClassCastException 风险。 整个集合框架使用泛型重写来保证类型安全。 让我们看看泛型如何帮助我们安全地使用集合类。

```java
List list = new ArrayList();
list.add("abc");
list.add(new Integer(5)); //OK

for(Object obj : list){
	//type casting leading to ClassCastException at runtime
    String str=(String) obj; 
}
```

上面的代码编译时没问题，但在运行时抛出 ClassCastException，因为我们将列表中的 Object 转换为 String，而其中一个元素是 Integer 类型。 在 Java 5 之后，我们使用如下的集合类。

```java
List<String> list1 = new ArrayList<String>(); // java 7 ? List<String> list1 = new ArrayList<>(); 
list1.add("abc");
//list1.add(new Integer(5)); //compiler error

for(String str : list1){
     //no type casting needed, avoids ClassCastException
}
```

请注意，在创建列表时，我们已指定列表中元素的类型为字符串。 因此，如果我们尝试在列表中添加任何其他类型的对象，程序将抛出编译时错误。 另请注意，在 for 循环中，我们不需要对列表中的元素进行类型转换，因此在运行时删除了 ClassCastException。

## 2.泛型的特性

泛型只在编译阶段有效。看下面的代码：

```java
List<String> stringArrayList = new ArrayList<String>();
List<Integer> integerArrayList = new ArrayList<Integer>();

Class classStringArrayList = stringArrayList.getClass();
Class classIntegerArrayList = integerArrayList.getClass();

if(classStringArrayList.equals(classIntegerArrayList)){
     System.out.println("泛型测试, 类型相同");
}
```

输出结果：`泛型测试，类型相同`。

通过上面的例子可以证明，在编译之后程序会采取去泛型化的措施。也就是说Java中的泛型，只在编译阶段有效。在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦出，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。也就是说，泛型信息不会进入到运行时阶段。

对此总结成一句话：泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型。

## 3.泛型的使用

泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法

### 泛型类

我们可以用泛型定义我们自己的类。 泛型类型是在类型上参数化的类或接口。 我们使用尖括号 (<>) 来指定类型参数。 为了泛型带来的好处，假设我们有一个简单的类：

```java
public class GenericsTypeOld {

	private Object t;

	public Object get() {
		return t;
	}

	public void set(Object t) {
		this.t = t;
	}

        public static void main(String args[]){
		GenericsTypeOld type = new GenericsTypeOld();
		type.set("Pankaj"); 
		String str = (String) type.get(); //type casting, error prone and can cause ClassCastException
	}
}
```

请注意，在使用此类时，我们必须使用类型转换，它会在运行时产生 ClassCastException。 现在我们将使用java泛型类来重写相同的类，如下所示。

```java
public class GenericsType<T> {

	private T t;
	
	public T get(){
		return this.t;
	}
	
	public void set(T t1){
		this.t=t1;
	}
	
	public static void main(String args[]){
		GenericsType<String> type = new GenericsType<>();
		type.set("Pankaj"); //valid
		
		GenericsType type1 = new GenericsType(); //raw type
		type1.set("Pankaj"); //valid
		type1.set(10); //valid and autoboxing support
	}
}
```

注意在 main 方法中使用了 GenericsType 类。 我们不需要进行类型转换，在运行时消除了 ClassCastException。 如果我们在创建时没有提供类型，编译器将产生警告“GenericsType 是原始类型。 对泛型类型 GenericType<T> 的引用应该被参数化”。 当我们不提供类型时，类型变为 Object，因此它允许 String 和 Integer 对象。 但是，我们应该始终尽量避免这种情况，因为在处理可能产生运行时错误的原始类型时，我们将不得不使用类型转换。

> 提示：我们可以使用`@SuppressWarnings("rawtypes")` 注解来抑制编译器警告
>
> 

### 泛型接口

`Comparable`是接口中泛型的一个很好的例子，代码如下：

```java
package java.lang;
import java.util.*;

public interface Comparable<T> {
    public int compareTo(T o);
}
```

类似地，我们可以在 java 中创建泛型接口。 我们也可以有多个类型参数，就像在 Map 接口中一样。 同样，我们也可以为参数化类型提供参数化值，例如 `new HashMap<String, List<String>>();`

当实现泛型接口的类，未传入泛型实参时：

```java
public class Person<T> implements Comparable<T> {
    @Override
    public int compareTo(T o) {
        return 0;
    }
}
```

当实现泛型接口的类，传入泛型实参时：

```java
public class Person implements Comparable<Integer> {
    private Integer age;

    @Override
    public int compareTo(Integer o) {
        return 0;
    }
}	
```

### 泛型类型名字约定

Java 泛型类型命名约定有助于我们轻松理解代码，并且拥有命名约定是 Java 编程语言的最佳实践之一。 所以泛型也有自己的命名约定。 通常，类型参数名称是单个大写字母，以便与 java 变量区分开来。 最常用的类型参数名称是：

- E - 元素（被 Java 集合框架广泛使用，例如 ArrayList、Set 等）
- K - Key (用于Map)
- N - 数字
- T - 类型
- V - 值 (用于Map)
- S,U,V 等 - 第 2、第 3、第 4 类型

### 泛型方法

有时我们不希望整个类都被参数化，在这种情况下，我们可以创建 java 泛型方法。泛型类，是在实例化类的时候指明泛型的具体类型；泛型方法，是在调用方法的时候指明泛型的具体类型。这是一个 java 泛型方法示例：

```java
public <T> T genericMethod(Class<T> tClass)throws InstantiationException ,
  IllegalAccessException{
        T instance = tClass.newInstance();
        return instance;
}
```

这个示例中，在`public`后面的<T>表明这是一个泛型方法，可以在方法中使用泛型类型T。只有在声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。使用方法如下：

```java
Object obj = genericMethod(Class.forName("com.test.test"));
```

#### 泛型方法的基本用法

光看上面的例子有的同学可能依然会非常迷糊，我们再通过一个例子，把我泛型方法再总结一下。

```java
public class GenericsTest {
    public class Generics<T> {
        private T key;

        public Generics(T key) {
            this.key = key;
        }

        //我想说的其实是这个，虽然在方法中使用了泛型，但是这并不是一个泛型方法。
        //这只是类中一个普通的成员方法，只不过他的返回值是在声明泛型类已经声明过的泛型。
        //所以在这个方法中才可以继续使用 T 这个泛型。
        public T getKey() {
            return key;
        }

        /*
        这个方法显然是有问题的，在编译器会给我们提示这样的错误信息"cannot reslove symbol E"
        因为在类的声明中并未声明泛型E，所以在使用E做形参和返回值类型时，编译器会无法识别。
        public E setKey(E Key) {
            this.ket = key
        }
         */
    }

    /*
    这是一个真正的泛型方法。
    首先在public与返回值之间的<T>必不可少，这表明这是一个泛型方法，并且声明了一个泛型T
    这个T可以出现在这个泛型方法的任意位置.
    泛型的数量也可以为任意多个
    */
    public <T> T showKeyName(Generics<T> container) {
        System.out.println("container key:" + container.getKey());
        T keyName = container.getKey();
        return keyName;
    }

    //这不是一个泛型方法，这就是一个普通方法，只是使用了Generic<Number>这个泛型类做形参
    public void showKeyValue1(Generics<Number> obj) {
        System.out.println("Generic test, the value of key is" + obj.getKey());
    }

    //这也不是一个泛型方法，这也是一个普通的方法，只不过使用了泛型通配符?
    //同时这也印证了泛型通配符章节所描述的，?是一种类型实参，可以看做为Number等所有类的父类
    public void showKeyValue2(Generics<?> obj) {
        System.out.println("Generic test, the value of key is" + obj.getKey());
    }

    /**
     * 这个方法是有问题的，编译器会为我们提示错误信息："UnKnown class 'E' "
     * 虽然我们声明了<T>,也表明了这是一个可以处理泛型的类型的泛型方法。
     * 但是只声明了泛型类型T，并未声明泛型类型E，因此编译器并不知道该如何处理E这个类型。
     public <T> T showKeyName(Generic<E> container) {

     }
     */

    /**
     * 这个方法也是有问题的，编译器会为我们提示错误信息："UnKnown class 'T' "
     * 对于编译器来说T这个类型并未项目中声明过，因此编译也不知道该如何编译这个类。
     * 所以这也不是一个正确的泛型方法声明。
     public void showkey(T genericObj){

     }
     */
}
```

#### 类中的泛型方法

泛型方法可以出现杂任何地方和任何场景中使用，但是有一种情况是非常特殊的，当泛型方法出现在泛型类中时，我们再通过一个例子看一下。

```java
public class GenericsFruits {

    public static void main(String[] args) {
        Apple apple = new Apple();
        People people = new People();

        MyGenericsTest<Fruit> genericsTest = new MyGenericsTest<>();
        //apple是Fruit的子类，所以这里可以
        genericsTest.show1(apple);
        //编译器会报错，因为泛型类型实参指定的是Fruit，而传入的实参类是People
        //genericsTest.show1(people);

        //使用这两个方法都可以成功
        genericsTest.show2(apple);
        genericsTest.show2(people);

        //使用这两个方法也都可以成功
        genericsTest.show3(apple);
        genericsTest.show3(people);

    }
}

class Fruit {
    public String toString() {
        return "fruit";
    }
}

class Apple extends Fruit {
    public String toString() {
        return "apple";
    }
}

class People {
    public String toString() {
        return "people";
    }
}

class MyGenericsTest<T> {
    public void show1(T t) {
        System.out.println(t.toString());
    }

    //在泛型类中声明了一个泛型方法，使用泛型E，这种泛型E可以为任意类型。可以类型与T相同，也可以不同。
    //由于泛型方法在声明的时候会声明泛型<E>，因此即使在泛型类中并未声明泛型，编译器也能够正确识别泛型方法中识别的泛型。
    public <E> void show2(E t) {
        System.out.println(t.toString());
    }

    //在泛型类中声明了一个泛型方法，使用泛型T，注意这个T是一种全新的类型，可以与泛型类中声明的T不是同一种类型。
    public <T> void show3(T t){
        System.out.println(t.toString());
    }
}
```

#### 泛型方法与可变参数

再看一个泛型方法和可变参数的例子：

```java
 public <T> void printMsg( T... args){
        for(T t : args){
            System.out.println("Generics test, t is " + t);
        }
  }
```

使用方法：

```java
printMsg("111",222,"aaaa","2323.4",55.55);
```

#### 静态方法与泛型

静态方法有一种情况需要注意一下，那就是在类中的静态方法使用泛型：静态方法无法访问类上定义的泛型；如果静态方法操作的引用数据类型不确定的时候，必须要将泛型定义在方法上。

即：如果静态方法要使用泛型的话，必须将静态方法也定义成泛型方法 。

```java
public class StaticGenerator<T> {
    ....
    ....
    /**
     * 如果在类中定义使用泛型的静态方法，需要添加额外的泛型声明（将这个方法定义成泛型方法）
     * 即使静态方法要使用泛型类中已经声明过的泛型也不可以。
     * 如：public static void show(T t){..},此时编译器会提示错误信息：
          "StaticGenerator cannot be refrenced from static context"
     */
    public static <T> void show(T t){

    }
}
```

#### 泛型方法总结

泛型方法能使方法独立于类而产生变化，以下是一个基本的指导原则：
无论何时，如果你能做到，你就该尽量使用泛型方法。也就是说，如果使用泛型方法将整个类泛型化，那么就应该使用泛型方法。另外对于一个static的方法而已，无法访问泛型类型的参数。所以如果static方法要使用泛型能力，就必须使其成为泛型方法。               

### 泛型边界

假设我们想要限制可以在参数化类型中使用的对象类型，例如在比较两个对象的方法中，并且我们想要确保接受的对象是 Comparables。 要声明有界类型参数，需要写出类型参数的名称，后跟 extends 关键字，然后是其上限，类似于下面的方法。       

```java
public static <T extends Comparable<T>> int compare(T t1, T t2){
		return t1.compareTo(t2);
	}
```

这些方法的调用类似于无界方法，只是如果我们尝试使用任何非 Comparable 类，则会引发编译时错误。 有界类型参数可以与方法以及类和接口一起使用。 Java 泛型也支持多个边界，即 <T extends A & B & C>。 在这种情况下，A 可以是接口或类。 如果 A 是类，那么 B 和 C 应该是一个接口。 我们不能在多个范围内拥有多个类。

### 泛型和继承

我们知道，如果 A 是 B 的子类，Java 继承允许我们将一个变量 A 分配给另一个变量 B。所以我们可能认为 A 的任何泛型类型都可以分配给 B 的泛型类型，但事实并非如此。 让我们用一个简单的程序来看看。                                                                                                                                                                                               

```java
public class GenericsInheritance {

	public static void main(String[] args) {
		String str = "abc";
		Object obj = new Object();
		obj=str; // works because String is-a Object, inheritance in java
		
		MyClass<String> myClass1 = new MyClass<String>();
		MyClass<Object> myClass2 = new MyClass<Object>();
		//myClass2=myClass1; // compilation error since MyClass<String> is not a MyClass<Object>
		obj = myClass1; // MyClass<T> parent is Object
	}
	
	public static class MyClass<T>{}
}
```

我们不允许将 MyClass<String> 变量分配给 MyClass<Object> 变量，因为它们不相关，实际上 MyClass<T> 的父级是 Object。

### 泛型类和子类型

我们可以通过扩展或实现泛型类或接口来创建它们的子类型。 一个类或接口的类型参数与另一个类或接口的类型参数之间的关系由extends 和implements 子句确定。 例如，ArrayList<E> 实现了扩展 了Collection<E> 的 List<E>，因此 ArrayList<String> 是 List<String> 的子类型，而 List<String> 是 Collection<String> 的子类型。 只要我们不更改类型参数，子类型关系就会被保留，下面显示了多个类型参数的示例。

```java
interface MyList<E,T> extends List<E>{
}	
```

List<String> 的子类型可以是 MyList<String,Object>、MyList<String,Integer> 等。

### 泛型通配符

问号 (?) 是泛型中的通配符，表示未知类型。 通配符可用作参数、字段或局部变量的类型，有时还可用作返回类型。 我们不能在调用泛型方法或实例化泛型类时使用通配符。 接下来，我们将了解上限通配符、下限通配符和通配符捕获。

#### Java 泛型上界通配符

上限通配符用于放宽对方法中变量类型的限制。 假设我们要编写一个方法来返回列表中数字的总和，那么我们的实现将是这样的。

```java
public static double sum(List<Number> list){
		double sum = 0;
		for(Number n : list){
			sum += n.doubleValue();
		}
		return sum;
	}
```

现在，上述实现的问题是它不适用于整数列表或双精度列表，因为我们知道 List<Integer> 和 List<Double> 是不相关的，这是上限通配符有用的时候。 我们使用带有 extends 关键字的泛型通配符和上限类或接口，这将允许我们传递上限或其子类类型的参数。 上面的实现可以像下面的程序一样修改。

```java
public class GenericsWildcards {

	public static void main(String[] args) {
		List<Integer> ints = new ArrayList<>();
		ints.add(3); ints.add(5); ints.add(10);
		double sum = sum(ints);
		System.out.println("Sum of ints="+sum);
	}

	public static double sum(List<? extends Number> list){
		double sum = 0;
		for(Number n : list){
			sum += n.doubleValue();
		}
		return sum;
	}
}
```

就像我们在接口上写代码一样，在上面的方法中我们可以使用上界类 Number 的所有方法。 请注意，对于有界列表，我们不允许将任何对象添加到列表中，但 null 除外。 如果我们尝试在 sum 方法中向列表中添加一个元素，程序将无法编译。

#### 泛型无界通配符

有时我们希望我们的泛型方法适用于所有类型，在这种情况下，可以使用无界通配符。 它与使用 <?  extends Object>。

```java
public static void printData(List<?> list){
		for(Object obj : list){
			System.out.print(obj + "::");
		}
}
```

我们可以为 printData 方法提供 List<String> 或 List<Integer> 或任何其他类型的 Object 列表参数。 与上限列表类似，我们不允许向列表中添加任何内容。

#### 泛型下界通配符

假设我们想将整数添加到方法中的整数列表中，我们可以将参数类型保留为 List<Integer> 但它将与 Integers 绑定，而 List<Number> 和 List<Object> 也可以保存整数，所以 我们可以使用下界通配符来实现这一点。 我们使用带有 super 关键字和下界类的泛型通配符 (?) 来实现这一点。 我们可以将下限或下限的任何超类型作为参数传递，在这种情况下，java 编译器允许将下限对象类型添加到列表中。

```java
public static void addIntegers(List<? super Integer> list){
		list.add(new Integer(50));
}
```

#### 使用泛型通配符进行子类型化

```java
List<? extends Integer> intList = new ArrayList<>();
List<? extends Number>  numList = intList;  // OK. List<? extends Integer> is a subtype of List<? extends Number>
```

### 泛型类型擦除

Java 中的泛型被添加以在编译时提供类型检查，但在运行时没有用处，因此 Java 编译器使用类型擦除功能删除字节码中的所有泛型类型检查代码，并在必要时插入类型转换。 类型擦除确保不会为参数化类型创建新类； 因此，泛型不会产生运行时开销。 例如，如果我们有一个像下面这样的泛型类；

```java
public class Test<T extends Comparable<T>> {

    private T data;
    private Test<T> next;

    public Test(T d, Test<T> n) {
        this.data = d;
        this.next = n;
    }

    public T getData() { return this.data; }
}
```

Java 编译器将有界类型参数 T 替换为第一个绑定接口 Comparable，如下代码：

```java
public class Test {

    private Comparable data;
    private Test next;

    public Node(Comparable d, Test n) {
        this.data = d;
        this.next = n;
    }

    public Comparable getData() { return data; }
}
```

## 4.泛型带来的好处

泛型提供强大的编译时类型检查并降低 ClassCastException 和对象显式转换的风险。

## 5.泛型在 Java 中是如何工作

通用代码确保类型安全。编译器使用类型擦除在编译时删除所有类型参数，以减少运行时的重载。

