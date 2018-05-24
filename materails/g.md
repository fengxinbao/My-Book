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
      if (min.compareTo(a[i]) > 0)
	min = a[i];
      if (max.compareTo(a[i]) < 0)
	max = a[i];
    }

    return new Pair<>(min, max);
  }
}
```



