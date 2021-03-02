## Java8之Function接口

### Function接口介绍
Java8 添加了一个新的特性Function，是一个函数式接口

所有标注了@FunctionalInterface注解的接口都是函数式接口，具体来说，所有标注了该注解的接口都将能用在lambda表达式上。
先看Function接口源码
```
package java.util.function;

import java.util.Objects;

@FunctionalInterface
public interface Function<T, R> {

    /**
     * Applies this function to the given argument.
     *
     * @param t the function argument
     * @return the function result
     */
    R apply(T t);

    /**
     * Returns a composed function that first applies the {@code before}
     * function to its input, and then applies this function to the result.
     * If evaluation of either function throws an exception, it is relayed to
     * the caller of the composed function.
     */
    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

    /**
     * Returns a composed function that first applies this function to
     * its input, and then applies the {@code after} function to the result.
     * If evaluation of either function throws an exception, it is relayed to
     * the caller of the composed function.
     */
    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }

    /**
     * Returns a function that always returns its input argument.
     *
     * @param <T> the type of the input and output objects to the function
     * @return a function that always returns its input argument
     */
    static <T> Function<T, T> identity() {
        return t -> t;
    }
}
```

### Function接口实例化
对于这样的函数式接口，因为只有一个抽象函数，所以只要实现了该函数就能够实例化对象了。所以可以通过下面这一行代码的方式实例化一个“接口”对象：
例如：
```
Function<Integer, Integer> fun = x -> x + 1;
```

其中等号（=）右边的是Function接口中的那个抽象方法的实现。鉴于此，我们可以得出一个结论：
对于函数式接口，形如这样的表达式（x -> x + 1）能够实例化一个对象。并且该表达式正是那个唯一的抽象方法的实现。

### 源码解析
#### 1.apply
讲完了上面这些就可以开始研究源码了。
首先我们已经知道了Function是一个泛型类，其中定义了两个泛型参数T和R，在Function中，T代表输入参数，R代表返回的结果。

也许你很好奇，为什么跟别的java源码不一样，Function 的源码中并没有具体的逻辑呢？
其实这很容易理解，Function 就是一个函数，其作用类似于数学中函数的定义，（x,y）跟<T,R>的作用几乎一致。
```
y=f(x)
```
所以Function中没有具体的操作，具体的操作需要我们去为它指定，因此apply具体返回的结果取决于传入的lambda表达式。
```
R apply(T t);
```

举个例子：
```
public void test(){
    Function<Integer,Integer> test=i->i+1;
    test.apply(5);
}
/** print:6*/
```

我们用lambda表达式定义了一个行为使得i自增1，我们使用参数5执行apply，最后返回6。这跟我们以前看待Java的眼光已经不同了，在函数式编程之前我们定义一组操作首先想到的是定义一个方法，然后指定传入参数，返回我们需要的结果。

**函数式编程的思想是先不去考虑具体的行为，而是先去考虑参数，具体的方法我们可以后续再设置。**

再举个例子：
```
public void test(){
    Function<Integer,Integer> test1=i->i+1;
    Function<Integer,Integer> test2=i->i*i;
    System.out.println(calculate(test1,5));
    System.out.println(calculate(test2,5));
}
public static Integer calculate(Function<Integer,Integer> test,Integer number){
    return test.apply(number);
}
/** print:6*/
/** print:25*/
```

我们通过传入不同的Function，实现了在同一个方法中实现不同的操作。在实际开发中这样可以大大减少很多重复的代码，比如我在实际项目中有个新增用户的功能，但是用户分为VIP和普通用户，且有两种不同的新增逻辑。那么此时我们就可以先写两种不同的逻辑。除此之外，这样还让逻辑与数据分离开来，我们可以实现逻辑的复用。

当然实际开发中的逻辑可能很复杂，比如两个方法F1,F2都需要两个个逻辑AB，但是F1需要A->B，F2方法需要B->A。这样的我们用刚才的方法也可以实现，源码如下：
```
public void test(){
    Function<Integer,Integer> A=i->i+1;
    Function<Integer,Integer> B=i->i*i;
    System.out.println("F1:"+B.apply(A.apply(5)));
    System.out.println("F2:"+A.apply(B.apply(5)));
}
/** F1:36 */
/** F2:26 */
```

### 2.compose和andThen
compose和andThen可以解决我们的问题。
先看compose的源码
```
    /**
     * Returns a composed function that first applies the {@code before}
     * function to its input, and then applies this function to the result.
     * If evaluation of either function throws an exception, it is relayed to
     * the caller of the composed function.
     *
     * @param <V> the type of input to the {@code before} function, and to the
     *           composed function
     * @param before the function to apply before this function is applied
     * @return a composed function that first applies the {@code before}
     * function and then applies this function
     * @throws NullPointerException if before is null
     *
     * @see #andThen(Function)
     */
    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }
```

compose接收一个Function参数，返回时先用传入的逻辑执行apply，然后使用当前Function的apply。

andThen跟compose正相反，先执行当前的逻辑，再执行传入的逻辑。
```
    /**
     * Returns a composed function that first applies this function to
     * its input, and then applies the {@code after} function to the result.
     * If evaluation of either function throws an exception, it is relayed to
     * the caller of the composed function.
     *
     * @param <V> the type of output of the {@code after} function, and of the
     *           composed function
     * @param after the function to apply after this function is applied
     * @return a composed function that first applies this function and then
     * applies the {@code after} function
     * @throws NullPointerException if after is null
     *
     * @see #compose(Function)
     */
    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
```

这样说可能不够直观，我可以换个说法给你看看

B.compose(A).apply(5)
compose等价于B.apply(A.apply(5))

B.andThen(A).apply(5))
而andThen等价于A.apply(B.apply(5))。

代码如下：
```
    @Test
    //@Ignore
    public void testFunction() {
        Function<String, String> f1 = (str) -> str.concat("fun");
        Function<String, String> f2 = (str) -> str.concat("abc");

        String input = "ricky_";
        System.out.println("===== compose =====");
        System.out.println("F1="+f1.compose(f2).apply(input));
        //等价形式
        System.out.println("F1="+f1.apply(f2.apply(input)));

        System.out.println("===== andThen =====");
        System.out.println("F2="+f1.andThen(f2).apply(input));
        //等价形式
        System.out.println("F2="+f2.apply(f1.apply(input)));
    }
```
输出结果如下：
```
===== compose =====
F1=ricky_abcfun
F1=ricky_abcfun
===== andThen =====
F2=ricky_funabc
F2=ricky_funabc
```

我们可以看到上述两个方法的返回值都是一个Function，这样我们就可以使用建造者模式的操作来使用。

### 3 identity方法
Java 8允许在接口中加入具体方法。接口中的具体方法有两种，default方法和static方法，identity()就是Function接口的一个静态方法。
Function.identity()返回一个输出跟输入一样的Lambda表达式对象，等价于形如t -> t形式的Lambda表达式

```
    /**
     * Returns a function that always returns its input argument.
     *
     * @param <T> the type of the input and output objects to the function
     * @return a function that always returns its input argument
     */
    static <T> Function<T, T> identity() {
        return t -> t;
    }
```
