# 新 Java，新 Spring


这里提供一份简要的JDK5.0 到 JDK 8的崭新并且重要的特性一览和部分解释。因为Java最重要的还是学习基础语法，学会基础语法的基础上，可以搜索学习各自新版本新语法。

## JDK5:

 - ① 自动拆装箱 (⭐️⭐️⭐️)
 - ② 静态导入 (⭐️⭐️)
 - ③ For Loop (⭐️⭐️⭐️)
 - ④ 可变参数序列 (⭐️⭐️)
 - ⑤ 枚举Enum （⭐️⭐️⭐️）
 - ⑥ 泛型  generic (⭐️⭐️⭐️⭐️⭐️)
         譬如写一个sort method 对integer array，String
              array，等其他支持compare的类型数组 使用 generic method，generic class
         they allow a type or method to operate an objects of
              various types while providing compile-time type safety.
         使得 types(classes, interfaces) to be parameters when
              defining classes, interfaces, methods - The difference is
              that the inputs to formal parameters are values, while
              the inputs to type parameters are types.
         好处是：
         1 elimination of casts
             List list = new ArrayList();
                  list.add("hello");
                  String s = (String) list.get(0);
                  When re-written to use generics, the code does not
                  require casting:
                  List<String> list = new ArrayList<String>();
                  list.add("hello");
                  String s = list.get(0);   // no cast

         2 stronger type checks at compile time
         3 enabling programmers to implement generic algorithms
        * [+] Generic Methods
        * [+] Generic Classes
 - ⑦ 注解 (⭐️⭐️⭐️⭐️)
         form of syntactic metadata that can be added to Java
              source code. 如类，方法，变量，参数，包都可以被注解
         where annotations can be used
        * [+] how to apply annotations
         what predefined annotation types are avaiable
         how type annotations can be used in xxx
         练习：
              https://docs.oracle.com/javase/tutorial/java/annotations/Q
              andE/answers.html
 - ⑧ 反射 (⭐️⭐️⭐️⭐️)
         API used to manipulate classes and everything in class
              including fields, methods, constructors, private data etc.
         JUnit 4, for example, will use reflection to look through
              your classes for methods tagged with the @Test
              annotation, and will then call them when running the unit
              test.
         元编程，debugger，ide，test 等
         Method method = foo.getClass().getMethod("doSomething",
              null); method.invoke(foo, null);

### JDK6:

 - ① Desktop和SystemTray类
 - ② Console类 (⭐️⭐️)
 - ③ Compiler API
 - ④ Web Service
 - ⑤ 公共注解类 (⭐️)

### JDK7：

 - ① 字面量改进
 - ② try resource语法 (⭐️⭐️⭐️)
         就是类似于Python的with语法
         when the try block finishes the FileInputStream will be
              closed automatically.
         private static void printFileJava7() throws IOException {

                  try(FileInputStream input = new
              FileInputStream("file.txt")) {

                      int data = input.read();
                      while(data != -1){
                          System.out.print((char) data);
                          data = input.read();
                      }
                  }
              }
         使用custom auto close
             public class MyAutoClosable implements AutoCloseable {

                      public void doIt() {
                          System.out.println("MyAutoClosable doing
                  it!");
                      }

                      @Override
                      public void close() throws Exception {
                          System.out.println("MyAutoClosable closed!");
                      }
                  }
             private static void myAutoClosable() throws Exception
                  {

                      try(MyAutoClosable myAutoClosable = new
                  MyAutoClosable()){
                          myAutoClosable.doIt();
                      }
                  }
 - ③ switch string语法 (⭐️⭐️)
 - ④ 泛型类型推断 (⭐️⭐️)
 - ⑤ catch捕获多个异常
    
### JDK8：

 - ① 接口默认方法 default method (⭐️⭐️⭐️)
         Ruby的Mixin模块貌似是对单继承和多继承两种继承模式的中间态妥协，而Java之前一直强调单继承模式，Java8
              的“允许接口有默认函数”是不是也是这样一种妥协？
         允许用组合而不是继承来方便地组织和复用代码。trait实现得好的语言，如rust，完全没有类继承、类层次这些东西，类
              型关系都是平坦的，其他语言里用继承实现的东西它都用trait来组合
         如果要给一个已有接口添加新方法，这会带来一些问题，因为新方法没有对应的实现，所以，实现这个接口的类就会编译不通过。而
              “default
              method”的引入给了方法一个实现，编译就可以通过了，从而我们可以在不改变已有代码的前提下，为程序库增加新的方法
         不需要同一份代码出现多处而纠结了。总不能定义那么多的类（接口的排列组合
              https://www.zhihu.com/question/25332643
 - ② Lambda表达式 (⭐️⭐️⭐️⭐️⭐️)
        * [+] lambda expressions
         functional interfaces
         method reference
             for(Object o:objCollection) {
                    someInfra.output(o);
                  }

                  objCollection.forEach(someInfra::output)
             为什么Java要用::
                  来表示eta转换，猜测可能是设计者考虑到java广大用户并不那么熟悉函数式风格，直接用一个函数名表达转换后的
                  lambda 容易在理解上有歧义，就像上面 Math.abs
                  可能被新手把abs误解为Math里的一个静态常量而非方法，所以写成 Math::abs 就不容易误解了。

             简单来讲，就是构造一个该方法的闭包。
             比如：
             Math::max等效于(a, b)->Math.max(a, b)
             String::startWith等效于(s1, s2)->s1.startWith(s2)
             s::isEmpty等效于()->s.isEmpty()
 - ③ Stream API (⭐️⭐️⭐️⭐️)
         http://winterbe.com/posts/2014/07/31/java8-stream-tutorial
              -examples/
         不是InputStream之流而是Monads，对引入functional programming 意义重大
         how to work with Java 8 streams
             List<String> myList =
                      Arrays.asList("a1", "a2", "b1", "c2", "c1");

                  myList
                      .stream()
                      .filter(s -> s.startsWith("c"))
                      .map(String::toUpperCase)
                      .sorted()
                      .forEach(System.out::println);
         how to use the different kind of available stream
              operations
             created from various data sources 如collections，lists
                  and sets
             Stream.of("a1", "a2", "a3")
         processing order and it affects runtime performance
             two intermediate operations map and filter and the
                  terminal operation forEach
         stream operations: reduce, collect, flatMap
         parallel stream
             capable of operating on multiple threads
 - ④ Date API
 - ⑤ 多重注解



