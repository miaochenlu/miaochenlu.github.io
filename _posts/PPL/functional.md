https://segmentfault.com/a/1190000019855034

https://my.oschina.net/thinwonton/blog/2992751



求解

```java
public class main{
    public static void main(String[] args) {
        IntStream.range(2, 100)
            .filter(
                x-> IntStream.range(2,x)
                    .filter(y->x % y == 0)
                    .count()==0
            )
            .forEach (
                x->System.out.println(x)
            );
    }
}
```

求解完美数

```java
public class main {
    public static void main(String[] args) {
        IntStream.range(2, 200)
        .filter(
            x->IntStream.range(1, x)
            .filter(y->x%y==0)
            .sum()==x
        )
        .forEach(
            x->System.out.println(x)
        );
    }
}
```

