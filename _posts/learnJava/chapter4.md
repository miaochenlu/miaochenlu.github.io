### 1. Foreach

```java
import java.util.*;
public class ForEachFloat {
  public static void main(String[] args) {
    Random rand = new Random(47);
    float f[] = new float[10];
    for(int i = 0; i < 10; i++)
      f[i] = rand.nextFloat();
    for(float x : f)
      System.out.println(x);
	}
}
```

注意：

```java
for(float x : f)
```

这条语句定义了一个float类型的变量x, 继而将每一个f的元素赋值给x

任何返回一个数组的方法都可以使用foreach。

例如， String类有一个方法toCharArray(), 他返回一个char数组, 因此可以很容易得像下面这样迭代在字符串里面的所有字符

```java
public class ForEachString{
  public static void main(String[] args) {
    for(char c : "An African Swallow".toCharArray())
      System.out.print(c + " ");
  }
}
```





