# 第 8 章 泛型程序设计

> 2018年5月23日

## 8.1 为什么使用泛型程序设计

使用泛型编写的代码可以被很多不同类型的对象所重用。

不使用泛型的代码存在两个问题：

- 当获取一个值时必需进行强制类型转换。

```java,L1
List files = new ArrayList();
// do something
String filename = (String) files.get(0);
```

- 没有错误检查。可以向 ArrayList 中添加任何类的对象。

```java,L1
files.add(new File("..."));
```
泛型编程利用**类型参数**（type parameters）解决了以上两个问题，使程序编写更容易、程序更安全、可读性更高。

## 8.2 定义简单泛型类

```java,L1
/**
 * 将类型变量 T 用尖括号括起来，放在类名的后面
 * @param <T>
 */
public class Pair<T> {

  private T first;
  private T second;

  public Pair() {
    first = null;
    second = null;
  }
  public Pair(T first, T second) {
    this.first = first;
    this.second = second;
  }
  public T getFirst() {
    return first;
  }
  public T getSecond() {
    return second;
  }
  public void setFirst(T newValue) {
    first = newValue;
  }
  public void setSecond(T newValue) {
    second = newValue;
  }
}
```

用具体的类型替换类型变量叫做**实例化泛型类型**：

```
Pair<String>
```

### 使用泛型类

```java,L1
public class ArrayAlg {

  public static void main(String[] args) {
    String[] words = { "Mary", "had", "a", "little", "lamb" };
    Pair<String> mm = ArrayAlg.minmax(words);
    System.out.println("min = " + mm.getFirst());
    System.out.println("max = " + mm.getSecond());
  }

  public static Pair<String> minmax(String[] a) {
    if (a == null || a.length == 0)
      return null;

    String min = a[0];
    String max = a[0];

    for (int i = 1; i < a.length; i++) {
      if (min.compareTo(a[i]) > 0){
	min = a[i];
      }
      if (max.compareTo(a[i]) < 0){
	max = a[i];
      }
    }

    return new Pair<>(min, max);
  }
}
```

## 8.3 泛型方法

泛型方法可以定义在泛型类中，也可以定义在普通类中。类型变量放在修饰符后面，返回类型前面。

```java,L1
public class ArrayAlg {

  public static <T> T getMiddle(T... a) {
    return a[a.length / 2];
  }
}
```

**调用泛型方法：**

```java,L1
String middle = ArrayAlg.<String>getMiddle("John", "Tommy", "Q");
```

可以省略 `<String>` 类型参数:

```java,L1
String middle = ArrayAlg.getMiddle("John", "Tommy", "Q");
```

编译器会对下面的调用报错：Type safety: A generic array of Number&Comparable<?> is created for a varargs parameter.

```java,L1
double middle = ArrayAlg.getMiddle(7.3, 3, 5);
```

[原因] 编译器会自动将三个参数打包一个为一个 Double 类型和两个 Integer 类型，而后寻找这些类的共同超类型。事实上，会找到两个这样的超类型：Number 和 Comparable 接口。

[解决方法] 将所有的参数改为 double 值：

```java,L1
double middle = ArrayAlg.getMiddle(7.3, 3.0, 5.0);
```

## 8.4 类型变量的限定

[提出问题] 假设我们希望计算数组中的最小元素，看下面定义的方法：

```java,L1
class ArrayAlg {
  public static <T> T min(T[] a) {
    if (a == null || a.length == 0)
      return null;
    T smallest = a[0];
    for (int i = 1; i < a.length; i++) {
      if (smallest.compareTo(a[i]) > 0)
	smallest = a[i];
    }
    return smallest;
  }
}
```

上面的代码的问题在于，无法保证 T 具有 compareTo 方法，因为 T 可以是任何类型。解决方法是限定 T 为 Compareable 的子类：`<T extends Comparable<T>>`

```java,L1
class ArrayAlg {
  public static <T extends Comparable<T>> T min(T[] a) {
    if (a == null || a.length == 0)
      return null;
    T smallest = a[0];
    for (int i = 1; i < a.length; i++) {
      if (smallest.compareTo(a[i]) > 0)
	smallest = a[i];
    }
    return smallest;
  }
}
```

研究下面的程序，它计算一个泛型数组的最大值和最小值：

```java,L1
class ArrayAlg {
  public static <T extends Comparable<T>> Pair<T> minMax(T[] a) {
    if (a == null || a.length == 0)
      return null;
    T min = a[0];
    T max = a[0];
    for (int i = 0; i < a.length; i++) {
      if (min.compareTo(a[i]) > 0) {
	min = a[i];
      }
      if (max.compareTo(a[i]) < 0) {
	max = a[i];
      }
    }
    return new Pair<>(min, max);
  }
}
```


### 多个限定

限定可以有多个，如：

```
T extends Comparable & Serializable
```

多个限定中最多只能有一个类，多个接口，且类要放在限定列表中的第一个。


## 8.5 泛型代码和虚拟机

虚拟机没有泛型对象，所有对象都属于普通类。

### 8.5.1 类型擦除

类型擦除会将类型变量替换为限定类型，无限定的变量用 Object。

例如，`Pair<T>` 类型擦除后变为：

```java,L1
public class Pair {
  private Object first;
  private Object second;

  public Pair() {
    first = null;
    second = null;
  }

  public Pair(Object first, Object second) {
    this.first = first;
    this.second = second;
  }

  public Object getFirst() {
    return first;
  }

  public Object getSecond() {
    return second;
  }

  public void setFirst(Object newValue) {
    first = newValue;
  }

  public void setSecond(Object newValue) {
    second = newValue;
  }
}
```








