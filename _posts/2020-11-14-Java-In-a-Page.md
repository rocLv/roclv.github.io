---
  layout: post
  title:  Java in a Page
---

##### Java Basic

This is a java tutorial in one page.

<!--more-->


```java

// Tutorial.java
package com.roclv;

import java.io.*;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.*;

// Reference: https://www.runoob.com/java/java-tutorial.html
// abstract only can be used in abstract class.

// public abstract AbstractClass {
//    public abstract String abstractMethod();
//    public abstract String abstractString;
//
// }

class Fruit {
    enum Size { SMALL, MEDIUM, LAR }
    Size size;
}


// Only one public class is allowed in one class file.
public class Tutorial {

    public static String  publicString = "This is a  public String";
    protected static String protectedString = "This is a protected string which could be inheritenced by subclass";
    private static String privateString = "This is a private string that only can be used in own class";

    private final static String CONST = "This is a const";

    // only could show in class
    public transient String willNotBeSerialized = "ignored";

    public volatile boolean active = true;

    public Tutorial(){
        // default construct method.
    }

    public Tutorial(String language) {
        System.out.println("This is another construct method.");
    }

    public static void main(String[] args) {
        // Basic data type

        // [-127, 127 -1]
        byte by = 0;

        // [-2 ** 15, 2 ** 15 - 1]
        short sh = 2;

        // -2 ** 31, 2 ** 31 - 1
        int i = 0;

        // -2 ** 63, 2 ** 63 - 1
        long l = 1000L;

        // -2 ** 31, 2 ** 31 - 1
        float f = 0.01f;

        // -2 ** 63, 2 ** 63 - 1
        double d = 0.0d;

        // default: false
        boolean bool = false;

        // \u0000 - \uffff
        char ch = 'a';

        int $a = 1, b = 2, _c = 3;

        System.out.println("Public String: " + publicString);
        System.out.println("Protected String: " + protectedString);
        System.out.println("Private String: " + privateString);

        System.out.println("Legal identities name: $a = " + $a + " b = " + b + "_c = " + _c);

        // enum can be used as CONSTANT.
        System.out.println("Fruit size: " + Fruit.Size.SMALL);

        System.out.println(new Tutorial("Java"));

        System.out.println(1 + 2 * 3 / 4 % 5 + i++ - b--);

        System.out.println( true || false && false != true || 1 > 0 || 3 < 2 || 4 >= 5 || 3 == 3);

        System.out.println( ~1 & 0 | 1 ^ 0 << 2 >> 2 >>> 3 );

        System.out.println( b += 3);

        int e = 3 >= 4 ? 4 : 6;

        System.out.println(e);

        System.out.println(" 's' is instanceof String: " + ("s" instanceof String));

        // loop
        while( true ) {
            System.out.println("while loop");
            break;
        }

        do {
            System.out.println("do {} while () loop");
            break;
        } while (true);

        for(int j = 0; j < 1; j++) {
            System.out.println("for loop: " + j);
        }

        int [] numbers  = {1,2,3};

        for(int k : numbers) {
            System.out.println("k: " + k);
        }

        if ( b < 20 ) {
            System.out.println("condition statement");
        } else if ( b > 4 ) {
            System.out.println("condition statement else");
        }

        int y = 1;

        switch (y) {
            case 1:
                System.out.println("y: " + 1);
                y = 2;
                break;
            case 2:
                System.out.println("y: " + y);
                break;
            default:
                System.out.println("switch default");
        }

        // Number & Math
        System.out.println("5.5 Ceil:" + Math.ceil(5.5));

        Integer num = 5;
        System.out.println("5 to double: " + num.doubleValue());
        System.out.println("5 to byte : " + num.byteValue());
        System.out.println("5 to long: " + num.longValue());

        System.out.println("5 > 3 is " + num.compareTo(3));


        // Character, must use single quote
        char chr= 'a';
        char chr2= '\u0394';
        char[] charArray = {'a', 'b'};

        Character character = new Character('a');

        System.out.println("C is letter: " + Character.isLetter('C'));
        System.out.println("9 is digit: " + Character.isDigit('9'));
        System.out.println("C is uppercase: " + Character.isUpperCase('C'));
        System.out.println("c's uppercase is " + Character.toUpperCase('c'));

        System.out.println("c is lowercase: " + Character.isLowerCase('c'));
        System.out.println("C's lowercase is " + Character.toLowerCase('C'));

        // Run system command
        String cmd = "pwd";
        Runtime run = Runtime.getRuntime();
        try {
            Process process = run.exec(cmd);
            process.waitFor();
            BufferedReader buf = new BufferedReader(new InputStreamReader(process.getInputStream()));
            String line = "";
            while((line = buf.readLine()) != null){
               System.out.println(line);
            }
        } catch (IOException | InterruptedException exp ){
            System.out.println(exp.getMessage());
        }

        // String
        char[] chrArray = {'a', 'b', 'c', 'd', 'e', 'f'};
        String str = new String(chrArray);
        System.out.println(str);
        System.out.println(str + "'s length is " + str.length());
        System.out.println("abc".concat("def"));

        System.out.println(String.format("%2f, %d, %s", 5.5, 5, "5.5"));
        System.out.println("the third char of abc is " + "abc".charAt(2));


        System.out.println("abc".compareTo("abc"));
        System.out.println("abc".compareToIgnoreCase("ABC"));
        System.out.println("abc".concat("dev"));
        System.out.println("abc".contentEquals("abc"));

        String str2 = "";
        System.out.println("str copy value of chrArray: " + str2.copyValueOf(chrArray));
        System.out.println("str copy value of chrArray: " + str2.copyValueOf(chrArray, 2, 2));
        System.out.println("abc ends with " + "abc".endsWith("c"));
        System.out.println("abc to bytes: " + "abc".getBytes().toString());
        try {
            System.out.println("abc to bytes: " + "abc".getBytes("UTF-8"));
        } catch (UnsupportedEncodingException ex) {
            ex.printStackTrace();
        }
        System.out.println("abc's hash code is " + "abc".hashCode());
        System.out.println("a in abc of position is " + "abc".indexOf('a'));

        char [] newChars = new char[6];
        "abc".getChars(0, 2, newChars, 0);
        System.out.println("get ab from abc: " + (new String(newChars)));

        System.out.println("abc matches a: " + "abc".matches("a(.*)"));
        System.out.println("abc".regionMatches(1, "bc", 0, 2));
        System.out.println("abcabc".replace('a', '*'));
        System.out.println("abc".replace("ab", "de"));
        System.out.println("abc".replaceAll("b", "@"));
        System.out.println("a,b,c".split(","));
        System.out.println("  a,b,c   ".trim());
        System.out.println(String.valueOf(0x10));
        System.out.println("abc".contains("a"));

        /*
            StringBuffer VS StringBuilder

            1. StringBuilder not thread safe
            2. StringBuilder is faster.
         */

        StringBuilder sb = new StringBuilder("I am faster, but not thread safe.");
        sb.append("Yeah.");
        System.out.println(sb);

        StringBuffer stringBuffer = new StringBuffer();
        stringBuffer.append("I am slower, but thread safe. \n");
        stringBuffer.append("If you need thread safe, yeah, it's me.");
        stringBuffer.append("My capacity is ".concat(String.valueOf(stringBuffer.capacity())));
        System.out.println(stringBuffer);


        // Array
        // type[] var;
        // type [] var = new type[length];
        double [] list = new double[4];
        list[0] = 1;
        list[1] = 2;
        list[2] = 3;
        list[3] = 4;

        double sum = 0;
        for(double element : list) {
           sum += element;
        }

        System.out.println("sum of list is " + sum);


        // Date
        // Get now time
        Date date = new Date();
        System.out.println(new Date());
        System.out.println((new Date()).getTime());
        System.out.println(new Date(2020, 11,18));
        System.out.println(new Date(20, 11, 18));

        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        System.out.println(format.format(new Date()));
        Date now = new Date();
        /*
          c 包括全部日期和时间信息 星期六 十月 27 14:21:20 CST 2007
         */
        System.out.printf("%tc%n", now);
        System.out.printf("%tF%n", now);
        System.out.printf("%tD%n", now);
        System.out.printf("%tr%n", now);
        System.out.printf("%tT%n", now);
        System.out.printf("%tR%n", now);

        try {
            System.out.println(format.parse("2020-11-18 11:11:11"));
        } catch (ParseException ex) {
            ex.printStackTrace();
        }

        Calendar calendar = Calendar.getInstance();
        calendar.set(2020, 11, 18);
        Calendar today = Calendar.getInstance();
        Calendar tomorrow = Calendar.getInstance();
        tomorrow.add(Calendar.DATE, 1);
        System.out.println("Tomorrow is " + tomorrow.get(Calendar.YEAR) + "-"
                + tomorrow.get(Calendar.MONTH) + "-"
                + tomorrow.get(Calendar.DATE));
        format.setTimeZone(tomorrow.getTimeZone());
        System.out.println(format.format(tomorrow.getTime()));


        // Collection

        // ArrayList
        ArrayList<String> strList = new ArrayList<>();
        strList.add("a");
        strList.add("b");
        strList.add("c");

        for(String string : strList) {
            System.out.println("String Array List: " + string);
        }

        // HashMap
        HashMap<String, String> hashMap = new HashMap<>();
        hashMap.put("one", "value");
        hashMap.put("two", "value2");

        System.out.println(hashMap);

        hashMap.forEach((key, value) -> {
            System.out.println(key + ": " + value);
        });



        // Oriented Object

        Monkey monkey = new Monkey();
        monkey.run();
        monkey.fly();
        monkey.rob();


        // Generics
        Integer [] intArray = {1, 2, 3};
        printArray(intArray);

        Box<String> box = new Box<String>();
        box.setT("This is a box");
        box.print();

    }

    /**
     *
     * @param array
     * @param <E>
     *
     *     所有泛型方法声明都有一个类型参数声明部分（由尖括号分隔），
     *         该类型参数声明部分在方法返回类型之前（在下面例子中的<E>）。
     *     每一个类型参数声明部分包含一个或多个类型参数，参数间用逗号隔开。
     *         一个泛型参数，也被称为一个类型变量，是用于指定一个泛型类型名称的标识符。
     *     类型参数能被用来声明返回值类型，并且能作为泛型方法得到的实际参数类型的占位符。
     *     泛型方法体的声明和其他方法一样。注意类型参数只能代表引用型类型，
     *        不能是原始类型（像int,double,char的等）。
     */
    public static < E > void printArray(E [] array){
        for( E e : array){
            System.out.printf("%s ", e );
        }

        System.out.println();
    }

    public static <T extends Comparable<T>> T maximum(T x, T y){
        return x.compareTo(y) > 0 ? x : y;
    }

    public static <T, U> void print(T [] t, U [] u){
        printArray(t);
        printArray(u);
    }

    public static class Box<T>{
        private T t;

        public void setT (T t){
            Tutorial.Box.this.t = t;
        }

        public void print(){
            System.out.println(t);
        }
    }

    public static void printList(List<? extends String> list){
        System.out.println(list.get(0));
    }


    public synchronized void run() {
        System.out.println("Only one thread could visit in the same time.");
    }


}

## 面向对象

```java


/**
    1. 抽象类不能被实例化(初学者很容易犯的错)，如果被实例化，就会报错，编译无法通过。只有抽象类的非抽象子类可以创建对象。

    2. 抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。

    3. 抽象类中的抽象方法只是声明，不包含方法体，就是不给出方法的具体实现也就是方法的具体功能。

    4. 构造方法，类方法（用 static 修饰的方法）不能声明为抽象方法。

    5. 抽象类的子类必须给出抽象类中的抽象方法的具体实现，除非该子类也是抽象类。
 **/

public abstract class AbstractCar {
    private String color;

    public abstract int startEngine();
}

public class Animal {
    private String name;

    public void run() {
        System.out.println("I can run");
    }
}

public final class Monkey extends Animal implements Fly, Rob{
    private String kind;

    @Override
    public void run() {
        super.run();
        System.out.println("I can jump");
    }

    @Override
    public void fly() {
        System.out.println("I can fly.");
    }

    @Override
    public void rob() {
        System.out.println("I can rob.");
    }
}


/**
 * 接口与类相似点：
 *    一个接口可以有多个方法。
 *    接口文件保存在 .java 结尾的文件中，文件名使用接口名。
 *    接口的字节码文件保存在 .class 结尾的文件中。
 *    接口相应的字节码文件必须在与包名称相匹配的目录结构中。
 *
 * 接口与类的区别：
 *    接口不能用于实例化对象。
 *    接口没有构造方法。
 *    接口中所有的方法必须是抽象方法。
 *    接口不能包含成员变量，除了 static 和 final 变量。
 *    接口不是被类继承了，而是要被类实现。
 *    接口支持多继承。
 *
 * 接口特性
 *    接口中每一个方法也是隐式抽象的,
 *    接口中的方法会被隐式的指定为 public abstract（只能是 public abstract，其他修饰符都会报错）。
 *    接口中可以含有变量，但是接口中的变量会被隐式的指定为 public static final 变量（并且只能是
 *       public，用 private 修饰会报编译错误）。
 *    接口中的方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法。
 *
 * 抽象类和接口的区别
 *    1. 抽象类中的方法可以有方法体，就是能实现方法的具体功能，但是接口中的方法不行。
 *    2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 public static final 类型的。
 *    3. 接口中不能含有静态代码块以及静态方法(用 static 修饰的方法)，而抽象类是可以有静态代码块和静态方法。
 *    4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口
 */
public interface Fly {
    public void fly();
}

public interface Rob {
    public void rob();
}


```

// Run with follow command.
// javac -d . *.java
// java -cp com/roclv com.roclv.Tutorial


