## 设计模式的目的

编写软件过程中，程序员面临着来自 **耦合性，内聚性以及可维护性，可扩展性，重用性，灵活性** 等多方面的 挑战，设计模式是为了让程序(软件)，具有更好 

代码重用性 (即：相同功能的代码，不用多次编写) 

可读性 (即：编程规范性, 便于其他程序员的阅读和理解) 

可扩展性 (即：当需要增加新的功能时，非常的方便，称为可维护) 

可靠性 (即：当我们增加新的功能后，对原来的功能没有影响) 

使程序呈现高内聚，低耦合的特性

## 设计模式七大原则

设计模式原则，其实就是程序员在编程时，应当遵守的原则，也是各种设计模式的基础(即：设计模式为什么 这样设计的依据)

### 单一职责原则

**对类来说的，即一个类应该只负责一项职责。如类 A 负责两个不同职责：职责 1，职责 2。当职责 1 需求变更 而改变 A 时，可能造成职责 2 执行错误，所以需要将类 A 的粒度分解为 A1，A2**

降低类的复杂度，一个类只负责一项职责。 2) 

提高类的可读性，可维护性 

降低变更引起的风险 

通常情况下，我们应当遵守单一职责原则，只有逻辑足够简单，才可以在代码级违反单一职责原则；只有类中方法数量足够少，可以在方法级别保持单一职责原则

```javascript
public class SingleResponsibility3 {
public static void main(String[] args) {
// TODO Auto-generated method stub
Vehicle2 vehicle2 = new Vehicle2();
vehicle2.run("汽车");
vehicle2.runWater("轮船");
vehicle2.runAir("飞机");
}
}
//1. 这种修改方法没有对原来的类做大的修改，只是增加方法
//2. 这里虽然没有在类这个级别上遵守单一职责原则，但是在方法级别上，仍然是遵守单一职责
class Vehicle2 {
public void run(String vehicle) {
    //处理
    System.out.println(vehicle + " 在公路上运行....");
    }
    public void runAir(String vehicle) {
    System.out.println(vehicle + " 在天空上运行....");
    }
    public void runWater(String vehicle) {
    System.out.println(vehicle + " 在水中行....");
    }
//方法 2. //.. //.. //... 
}
```

### 接口隔离原则

**客户端不应该依赖它不需要的接口，即一个类对另一个类的依赖应该建立在最小的接口上**

```java
public class Segregation1 {
public static void main(String[] args) {
// TODO Auto-generated method stub
// 使用一把
A a = new A();
a.depend1(new B()); // A 类通过接口去依赖 B 类
a.depend2(new B());
a.depend3(new B());
C c = new C();
c.depend1(new D()); // C 类通过接口去依赖(使用)D 类
c.depend4(new D());
c.depend5(new D());
}
}
// 接口 1
interface Interface1 {
void operation1();
}
// 接口 2
interface Interface2 {
void operation2();
void operation3();
}
// 接口 3
interface Interface3 {
void operation4();
void operation5();
}
class B implements Interface1, Interface2 {
public void operation1() {
System.out.println("B 实现了 operation1");
}
public void operation2() {
System.out.println("B 实现了 operation2");
}
public void operation3() {
System.out.println("B 实现了 operation3");
}
}
class D implements Interface1, Interface3 {
public void operation1() {
System.out.println("D 实现了 operation1");
}
public void operation4() {
System.out.println("D 实现了 operation4");
}
public void operation5() {
System.out.println("D 实现了 operation5");
}
}
class A { // A 类通过接口 Interface1,Interface2 依赖(使用) B 类，但是只会用到 1,2,3 方法
public void depend1(Interface1 i) {
i.operation1();
}
public void depend2(Interface2 i) {
i.operation2();
}
public void depend3(Interface2 i) {
i.operation3();
}
}
class C { // C 类通过接口 Interface1,Interface3 依赖(使用) D 类，但是只会用到 1,4,5 方法
public void depend1(Interface1 i) {
i.operation1();
}
public void depend4(Interface3 i) {
i.operation4();
}
public void depend5(Interface3 i) {
i.operation5();
}
}
```

### 依赖倒转(倒置)原则

高层模块不应该依赖低层模块，二者都应该依赖其抽象 

抽象不应该依赖细节，细节应该依赖抽象

依赖倒转(倒置)的中心思想是面向接口编程

依赖倒转原则是基于这样的设计理念：相对于细节的多变性，抽象的东西要稳定的多。以抽象为基础搭建的架 构比以细节为基础的架构要稳定的多。在 java 中，抽象指的是接口或抽象类，细节就是具体的实现类 

使用接口或抽象类的目的是制定好规范，而不涉及任何具体的操作，把展现细节的任务交给他们的实现类去完成

```java
public class DependecyInversion {
public static void main(String[] args) {
    //客户端无需改变
    Person person = new Person();
    person.receive(new Email());
    person.receive(new WeiXin());
    }
}
//定义接口，定义规范，实现依赖转置原则
interface IReceiver {
    public String getInfo();
}    
class Email implements IReceiver {
    public String getInfo() {
    return "电子邮件信息: hello,world";
    }
}
//增加微信
class WeiXin implements IReceiver {
    public String getInfo() {
    return "微信信息: hello,ok";
    }
}
//方式 2
class Person {
    //这里我们是对接口的依赖
    public void receive(IReceiver receiver ) {
    System.out.println(receiver.getInfo());
    }
}
```

#### 接口传递

```java
public class DependencyPass {
public static void main(String[] args) {
    ChangHong changHong = new ChangHong();
    OpenAndClose openAndClose = new OpenAndClose();
     openAndClose.open(changHong);
    }
}
 interface IOpenAndClose {
     public void open(ITV tv); //抽象方法,接收接口
 }

 interface ITV { //ITV 接口
     public void play();
 }

 class ChangHong implements ITV {
     @Override
     public void play() {
     // TODO Auto-generated method stub
     System.out.println("长虹电视机，打开");
 }

 }
// 实现接口
 class OpenAndClose implements IOpenAndClose{
     public void open(ITV tv){
         tv.play();
     }
 }
```

#### 构造方法传递

```java
public class DependencyPass {
public static void main(String[] args) {
    //通过构造器进行依赖传递
     ChangHong changHong = new ChangHong();
   OpenAndClose openAndClose = new OpenAndClose(changHong);
    openAndClose.open();
    }
}
 interface IOpenAndClose {
     public void open(ITV tv); //抽象方法,接收接口
 }

 interface ITV { //ITV 接口
     public void play();
 }
 class OpenAndClose implements IOpenAndClose{
     public ITV tv; //成员
     public OpenAndClose(ITV tv){ //构造器
     this.tv = tv;
 }
 public void open(){
     this.tv.play();
     }
 }
```

#### setter 方式传递

```java
public class DependencyPass {
public static void main(String[] args) {
    //通过 setter 方法进行依赖传递
     ChangHong changHong = new ChangHong();
    OpenAndClose openAndClose = new OpenAndClose();
    openAndClose.setTv(changHong);
    openAndClose.open();
    }
}
interface IOpenAndClose {
    public void open(); // 抽象方法
    public void setTv(ITV tv);
}
interface ITV { // ITV 接口
    public void play();
}
class OpenAndClose implements IOpenAndClose {
    private ITV tv;
    public void setTv(ITV tv) {
    this.tv = tv;
}
public void open() {
    this.tv.play();
    }
}
class ChangHong implements ITV {
    @Override
    public void play() {
    // TODO Auto-generated method stub
    System.out.println("长虹电视机，打开");
    }
}
```

#### 总结

低层模块尽量都要有抽象类或接口，或者两者都有，程序稳定性更好. 

变量的声明类型尽量是**抽象类或接口**, 这样我们的变量引用和实际对象间，就存在一个**缓冲层**，利于程序扩展 和优化 

继承时遵循**里氏替换原则**

### 里氏替换原则

如果对每个类型为 T1 的对象 o1，都有类型为 T2 的对象 o2，使得以 T1 定义的所有程序 P 在所有的对象 o1 都 代换成 o2 时，程序 P 的行为没有发生变化，那么类型 T2 是类型 T1 的子类型。换句话说，所有引用基类的地 方必须能透明地使用其子类的对象。 

在使用继承时，遵循里氏替换原则，**在子类中尽量不要重写父类的方法** 

里氏替换原则告诉我们，继承实际上让两个类耦合性增强了，在适当的情况下，可以通过**聚合，组合，依赖来**解决问题。

```java
//创建一个更加基础的基类
class Base {
    //把更加基础的方法和成员写到 Base 类
}
// A 类
class A extends Base {
    // 返回两个数的差
    public int func1(int num1, int num2) {
        return num1 - num2;
    }
}

class B extends Base {
    //如果 B 需要使用 A 类的方法,使用组合关系
    private A a = new A();
    //这里，重写了 A 类的方法, 可能是无意识
    public int func1(int a, int b) {
        return a + b;
    }
    public int func2(int a, int b) {
        return func1(a, b) + 9;
    }
    //我们仍然想使用 A 的方法
    public int func3(int a, int b) {
        return this.a.func1(a, b);
    }
}
```

### 开闭原则

开闭原则（Open Closed Principle）是编程中**最基础、最重要**的设计原则

一个软件实体如类，模块和函数应该**对扩展开放(对提供方)**，**对修改关闭(对使用方)**。用抽象构建框架，用实现扩展细节。 

当软件需要变化时，尽量**通过扩展软件**实体的行为来实现变化，而不是通过**修改已有的代码**来实现变化。

编程中遵循其它原则，以及使用设计模式的目的就是遵循开闭原则。

```java
public class Ocp{
public static void main(String[] args) {
    GraphicEditor graphicEditor = new GraphicEditor();
    graphicEditor.drawShape(new Rectangle());
    graphicEditor.drawShape(new Circle());
    graphicEditor.drawShape(new Triangle());
    //使用方新增扩展
    graphicEditor.drawShape(new OtherGraphic());
    }
}
//这是一个用于绘图的类 [使用方]
class GraphicEditor {
//接收 Shape 对象，调用 draw 方法
    public void drawShape(Shape s) {
    s.draw();
    }
}
//Shape 类，基类
abstract class Shape {
    int m_type;
    public abstract void draw();//抽象方法
}
class Rectangle extends Shape {
    Rectangle() {
    super.m_type = 1;
    }
    @Override
    public void draw() {
    // TODO Auto-generated method stub
    System.out.println(" 绘制矩形 ");
    }
}
class Circle extends Shape {
    Circle() {
    super.m_type = 2;
    }
    @Override
    public void draw() {
    // TODO Auto-generated method stub
    System.out.println(" 绘制圆形 ");
    }
}
//新增画三角形
class Triangle extends Shape {
    Triangle() {
    super.m_type = 3;
    }
    @Override
    public void draw() {
    // TODO Auto-generated method stub
    System.out.println(" 绘制三角形 ");
    }
}
//新增一个图形
class OtherGraphic extends Shape {
    OtherGraphic() {
    super.m_type = 4;
    }
    @Override
    public void draw() {
    // TODO Auto-generated method stub
    System.out.println(" 绘制其它图形 ");
    }
}
```

### 迪米特法则

一个对象应该对其他对象保持最少的了解 

类与类关系越密切，耦合度越大 

迪米特法则(Demeter Principle)又叫**最少知道原则**，即一个类对自己依赖的类知道的越少越好。也就是说，对于 被依赖的类不管多么复杂，**都尽量将逻辑封装在类的内部**。对外除了提供的 public 方法，不对外泄露任何信息 

迪米特法则还有个更简单的定义：**只与直接的朋友通信** 

**直接的朋友**：每个对象都会与其他对象有耦合关系，只要两个对象之间有耦合关系，我们就说这两个对象之间 是朋友关系。耦合的方式很多，依赖，关联，组合，聚合等。其中，我们称出现成员变量，方法参数，方法返 回值中的类为直接的朋友，而出现在局部变量中的类不是直接的朋友。也就是说，陌生的类最好不要以局部变 量的形式出现在类的内部。

```java
//客户端
public class Demeter1 {
public static void main(String[] args) {
    System.out.println("~~~使用迪米特法则的改进~~~");
    //创建了一个 SchoolManager 对象
    SchoolManager schoolManager = new SchoolManager();
    //输出学院的员工 id 和 学校总部的员工信息
    schoolManager.printAllEmployee(new CollegeManager());
    }
}
//学校总部员工类
class Employee {
    private String id;
    public void setId(String id) {
    this.id = id;
    }
    public String getId() {
    return id;
    }
}
//学院的员工类
class CollegeEmployee {
    private String id;
    public void setId(String id) {
    this.id = id;
    }
    public String getId() {
    return id;
    }
}
//管理学院员工的管理类
class CollegeManager {
    //返回学院的所有员工
    public List<CollegeEmployee> getAllEmployee() {
    List<CollegeEmployee> list = new ArrayList<CollegeEmployee>();
    for (int i = 0; i < 10; i++) { //这里我们增加了 10 个员工到 list
        CollegeEmployee emp = new CollegeEmployee();
        emp.setId("学院员工 id= " + i);
        list.add(emp);
        }
    return list;
    }
    //输出学院员工的信息
    public void printEmployee() {
    //获取到学院员工
    List<CollegeEmployee> list1 = getAllEmployee();
        System.out.println("------------学院员工------------");
        for (CollegeEmployee e : list1) {
        System.out.println(e.getId());
        }
    }
}
//学校管理类
//分析 SchoolManager 类的直接朋友类有哪些 Employee、CollegeManager
//CollegeEmployee 不是 直接朋友 而是一个陌生类，这样违背了 迪米特法则
class SchoolManager {
//返回学校总部的员工
    public List<Employee> getAllEmployee() {
    List<Employee> list = new ArrayList<Employee>();
    for (int i = 0; i < 5; i++) { //这里我们增加了 5 个员工到 list
        Employee emp = new Employee();
        emp.setId("学校总部员工 id= " + i);
        list.add(emp);
        }
        return list;
    }
    //该方法完成输出学校总部和学院员工信息(id)
    void printAllEmployee(CollegeManager sub) {
        //分析问题
        //迪米特原则的应用
        //1. 将输出学院的员工方法，封装到 CollegeManager
        sub.printEmployee();
        //获取到学校总部员工
        List<Employee> list2 = this.getAllEmployee();
        System.out.println("------------学校总部员工------------");
        for (Employee e : list2) {
        System.out.println(e.getId());
        }
    }
}
```

#### 总结

迪米特法则的核心是降低类之间的耦合 

 但是注意：由于每个类都减少了不必要的依赖，因此迪米特法则只是**要求降低类间(对象间)耦合关系**， 并不是要求完全没有依赖关系

### 合成复用原则

 找出应用中可能需要变化之处，把它们独立出来，不要和那些不需要变化的代码混在一起。

针对接口编程，而不是针对实现编程。

为了交互对象之间的松耦合设计而努力

## 类的关系

### 依赖关系（Dependence）

只要是在类中用到了对方，那么他们之间就存在依赖关系。如果没有对方，连编绎都通过不了。

**类中用到了对方** 

**如果是类的成员属性**

**如果是方法的返回类型**

**是方法接收的参数类型** 

**方法中使用到**

```java
public class PersonServiceBean {
    private PersonDao personDao;//类
    public void save(Person person){}
    public IDCard getIDCard(Integer personid){}
    public void modify(){
    Department department = new Department();
    }
}
public class PersonDao{}
public class IDCard{}
public class Person{}
public class
```

### 泛化关系(generalization）

泛化关系实际上就是继承关系，它是**依赖关系**的特例

 如果 A 类继承了 B 类，我们就说 A 和 B 存在泛化关系

```java
public abstract class DaoSupport{
    public void save(Object entity){
    }
    public void delete(Object id){
    }
}
public class PersonServiceBean extends Daosupport{
}
```

### 实现关系（Implementation）

实现关系实际上就是 A 类实现 B 接口，他是依赖关系的特例 

```java
public interface PersonService { 
    public void delete(Interger id); 
} 
public class PersonServiceBean implements PersonService { 
    public void delete(Interger id){} 
}
```

### 关联关系（Association）

关联关系是依赖关系的特例，所 以它具有**导航性与多重性。**

```java
//一对一
public class Person{
    private Card card;
}
public class Card{
}
//多对多
public class Person{
    private Card card;
}
public class Card{
    private Person person;
}
```

### 聚合关系（Aggregation)

聚合关系（Aggregation）表示的是**整体和部分的关系，整体与部分可以分开**。聚合关系是关联关系的特例，所 以他具有关联的**导航性与多重性。**

```java
//一台电脑由键盘(keyboard)、显示器(monitor)，鼠标等组成；组成电脑的各
//个配件是可以从电脑上分离出来的，使用带空心菱形的实线来表示：
public class Computer{
    private Monitor mouse;
    private Keyboard keyboard;
    public void setMouse(Mouse mouse) {
        this.mouse = mouse;
    }
    public void setKeyboard(Keyboard keyboard) {
        this.Keyboard = Keyboard;
    }
}
public class Keyboard {
}
public class Moniter {
}
```

### 组合关系（Composition）

组合关系：也是整体与部分的关系，但是**整体与部分不可以分开**。

```java
public class Computer {
    private Mouse mouse = new Keyboard(); //鼠标可以和 computer 不能分离
    private Moniter moniter = new Moniter();//显示器可以和 Computer 不能分离
}
public class Keyboard {
}
public class Moniter {
}
```

## 设计模式

### 创建型模式

#### 单例模式

所谓类的单例设计模式，就是**采取一定的方法保证在整个的软件系统中，对某个类只能存在一个对象实例**， 并且该类只提供一个取得其对象实例的方法(静态方法)

##### 饿汉式(静态常量)

```java
class Singleton {
//1. 构造器私有化, 外部能 new
    private Singleton() {
    }
    //2.本类内部创建对象实例
    private final static Singleton instance = new Singleton();
    //3. 提供一个公有的静态方法，返回实例对象
    public static Singleton getInstance() {
        return instance;
    }
}
```

###### 优缺点说明

优点：这种写法比较简单，就是在类装载的时候就完成实例化。避免了线程同步问题。 

缺点：在类装载的时候就完成实例化，没有达到 Lazy Loading 的效果。如果从始至终从未使用过这个实例，则 会造成内存的浪费 

这种方式基于 classloder 机制避免了多线程的同步问题，不过，instance 在类装载时就实例化，在单例模式中大多数都是调用 getInstance 方法，但是导致类装载的原因有很多种，因此不能确定有其他的方式（或者其他的静 态方法）导致类装载，这时候初始化 instance 就没有达到 lazy loading 的效果 

结论：**这种单例模式可用，可能造成内存浪费**

##### 饿汉式（静态代码块）

```java
class Singleton {
    //1. 构造器私有化, 外部能 new
    private Singleton() {
    }
    //2.本类内部创建对象实例
    private static Singleton instance;
    static { // 在静态代码块中，创建单例对象
        instance = new Singleton();
    }
    //3. 提供一个公有的静态方法，返回实例对象
    public static Singleton getInstance() {
        return instance;
    }
}
```

###### 优缺点说明

和上述相同，都是在client初始化。

##### 懒汉式(线程不安全)

```java
class Singleton {
    private static Singleton instance;
    private Singleton() {}
    //提供一个静态的公有方法，当使用到该方法时，才去创建 instance
    //即懒汉式
    public static Singleton getInstance() {
    if(instance == null) {
        instance = new Singleton();
    }
    return instance;
}
}
```

###### 优缺点说明：

起到了 Lazy Loading 的效果，但是只能**在单线程下**使用。 

如果在多线程下，一个线程进入了 if (singleton == null)判断语句块，还未来得及往下执行，另一个线程也通过 了这个判断语句，这时便会产生多个实例。所以在多线程环境下不可使用这种方式 

结论：在实际开发中，**不要使用这种方式.**

##### 懒汉式(线程安全，同步方法)

```java
// 懒汉式(线程安全，同步方法)
class Singleton {
    private static Singleton instance;
    private Singleton() {}
    //提供一个静态的公有方法，加入同步处理的代码，解决线程安全问题
    //即懒汉式
    public static synchronized Singleton getInstance() {
        if(instance == null) {
        instance = new Singleton();
        }
    return instance;
    }
}
```

###### 优缺点说明

解决了线程安全问题 

效率太低了，每个线程在想获得类的实例时候，执行 getInstance()方法都要进行同步。而其实这个方法只执行 一次实例化代码就够了，后面的想获得该类实例，直接 return 就行了。方法进行**同步效率太低** 

结论：在实际开发中，**不推荐使用这种方式**

##### 懒汉式(线程不安全，同步代码块)

```java
// 懒汉式(线程安全，同步方法)
class Singleton {
    private static Singleton instance;
    private Singleton() {}
    //提供一个静态的公有方法，加入同步处理的代码，解决线程安全问题
    //即懒汉式
    public static  Singleton getInstance() {
        if(instance == null) {
        synchronized(Singleton.class){
        instance = new Singleton();
        }
    return instance;
        }
    }
}
```

###### 优缺点说明

没有解决线程安全的问题，**不推荐使用**

##### 双重检查

```
// 懒汉式(线程安全，同步方法)
class Singleton {
    //volatile禁止指令重排
    private static volatile Singleton instance;
    private Singleton() {}
    //提供一个静态的公有方法，加入双重检查代码，解决线程安全问题, 同时解决懒加载问题
    //同时保证了效率, 推荐使用
    public static synchronized Singleton getInstance() {
    if(instance == null) {
        synchronized (Singleton.class) {
        if(instance == null) {
        instance = new Singleton();
        }
        }
    }
    return instance;
    }
}
```

###### 优缺点说明

Double-Check 概念是多线程开发中常使用到的，如代码中所示，我们进行了**两次** if (singleton == null)检查，这 样就可以保证线程安全了。

这样，实例化代码只用执行一次，后面再次访问时，判断 if (singleton == null)，直接 return 实例化对象，也避免的反复进行方法同步. 

线程安全；延迟加载；**效率较高**

结论：在实际开发中，推荐使用这种**单例设计模式**

##### 静态内部类

```java
// 静态内部类完成， 推荐使用
class Singleton {
    private static Singleton instance;
    //构造器私有化
    private Singleton() {}
    //写一个静态内部类,该类中有一个静态属性 Singleton
        private static class SingletonInstance {
        private static final Singleton INSTANCE = new Singleton();
    }
//提供一个静态的公有方法，直接返回 SingletonInstance.INSTANCE
    public static synchronized Singleton getInstance() {
        return SingletonInstance.INSTANCE;
        }
}
```

###### 优缺点说明

 这种方式采用了**类装载的机制来保证初始化实例时只有一个线程**。

静态内部类方式在 Singleton 类被装载时并不会立即实例化，而是在需要实例化时，调用 getInstance 方法，才 会装载 SingletonInstance 类，从而完成 Singleton 的实实例化。

类的静态属性只会在第一次加载类的时候初始化，所以在这里，JVM 帮助我们保证了线程的安全性，在类进行 初始化时，别的线程是无法进入的。

优点：**避免了线程不安全，利用静态内部类特点实现延迟加载，效率高** 

结论：**推荐使用.**

##### 枚举

```java
public class SingletonTest08 {
    public static void main(String[] args) {
        Singleton instance = Singleton.INSTANCE;
        Singleton instance2 = Singleton.INSTANCE;
        System.out.println(instance == instance2);
        System.out.println(instance.hashCode());
        System.out.println(instance2.hashCode());
        instance.sayOK();
    }
}
//使用枚举，可以实现单例, 推荐
enum Singleton {
    INSTANCE; //属性
    public void sayOK() {
    System.out.println("ok~");
    }
}
```

###### 优缺点说明

这借助 JDK1.5 中添加的枚举来实现单例模式。不仅能避免多线程同步问题，而且还能防止反序列化重新创建 新的对象。

这种方式是 Effective Java 作者 Josh Bloch 提倡的方式 

结论：**推荐使用**

##### 单例模式注意事项和细节说明

单例模式保证了 系统内存中该类只存在一个对象，节省了系统资源，对于一些需要频繁创建销毁的对象，使 用单例模式可以提高系统性能

当想实例化一个单例类的时候，必须要记住使用相应的获取对象的方法，而不是使用 new

单例模式使用的场景：**需要频繁的进行创建和销毁的对象**、**创建对象时耗时过多或耗费资源过多**(即：重量级 对象)，但又经常用到的对象、工具类对象、频繁访问数据库或文件的对象(比如数据源、session 工厂等)

#### 原型模式

##### 示例

```java
/*
现在有一只羊 tom，姓名为: tom, 年龄为：1，颜色为：白色，请编写程序创建和 tom 羊 属性完全相同的 10只羊。
1) 原型模式(Prototype 模式)是指：用原型实例指定创建对象的种类，并且通过拷贝这些原型，创建新的对象
2) 原型模式是一种创建型设计模式，允许一个对象再创建另外一个可定制的对象，无需知道如何创建的细节
3) 工作原理是:通过将一个原型对象传给那个要发动创建的对象，这个要发动创建的对象通过请求原型对象拷贝它们自己来实施创建，即 对象.clone()
*/

public class Sheep implements Cloneable {
private String name;
private int age;
private String color;
private String address = "蒙古羊";
public Sheep friend; //是对象, 克隆是会如何处理, 默认是浅拷贝
public Sheep(String name, int age, String color) {
        super();
        this.name = name;
        this.age = age;
        this.color = color;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age){
        this.age = age;
    }
    public String getColor() {
        return color;
    }
    public void setColor(String color) {
        this.color = color;
    }
    @Override
    public String toString() {
        return "Sheep [name=" + name + ", age=" + age + ", color=" + color + ", address=" + address + "]";
    }
    //克隆该实例，使用默认的 clone 方法来完成
    @Override
    protected Object clone() {
        Sheep sheep = null;
        try {
            sheep = (Sheep)super.clone();
        } catch (Exception e) {
        // TODO: handle exception
            System.out.println(e.getMessage());
        }
        // TODO Auto-generated method stub
    return sheep;
    }
}
public class Client {
    public static void main(String[] args) {
        System.out.println("原型模式完成对象的创建");
        // TODO Auto-generated method stub
        Sheep sheep = new Sheep("tom", 1, "白色");
        sheep.friend = new Sheep("jack", 2, "黑色");
        Sheep sheep2 = (Sheep)sheep.clone(); //克隆
        Sheep sheep3 = (Sheep)sheep.clone(); //克隆
        Sheep sheep4 = (Sheep)sheep.clone(); //克隆
        Sheep sheep5 = (Sheep)sheep.clone(); //克隆
        System.out.println("sheep2 =" + sheep2 + "sheep2.friend=" + sheep2.friend.hashCode())
        System.out.println("sheep3 =" + sheep3 + "sheep3.friend=" + sheep3.friend.hashCode());
        System.out.println("sheep4 =" + sheep4 + "sheep4.friend=" + sheep4.friend.hashCode());
        System.out.println("sheep5 =" + sheep5 + "sheep5.friend=" + sheep5.friend.hashCode());
        }
}
```

##### 实现引用类型深拷贝

```java
/*
1）对于数据类型是基本数据类型的成员变量，浅拷贝会直接进行值传递，也就是将该属性值复制一份给新的对象。
2) 对于数据类型是引用数据类型的成员变量，比如说成员变量是某个数组、某个类的对象等，那么浅拷贝会进行引用传递，也就是只是将该成员变量的引用值（内存地址）复制一份给新的对象。因为实际上两个对象的该成员变量都指向同一个实例。在这种情况下，在一个对象中修改该成员变量会影响到另一个对象的该成员变量值
*/

public class DeepProtoType implements Serializable, Cloneable{
    public String name; //String 属性
    public DeepCloneableTarget deepCloneableTarget;// 引用类型
    public DeepProtoType() {
        super();
    }
    //深拷贝 - 方式 1 使用 clone 方法
    @Override
    protected Object clone() throws CloneNotSupportedException {
        Object deep = null;
        //这里完成对基本数据类型(属性)和 String 的克隆
        deep = super.clone();
        //对引用类型的属性，进行单独处理
        DeepProtoType deepProtoType = (DeepProtoType)deep;
        deepProtoType.deepCloneableTarget = (DeepCloneableTarget)deepCloneableTarget.clone();
        // TODO Auto-generated method stub
        return deepProtoType;
    }
    //深拷贝 - 方式 2 通过对象的序列化实现 (推荐)
    public Object deepClone() {
        //创建流对象
        ByteArrayOutputStream bos = null;
        ObjectOutputStream oos = null;
        ByteArrayInputStream bis = null;
        ObjectInputStream ois = null;
        try {
            //序列化
            bos = new ByteArrayOutputStream();
            oos = new ObjectOutputStream(bos);
            oos.writeObject(this); //当前这个对象以对象流的方式输出
            //反序列化
            bis = new ByteArrayInputStream(bos.toByteArray());
            ois = new ObjectInputStream(bis);
            DeepProtoType copyObj = (DeepProtoType)ois.readObject();
            return copyObj
            } catch (Exception e) {
        // TODO: handle exception
            e.printStackTrace();
            return null;
        } finally {
        //关闭流
        try {
            bos.close();
            oos.close();
            bis.close();
            ois.close();
        } catch (Exception e2) {
            // TODO: handle exception
            System.out.println(e2.getMessage());
            }
        }
    }
}
public class Client {
public static void main(String[] args) throws Exception {
    // TODO Auto-generated method stub
    DeepProtoType p = new DeepProtoType();
    p.name = "宋江";
    p.deepCloneableTarget = new DeepCloneableTarget("大牛", "小牛");
    //方式 1 完成深拷贝
    // DeepProtoType p2 = (DeepProtoType) p.clone();
    //
    // System.out.println("p.name=" + p.name + "p.deepCloneableTarget=" + p.deepCloneableTarget.hashCode());
    // System.out.println("p2.name=" + p.name + "p2.deepCloneableTarget=" + p2.deepCloneableTarget.hashCode());
    //方式 2 完成深拷贝
    DeepProtoType p2 = (DeepProtoType) p.deepClone();
    System.out.println("p.name=" + p.name + "p.deepCloneableTarget=" + p.deepCloneableTarget.hashCode());
    System.out.println("p2.name=" + p.name + "p2.deepCloneableTarget=" + p2.deepCloneableTarget.hashCode());
    }
}
```

##### 小结

创建新的对象比较复杂时，可以利用原型模式**简化对象的创建过程，同时也能够提高效率** 

不用重新初始化对象，而是动态地**获得对象运行时**的状态

如果原始对象发生变化(增加或者减少属性)，其它克隆对象的也会发生相应的变化，无需修改代码  

在实现深克隆的时候可能需要比较复杂的代码

缺点：需要为每一个类配备一个克隆方法，这对全新的类来说不是很难，但对**已有的类进行改造时，需要修改其源代码**，违背了 ocp 原则，这点请同学们注意

#### 建造者模式（生成者模式）

##### 示例

```java
/*
1) 需要建房子：这一过程为打桩、砌墙、封顶
2) 房子有各种各样的，比如普通房，高楼，别墅，各种房子的过程虽然一样，但是要求不要相同的. 
3) 请编写程序，完成需求
*/
/*
建造者模式（Builder Pattern） 又叫生成器模式，是一种对象构建模式。它可以将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象。
建造者模式 是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。
*/
/*
建造者模式的四个角色
    1) Product（产品角色）： 一个具体的产品对象。
    2) Builder（抽象建造者）： 创建一个 Product 对象的各个部件指定的 接口/抽象类。
    3) ConcreteBuilder（具体建造者）： 实现接口，构建和装配各个部件。
    4) Director（指挥者）： 构建一个使用 Builder 接口的对象。它主要是用于创建一个复杂的对象。它主要有两个作用，一是：隔离了客户与对象的生产过程，二是：负责控制产品对象的生产过程。
*/
//客户端
public class Client {
public static void main(String[] args) {
    //盖普通房子
    CommonHouse commonHouse = new CommonHouse();
    //准备创建房子的指挥者
    HouseDirector houseDirector = new HouseDirector(commonHouse);
    //完成盖房子，返回产品(普通房子)
    House house = houseDirector.constructHouse();
    //System.out.println("输出流程");
    System.out.println("--------------------------");
    //盖高楼
    HighBuilding highBuilding = new HighBuilding();
    //重置建造者
    houseDirector.setHouseBuilder(highBuilding);
    //完成盖房子，返回产品(高楼)
    houseDirector.constructHouse();}
}
//建造者
public class CommonHouse extends HouseBuilder {
    @Override
    public void buildBasic() {
        // TODO Auto-generated method stub
        System.out.println(" 普通房子打地基 5 米 ");
    }
    @Override
    public void buildWalls() {
        // TODO Auto-generated method stub
        System.out.println(" 普通房子砌墙 10cm ");
    }
    @Override
    public void roofed() {
        // TODO Auto-generated method stub
        System.out.println(" 普通房子屋顶 ");
    }
}
public class HighBuilding extends HouseBuilder {
    @Override
    public void buildBasic() {
        // TODO Auto-generated method stub
        System.out.println(" 高楼的打地基 100 米 ");
    }
    @Override
    public void buildWalls() {
        // TODO Auto-generated method stub
        System.out.println(" 高楼的砌墙 20cm ");
    }
    @Override
    public void roofed() {
        // TODO Auto-generated method stub
        System.out.println(" 高楼的透明屋顶 ");
    }
}
//产品类
@Data
public class House {
    private String baise;
    private String wall;
    private String roofed;
    }
// 抽象的建造者
public abstract class HouseBuilder {
    protected House house = new House();
    //将建造的流程写好, 抽象的方法
    public abstract void buildBasic();
    public abstract void buildWalls();
    public abstract void roofed();
    //建造房子好， 将产品(房子) 返回
    public House buildHouse() {
    return house;
    }
}
//指挥者，这里去指定制作流程，返回产品
public class HouseDirector {
    HouseBuilder houseBuilder = null;
    //构造器传入 houseBuilder
    public HouseDirector(HouseBuilder houseBuilder) {
        this.houseBuilder = houseBuilder;
    }
    //通过 setter 传入 houseBuilder
    public void setHouseBuilder(HouseBuilder houseBuilder) {
        this.houseBuilder = houseBuilder;
    }
    //如何处理建造房子的流程，交给指挥者
    public House constructHouse() {
        houseBuilder.buildBasic();
        houseBuilder.buildWalls();
        houseBuilder.roofed();
        return houseBuilder.buildHouse();
    }
}
```

##### 小结

客户端(使用程序)不必知道产品内部组成的细节，将产品本身与产品的创建过程解耦，使得相同的创建过程可 以创建不同的产品对象

每一个具体建造者都相对独立，而与其他的具体建造者无关，因此可以很方便地替换具体建造者或增加新的具 体建造者， 用户使用不同的具体建造者即可得到不同的产品对象

 可以更加精细地控制产品的创建过程 。将复杂产品的创建步骤分解在不同的方法中，使得创建过程更加清晰， 也更方便使用程序来控制创建过程  

增加新的具体建造者无须修改原有类库的代码，指挥者类针对抽象建造者类编程，系统扩展方便，符合“开闭 原则” 

建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，如果产品之间的差异性很大，则不适合使 用建造者模式，因此其使用范围受到一定的限制。 

如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大，因 此在这种情况下，要考虑是否选择建造者模式. 

抽象工厂模式 VS 建造者模式 抽象工厂模式实现对产品家族的创建，一个产品家族是这样的一系列产品：具有不同分类维度的产品组合，采 用抽象工厂模式不需要关心构建过程，只关心什么产品由什么工厂生产即可。而建造者模式则是要求按照指定 的蓝图建造产品，它的主要目的是通过组装零配件而产生一个新产品

#### 工厂模式

##### 简单工厂模式

###### 示例

```java
/*
看一个披萨的项目：要便于披萨种类的扩展，要便于维护
1) 披萨的种类很多(比如 GreekPizz、CheesePizz 等)
2) 披萨的制作有 prepare，bake, cut, box
3) 完成披萨店订购功能
*/
public  class OrderPizza{
 构造器
     public OrderPizza() {
         Pizza pizza = null;
         String orderType; // 订购披萨的类型
         do {
             orderType = getType();
             if (orderType.equals("greek")) {
                 pizza = new GreekPizza();
                 pizza.setName(" 希腊披萨 ");
             } else if (orderType.equals("cheese")) {
                 pizza = new CheesePizza();
                 pizza.setName(" 奶酪披萨 ");
             } else if (orderType.equals("pepper")) {
                 pizza = new PepperPizza();
                 pizza.setName("胡椒披萨");
             } else {
             break;
             }
         //输出 pizza 制作过程
         pizza.prepare();
         pizza.bake();
         pizza.cut();
         pizza.box();

     } while (true);
}
/*
    1) 优点是比较好理解，简单易操作。
    2) 缺点是违反了设计模式的 ocp 原则，即对扩展开放，对修改关闭。即当我们给类增加新功能的时候，尽量不修改代码，或者尽可能少修改         代码. 
    3)比如我们这时要新增加一个 Pizza 的种类(Pepper 披萨)，我们需要将整个构造函数进行需改
*/
```

###### 简单工厂优化

```java
/*
    简单工厂模式是属于创建型模式，是工厂模式的一种。简单工厂模式是由一个工厂对象决定创建出哪一种产品类的实例。简单工厂模式是工厂模式家族中最简单实用的模式
    简单工厂模式：定义了一个创建对象的类，由这个类来封装实例化对象的行为(代码)
    在软件开发中，当我们会用到大量的创建某种、某类或者某批对象时，就会使用到工厂模式.
*/
//简单工厂类
public class SimpleFactory {
    //更加 orderType 返回对应的 Pizza 对象
    public Pizza createPizza(String orderType) {
        Pizza pizza = null;
        System.out.println("使用简单工厂模式");
        if (orderType.equals("greek")) {
            pizza = new GreekPizza();
            pizza.setName(" 希腊披萨 ");
        } else if (orderType.equals("cheese")) {
            pizza = new CheesePizza();
            pizza.setName(" 奶酪披萨 ");
        } else if (orderType.equals("pepper")) {
            pizza = new PepperPizza();
            pizza.setName("胡椒披萨");
    }
    return pizza;
}
//简单工厂模式 也叫 静态工厂模式，静态优化
public static Pizza createPizza2(String orderType) {
    Pizza pizza = null;
    System.out.println("使用简单工厂模式 2");
    if (orderType.equals("greek")) {
        pizza = new GreekPizza();
        pizza.setName(" 希腊披萨 ");
    } else if (orderType.equals("cheese")) {
        pizza = new CheesePizza();
        pizza.setName(" 奶酪披萨 ");
    } else if (orderType.equals("pepper")) {
        pizza = new PepperPizza();
        pizza.setName("胡椒披萨");
    }
    return pizza;
    }
}
public  class OrderPizza{
    //定义一个简单工厂对象
    SimpleFactory simpleFactory;
    Pizza pizza = null;
    //构造器
    //public OrderPizza(SimpleFactory simpleFactory) {
    //    setFactory(simpleFactory);
   // }
   // public void setFactory(SimpleFactory simpleFactory) {
   //     String orderType = ""; //用户输入的
   //     this.simpleFactory = simpleFactory; //设置简单工厂对象
    //    do {
    //        orderType = getType();
    //        pizza = this.simpleFactory.createPizza(orderType);
        //输出 pizza
     //   if(pizza != null) { //订购成功
     //       pizza.prepare();
     //       pizza.bake();
      //      pizza.cut();
     //       pizza.box();
      //  } else {
     //       System.out.println(" 订购披萨失败 ");
     //      break;
      //  }
    //}while(true);
    //静态工厂
      public void setFactory2( ) {
        String orderType = ""; //用户输入的
        do {
            orderType = getType();
            pizza = this.simpleFactory.createPizza2(orderType);
        //输出 pizza
        if(pizza != null) { //订购成功
            pizza.prepare();
            pizza.bake();
            pizza.cut();
            pizza.box();
        } else {
            System.out.println(" 订购披萨失败 ");
            break;
        }
    }while(true);   

 }


// 写一个方法，可以获取客户希望订购的披萨种类
private String getType() {
    try {
        BufferedReader strin = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("input pizza 种类:");
        String str = strin.readLine();
        return str;
    } catch (IOException e) {
        e.printStackTrace();
        return "";
        }
}
```

##### 工厂方法模式

###### 示例

```
/*
披萨项目新的需求：客户在点披萨时，可以点不同口味的披萨，比如 北京的奶酪 pizza、北京的胡椒 pizza 或
者是伦敦的奶酪 pizza、伦敦的胡椒 pizza。
*/
```

```java
/*
    1) 工厂方法模式设计方案：将披萨项目的实例化功能抽象成抽象方法，在不同的口味点餐子类中具体实现。
    2) 工厂方法模式：定义了一个创建对象的抽象方法，由子类决定要实例化的类。工厂方法模式将对象的实例化推迟到子类。
    3) 工厂类让子类自己工厂化
*/
public abstract class OrderPizza {
    //定义一个抽象方法，createPizza , 让各个工厂子类自己实现
    abstract Pizza createPizza(String orderType); 
}
public class BJOrderPizza extends OrderPizza {
    @Override
    Pizza createPizza(String orderType) {
        Pizza pizza = null;
        if(orderType.equals("cheese")) {
            pizza = new BJCheesePizza();
        } else if (orderType.equals("pepper")) {
            pizza = new BJPepperPizza();
        }
        // TODO Auto-generated method stub
        return pizza;
    }
}
public class LDOrderPizza extends OrderPizza {
    @Override
    Pizza createPizza(String orderType) {
        Pizza pizza = null;
        if(orderType.equals("cheese")) {
            pizza = new LDCheesePizza();
        } else if (orderType.equals("pepper")) {
            pizza = new LDPepperPizza();
        }
        // TODO Auto-generated method stub
        return pizza;
    }
}
public class pizzaStore(){
      public static void main(String[] args) {
        Pizza pizza = null;
          BufferedReader strin = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("input pizza 地域:");
         String str = strin.readLine();
         if(orderType.equals("bj")) {
          pizza = new BJOrderPizza();
        } else  (orderType.equals("ld")) {
            pizza = new LDOrderPizza();
        } 
          getPizza(pizza);
     private static void getPizza(OrderPizza pizza){
            String orderType; // 订购披萨的类型
        do {
            orderType = getType();
            pizza = pizza.createPizza(orderType); //抽象方法，由工厂子类完成
            //输出 pizza 制作过程
            pizza.prepare();
            pizza.bake();
            pizza.cut();
            pizza.box();
    } 
     }
    private String getType() {
        try {
            BufferedReader strin = new BufferedReader(new InputStreamReader(System.in));
                System.out.println("input pizza 种类:");
                String str = strin.readLine();
                return str;
            } catch (IOException e) {
                e.printStackTrace();
                return "";
                }
            }
    }
}
```

##### 抽象工厂模式

```java
/*
1) 抽象工厂模式：定义了一个 interface 用于创建相关或有依赖关系的对象簇，而无需指明具体的类
2) 抽象工厂模式可以将简单工厂模式和工厂方法模式进行整合。
3) 从设计层面看，抽象工厂模式就是对简单工厂模式的改进(或者称为进一步的抽象)。
4) 将工厂抽象成两层，AbsFactory(抽象工厂) 和 具体实现的工厂子类。程序员可以根据创建对象类型使用对应的工厂子类。这样将单个的简单工厂类变成了工厂簇，更利于代码的维护和扩展
*/
//使用抽象工厂模式来完成披萨项目
//一个抽象工厂模式的抽象层(接口)
public interface AbsFactory {
    //让下面的工厂子类来 具体实现
    public Pizza createPizza(String orderType);
}
//这是工厂子类
public class BJFactory implements AbsFactory {
    @Override
    public Pizza createPizza(String orderType) {
        System.out.println("~使用的是抽象工厂模式~");
        // TODO Auto-generated method stub
        Pizza pizza = null;
        if(orderType.equals("cheese")) {
            pizza = new BJCheesePizza();
        } else if (orderType.equals("pepper")){
            pizza = new BJPepperPizza();
        }
        return pizza;
    }
}    
public class LDFactory implements AbsFactory {
    @Override
    public Pizza createPizza(String orderType) {
        System.out.println("~使用的是抽象工厂模式~");
        Pizza pizza = null;
        if (orderType.equals("cheese")) {
            pizza = new LDCheesePizza();
        } else if (orderType.equals("pepper")) {
            pizza = new LDPepperPizza();
        }
        return pizza;
    }
}
public class OrderPizza {
    AbsFactory factory;
    // 构造器
    public OrderPizza(AbsFactory factory) {
        setFactory(factory);
    }
    private void setFactory(AbsFactory factory) {
        Pizza pizza = null;
        String orderType = ""; // 用户输入
        this.factory = factory;      
            orderType = getType();
            // factory 可能是北京的工厂子类，也可能是伦敦的工厂子类
            pizza = factory.createPizza(orderType);
            if (pizza != null) { // 订购 ok
                pizza.prepare();
                pizza.bake();
                pizza.cut();
                pizza.box();
            } else {
                System.out.println("订购失败");
                break;            
        } 
    }
    // 写一个方法，可以获取客户希望订购的披萨种类
    private String getType() {
        try {
            BufferedReader strin = new BufferedReader(new InputStreamReader(System.in));
            System.out.println("input pizza 种类:");
            String str = strin.readLine();
            return str;
        } catch (IOException e) {
            e.printStackTrace();
            return "";
        }
    }
}
//客户端
public class pizzaStore(){
      public static void main(String[] args) {
        Pizza pizza = null;
          BufferedReader strin = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("input pizza 地域:");
         String str = strin.readLine();
         if(orderType.equals("bj")) {
          pizza = new BJOrderPizza();
        } else  (orderType.equals("ld")) {
            pizza = new LDOrderPizza();
        } 
     OrderPizza orderPizza=new OrderPizza()
        orderPizza.setFactory(pizza);


}
```

##### 小结

```
1) 工厂模式的意义
将实例化对象的代码提取出来，放到一个类中统一管理和维护，达到和主项目的依赖关系的解耦。从而提高项目的扩展和维护性。
2) 三种工厂模式 (简单工厂模式、工厂方法模式、抽象工厂模式)
3) 设计模式的依赖抽象原则
 创建对象实例时，不要直接 new 类, 而是把这个 new 类的动作放在一个工厂的方法中，并返回。有的书上说，变量不要直接持有具体类的引用。
 不要让类继承具体类，而是继承抽象类或者是实现 interface(接口)
 不要覆盖基类中已经实现的方法。
```

### 结构型模式

#### 适配器模式

适配器模式(Adapter Pattern)将某个类的接口转换成客户端期望的另一个接口表示，主的目的是兼容性，让原本 因接口不匹配不能一起工作的两个类可以协同工作。其别名为**包装器(Wrapper)** 

适配器模式属于**结构型模式** 

主要分为三类：**类适配器模式、对象适配器模式、接口适配器模式**

##### 工作原理

 适配器模式：将一个类的接口转换成另一种接口.让原本接口不兼容的类可以兼容 

从用户的角度看不到被适配者，是**解耦**的

用户调用适配器转化出来的目标接口方法，适配器再调用被适配者的相关接口方法 

用户收到反馈结果，感觉只是和目标接口交互

##### 类适配器模式

###### 示例

```java
/*
以生活中充电器的例子来讲解适配器，充电器本身相当于 Adapter，220V 交流电相当于 src (即被适配者)，我们的目 dst(即 目标)是 5V 直流电
*/
public class Client {
public static void main(String[] args) {
    // TODO Auto-generated method stub
    System.out.println(" === 类适配器模式 ====");
    Phone phone = new Phone();
    phone.charging(new VoltageAdapter());
    }
}
//适配接口
public interface IVoltage5V {
    public int output5V();
}
public class Phone {
    //充电
public void charging(IVoltage5V iVoltage5V) {
    if(iVoltage5V.output5V() == 5) {
        System.out.println("电压为 5V, 可以充电~~");
    } else if (iVoltage5V.output5V() > 5) {
        System.out.println("电压大于 5V, 不能充电~~");
        }
    }
}
//被适配的类
public class Voltage220V {
    //输出 220V 的电压
    public int output220V() {
        int src = 220;
        System.out.println("电压=" + src + "伏");
        return src;
    }
}
public class VoltageAdapter extends Voltage220V implements IVoltage5V {
    @Override
    public int output5V() {
        // TODO Auto-generated method stub
        //获取到 220V 电压
        int srcV = output220V();
        int dstV = srcV / 44 ; //转成 5v
        return dstV;
    }
}
```

###### 小结

Java 是单继承机制，所以类适配器需要继承 src 类这一点算是一个缺点, 因为这要求 dst 必须是接口，有一定局限性; 

src 类的方法在 Adapter 中都会暴露出来，也增加了使用的成本。 

由于其继承了 src 类，所以它可以根据需求重写 src 类的方法，使得 Adapter 的灵活性增强了。

##### 对象适配器

###### 示例

```java
/*
1) 基本思路和类的适配器模式相同，只是将 Adapter 类作修改，不是继承 src 类，而是持有 src 类的实例，以解决
兼容性的问题。 即：持有 src 类，实现 dst 类接口，完成 src->dst 的适配
2) 根据“合成复用原则”，在系统中尽量使用关联关系（聚合）来替代继承关系。
3) 对象适配器模式是适配器模式常用的一种
*/
/*
以生活中充电器的例子来讲解适配器，充电器本身相当于 Adapter，220V 交流电相当于 src (即被适配者)，我们的目 dst(即 目标)是 5V 直流电
*/
public class Client {
public static void main(String[] args) {
    // TODO Auto-generated method stub
    System.out.println(" === 类适配器模式 ====");
    Phone phone = new Phone();
    phone.charging(new VoltageAdapter());
    }
}
//适配接口
public interface IVoltage5V {
    public int output5V();
}
public class Phone {
    //充电
public void charging(IVoltage5V iVoltage5V) {
    if(iVoltage5V.output5V() == 5) {
        System.out.println("电压为 5V, 可以充电~~");
    } else if (iVoltage5V.output5V() > 5) {
        System.out.println("电压大于 5V, 不能充电~~");
        }
    }
}
//被适配的类
public class Voltage220V {
    //输出 220V 的电压
    public int output220V() {
        int src = 220;
        System.out.println("电压=" + src + "伏");
        return src;
    }
}
public class VoltageAdapter  implements IVoltage5V {
    private Voltage220V voltage220V; // 关联关系-聚合
  //通过构造器，传入一个 Voltage220V 实例
    public VoltageAdapter(Voltage220V voltage220v) {
        this.voltage220V = voltage220v;
    }
    @Override
    public int output5V() {
        int dst = 0;
        if(null != voltage220V) {
            int src = voltage220V.output220V();//获取 220V 电压
            System.out.println("使用对象适配器，进行适配~~");
            dst = src / 44;
            System.out.println("适配完成，输出的电压为=" + dst);
        }
    return dst;
    }
}
```

###### 小结

对象适配器和类适配器其实算是同一种思想，只不过实现方式不同。 根据合成复用原则，使用组合替代继承， 所以它解决了类适配器必须继承 src 的局限性问题，也不再要求 dst 必须是接口。 

使用成本更低，更灵活

##### 接口适配器

一些书籍称为：适配器模式(Default Adapter Pattern)或缺省适配器模式。 

核心思路：当不需要全部实现接口提供的方法时，可先设计一个抽象类实现接口，并为该接口中每个方法提供 一个**默认实现（空方法）**，那么该抽象类的子类可有选择地覆盖父类的某些方法来实现需求

适用于一个接口不想使用其所有的方法的情况

```java
public interface Interface4 {
    public void m1();
    public void m2();
    public void m3();
    public void m4();
}
//在 AbsAdapter 我们将 Interface4 的方法进行默认实现
public abstract class AbsAdapter implements Interface4 {
    //默认实现
    public void m1() {
    }
    public void m2() {
    }
    public void m3() {
    }
    public void m4() {
    }
}
public class Client { 
    public static void main(String[] args){
        AbsAdapter absAdapter = new AbsAdapter() {
            //只需要去覆盖我们 需要使用 接口方法
            @Override
            public void m1() {
            // TODO Auto-generated method stub
                System.out.println("使用了 m1 的方法");
                }
            };
            absAdapter.m1();
    }
}
```

###### 小结

由于jdk已经默认实现接口方法了，该方案可以将中间层抽象类抽离，**匿名实现接口**

#### 桥接模式

##### 示例

```java
/*
现在对不同手机类型的不同品牌实现操作编程(比如:开机、关机、上网，打电话等)
*/
/*
1)桥接模式(Bridge 模式)是指：将实现与抽象放在两个不同的类层次中，使两个层次可以独立改变。
2) 是一种结构型设计模式
3) Bridge 模式基于类的最小设计原则，通过使用封装、聚合及继承等行为让不同的类承担不同的职责。它的主要
特点是把抽象(Abstraction)与行为实现(Implementation)分离开来，从而可以保持各部分的独立性以及应对他们的功能扩展
*/
//接口
public interface Brand {
    void open();
    void close();
    void call();
}
public class Vivo implements Brand {
    @Override
    public void open() {
        // TODO Auto-generated method stub
        System.out.println(" Vivo 手机开机 ");
    }
    @Override
    public void close() {
        // TODO Auto-generated method stub
        System.out.println(" Vivo 手机关机 ");
    }
    @Override
    public void call() {
        // TODO Auto-generated method stub
        System.out.println(" Vivo 手机打电话 ");
    }
}
public class XiaoMi implements Brand {
    @Override
    public void open() {
    // TODO Auto-generated method stub
    System.out.println(" 小米手机开机 ");
    }
    @Override
    public void close() {
        // TODO Auto-generated method stub
        System.out.println(" 小米手机关机 ");
    }
    @Override
    public void call() {
        // TODO Auto-generated method stub
        System.out.println(" 小米手机打电话 ");
    }
}
public abstract class Phone {
    //组合品牌
    private Brand brand;
    //构造器
    public Phone(Brand brand) {
        super();
        this.brand = brand;
    }
    protected void open() {
        this.brand.open();
    }
    protected void close() {
        brand.close();
    }
    protected void call() {
        brand.call();
    }
}
public class UpRightPhone extends Phone {
    //构造器
    public UpRightPhone(Brand brand) {
        super(brand);
    }
    public void open() {
        super.open();
        System.out.println(" 直立样式手机 ");
    }
    public void close() {
        super.close();
        System.out.println(" 直立样式手机 ");
    }
    public void call() {
        super.call();
        System.out.println(" 直立样式手机 ");
    }
}
//折叠式手机类，继承 抽象类 Phone
public class FoldedPhone extends Phone {
    //构造器
    public FoldedPhone(Brand brand) {
    super(brand);
    }
    public void open() {
    super.open();
    System.out.println(" 折叠样式手机 ");
    }
    public void close() {
    super.close();
    System.out.println(" 折叠样式手机 ");
    }
    public void call() {
    super.call();
    System.out.println(" 折叠样式手机 ");
    }
}
//客户端
public class Client {
public static void main(String[] args) {
    //获取折叠式手机 (样式 + 品牌 )
    Phone phone1 = new FoldedPhone(new XiaoMi());
    phone1.open();
    phone1.call();
    phone1.close();
    System.out.println("=======================");
    Phone phone2 = new FoldedPhone(new Vivo());
    phone2.open();
    phone2.call();
    phone2.close();
    System.out.println("==============");
    UpRightPhone phone3 = new UpRightPhone(new XiaoMi());
    phone3.open();
    phone3.call();
    phone3.close();
    System.out.println("==============");
    UpRightPhone phone4 = new UpRightPhone(new Vivo());
    phone4.open();
    phone4.call();
    phone4.close();
    }
}
```

##### 小结

实现了抽象和实现部分的分离，从而极大的提供了系统的灵活性，让抽象部分和实现部分独立开来，这有助于 系统进行分层设计，从而产生更好的结构化系统。 

 对于系统的高层部分，只需要知道抽象部分和实现部分的接口就可以了，其它的部分由具体业务来完成。 

**桥接模式替代多层继承方案**，可以减少子类的个数，降低系统的管理和维护成本

桥接模式的引入增加了系统的理解和设计难度，由于聚合关联关系建立在抽象层，要求开发者针对抽象进行设 计和编程 

桥接模式要求正确识别出系统中两个独立变化的维度(抽象、和实现)，因此其使用范围有一定的局限性，即需 要有这样的应用场景。

#### 装饰者模式

##### 示例

```java
/*
1) 咖啡种类/单品咖啡：Espresso(意大利浓咖啡)、ShortBlack、LongBlack(美式咖啡)、Decaf(无因咖啡)
2) 调料：Milk、Soy(豆浆)、Chocolate
3) 要求在扩展新的咖啡种类时，具有良好的扩展性、改动方便、维护方便
4) 使用 OO 的来计算不同种类咖啡的费用: 客户可以点单品咖啡，也可以单品咖啡+调料组合。
*/
/*
装饰者模式：动态的将新功能附加到对象上。在对象功能扩展（套娃）方面，它比继承更有弹性，装饰者模式也体现了开闭原则(ocp),
*/
//装饰者接口
public abstract class Drink {
    public String des; // 描述
    private float price = 0.0f;
        public String getDes() {
        return des;
    }
    public void setDes(String des) {
        this.des = des;
    }
    public float getPrice() {
        return price;
    }
    public void setPrice(float price) {
        this.price = price;
    }
    //计算费用的抽象方法
    //子类来实现
    public abstract float cost();
}
//抽象的装饰者
public class Decorator extends Drink {
    private Drink obj;
    public Decorator(Drink obj) { //组合
        // TODO Auto-generated constructor stub
        this.obj = obj;
    }
    @Override
    public float cost() {
        // TODO Auto-generated method stub
        // getPrice 自己价格
        return super.getPrice() + obj.cost();
    }
    @Override
    public String getDes() {
        // TODO Auto-generated method stub
        // obj.getDes() 输出被装饰者的信息
        return des + " " + getPrice() + " && " + obj.getDes();
    }
}

//具体的 Decorator， 这里就是调味品
public class Chocolate extends Decorator {
    public Chocolate(Drink obj) {
        super(obj);
        setDes(" 巧克力 ");
        setPrice(3.0f); // 调味品 的价格
    }
}
public class Milk extends Decorator {
    public Milk(Drink obj) {
    super(obj);
    // TODO Auto-generated constructor stub
    setDes(" 牛奶 ");
    setPrice(2.0f);
    }
}
public class Soy extends Decorator{
    public Soy(Drink obj) {
        super(obj);
        // TODO Auto-generated constructor stub
        setDes(" 豆浆 ");
        setPrice(1.5f);
    }
}

//缓冲层
public class Coffee extends Drink {
    @Override
    public float cost() {
    // TODO Auto-generated method stub
        return super.getPrice();
    }
}
//咖啡种类
public class DeCaf extends Coffee {
    public DeCaf() {
    setDes(" 无因咖啡 ");
    setPrice(1.0f);
    }
}
public class LongBlack extends Coffee {
public LongBlack(){
    setDes(" longblack ");
    setPrice(5.0f);
    }
}
public class Espresso extends Coffee {
    public Espresso() {
        setDes(" 意大利咖啡 ");
        setPrice(6.0f);
    }
}
public class ShortBlack extends Coffee{
    public ShortBlack() {
    setDes(" shortblack ");
    setPrice(4.0f);
    }
}
//客户端
public class CoffeeBar {
public static void main(String[] args) {
    // TODO Auto-generated method stub
    // 装饰者模式下的订单：2 份巧克力+一份牛奶的 LongBlack
    // 1. 点一份 LongBlack
    Drink order = new LongBlack();
    System.out.println("费用 1=" + order.cost());
    System.out.println("描述=" + order.getDes());
    // 2. order 加入一份牛奶
    order = new Milk(order);
    System.out.println("order 加入一份牛奶 费用 =" + order.cost());
    System.out.println("order 加入一份牛奶 描述 = " + order.getDes());
    // 3. order 加入一份巧克力
    order = new Chocolate(order);
    System.out.println("order 加入一份牛奶 加入一份巧克力 费用 =" + order.cost())
    System.out.println("order 加入一份牛奶 加入一份巧克力 描述 = " + order.getDes());
    // 3. order 加入一份巧克力
    order = new Chocolate(order);
    System.out.println("order 加入一份牛奶 加入 2 份巧克力 费用 =" + order.cost());
    System.out.println("order 加入一份牛奶 加入 2 份巧克力 描述 = " + order.getDes());
    System.out.println("===========================");
    Drink order2 = new DeCaf();
    System.out.println("order2 无因咖啡 费用 =" + order2.cost());
    System.out.println("order2 无因咖啡 描述 = " + order2.getDes());
    order2 = new Milk(order2);
    System.out.println("order2 无因咖啡 加入一份牛奶 费用 =" + order2.cost());
    System.out.println("order2 无因咖啡 加入一份牛奶 描述 = " + order2.getDes());
    }
}
```

#### 组合模式(部分整体模式)

##### 示例

```java
/*
编写程序展示一个学校院系结构：需求是这样，要在一个页面中展示出学校的院系组成，一个学校有多个学院，一个学院有多个系
*/
/*
1) 组合模式（Composite Pattern），又叫部分整体模式，它创建了对象组的树形结构，将对象组合成树状结构以
表示“整体-部分”的层次关系。
2) 组合模式依据树形结构来组合对象，用来表示部分以及整体层次。
3) 这种类型的设计模式属于结构型模式。
4) 组合模式使得用户对单个对象和组合对象的访问具有一致性，即：组合能让客户以一致的方式处理个别对象以及组合对象
*/
//抽象顶层
public abstract class OrganizationComponent {
    private String name; // 名字
    private String des; // 说明
    protected void add(OrganizationComponent organizationComponent) {
    //默认实现
    throw new UnsupportedOperationException();
    }
    protected void remove(OrganizationComponent organizationComponent) {
    //默认实现
    throw new UnsupportedOperationException();
    }
    //构造器
    public OrganizationComponent(String name, String des) {
    super();
    this.name = name;
    this.des = des;
    }
    public String getName() {
    return name;
    }
    public void setName(String name) {
    this.name = name;
    }
    public String getDes() {
    return des;
    }
    public void setDes(String des) {
    this.des = des;
    }
    //方法 print, 做成抽象的, 子类都需要实现
    protected abstract void print();
}
//University 就是 Composite , 可以管理 College
public class University extends OrganizationComponent {
    List<OrganizationComponent> organizationComponents = new ArrayList<OrganizationComponent>();
        // 构造器
        public University(String name, String des) {
        super(name, des);
        // TODO Auto-generated constructor stub
    }
    // 重写 add
    @Override
    protected void add(OrganizationComponent organizationComponent) {
        // TODO Auto-generated method stub
        organizationComponents.add(organizationComponent);
    }
    // 重写 remove
    @Override
    protected void remove(OrganizationComponent organizationComponent) {
        // TODO Auto-generated method stub
        organizationComponents.remove(organizationComponent);
    }
    @Override
    public String getName() {
        // TODO Auto-generated method stub
        return super.getName();
    }
    @Override
    public String getDes() {
        // TODO Auto-generated method stub
        return super.getDes();
    }
    // print 方法，就是输出 University 包含的学院
    @Override
    protected void print() {
        // TODO Auto-generated method stub
        System.out.println("--------------" + getName() + "--------------");
        //遍历 organizationComponents
        for (OrganizationComponent organizationComponent : organizationComponents) {
        organizationComponent.print();
        }
    }
}
//院系类
public class College extends OrganizationComponent {
    //List 中 存放的 Department
    List<OrganizationComponent> organizationComponents = new ArrayList<OrganizationComponent>();
    // 构造器
    public College(String name, String des) {
        super(name, des);
        // TODO Auto-generated constructor stub
    }
    // 重写 add
    @Override
    protected void add(OrganizationComponent organizationComponent) {
        // TODO Auto-generated// 将来实际业务中，Colleage 的 add 和 University add 不一定完全一样
        organizationComponents.add(organizationComponent);
    }
    // 重写 remove
    @Override
    protected void remove(OrganizationComponent organizationComponent) {
        // TODO Auto-generated method stub
        organizationComponents.remove(organizationComponent);
    }
    @Override
    public String getName() {
        // TODO Auto-generated method stub
        return super.getName();
    }
    @Override
    public String getDes() {
        // TODO Auto-generated method stub
        return super.getDes();
    }
    // print 方法，就是输出 University 包含的学院
    @Override
    protected void print(){ // TODO Auto-generated method stub
        System.out.println("--------------" + getName() + "--------------");
        //遍历 organizationComponents
        for (OrganizationComponent organizationComponent : organizationComponents) {
        organizationComponent.print();
        }
    }
}
//专业类，叶子节点啦
public class Department extends OrganizationComponent {
    //没有集合
    public Department(String name, String des) {
        super(name, des);
        // TODO Auto-generated constructor stub
    }
    //add , remove 就不用写了，因为他是叶子节点
    @Override
    public String getName() {
        // TODO Auto-generated method stub
        return super.getName();
    }
    @Override
    public String getDes() {
        // TODO Auto-generated method stub
        return super.getDes();
    }
    @Override
    protected void print() {
        // TODO Auto-generated method stub
        System.out.println(getName());
    }
}
//客户端
public class Client {
public static void main(String[] args) {
    // TODO Auto-generated method stub
    //从大到小创建对象 学校
    OrganizationComponent university = new University("清华大学", " 中国顶级大学 ");
    //创建 学院
    OrganizationComponent computerCollege = new College("计算机学院", " 计算机学院 ");
    OrganizationComponent infoEngineercollege = new College("信息工程学院", " 信息工程学院 ");
    //创建各个学院下面的系(专业)
    computerCollege.add(new Department("软件工程", " 软件工程不错 "));
    computerCollege.add(new Department("网络工程", " 网络工程不错 "));
    computerCollege.add(new Department("计算机科学与技术", " 计算机科学与技术是老牌的专业 "));
    //
    infoEngineercollege.add(new Department("通信工程", " 通信工程不好学 "));
    infoEngineercollege.add(new Department("信息工程", " 信息工程好学 "));
    //将学院加入到 学校
    university.add(computerCollege);
    university.add(infoEngineercollege);
    //university.print();
    infoEngineercollege.print();
    }
}
```

#### 外观模式(过程模式)

##### 示例

```java
/*
    组建一个家庭影院：
    DVD 播放器、投影仪、自动屏幕、环绕立体声、爆米花机,要求完成使用家庭影院的功能，其过程为：
    直接用遥控器：统筹各设备开关
    开爆米花机
    放下屏幕
    开投影仪
    开音响
    开 DVD，选 dvd
    去拿爆米花
    调暗灯光
    播放
    观影结束后，关闭各种设备
*/
/*
1) 外观模式（Facade），也叫“过程模式：外观模式为子系统中的一组接口提供一个一致的界面，此模式定义了一个高层接口，这个接口使得这一子系统更加容易使用
2) 外观模式通过定义一个一致的接口，用以屏蔽内部子系统的细节，使得调用端只需跟这个接口发生调用，而无需关心这个子系统的内部细节
*/

//要私有化构造器
public class DVDPlayer {
    //使用单例模式, 使用饿汉式
    private static DVDPlayer instance = new DVDPlayer();
    public static DVDPlayer getInstanc() {
        return instance;
    }
    public void on() {
        System.out.println(" dvd on ");
    }
    public void off() {
        System.out.println(" dvd off ");
    }
    public void play() {
        System.out.println(" dvd is playing ");
    }
    //.... public void pause() {
        System.out.println(" dvd pause ..");
    }
}
public class Popcorn {
    private static Popcorn instance = new Popcorn();
    public static Popcorn getInstance() {
    return instance;
    }
    public void on() {
    System.out.println(" popcorn on ");
    }
    public void off() {
    System.out.println(" popcorn ff ");
    }
    public void pop() {
    System.out.println(" popcorn is poping ");
    }
}    
public class Projector {
    private static Projector instance = new Projector();
    public static Projector getInstance() {
    return instance;
    }
    public void on() {
    System.out.println(" Projector on ");
    }
    public void off() {
    System.out.println(" Projector ff ");
    }
    public void focus() {
    System.out.println(" Projector is Projector ");
    }
    //... 
}
public class Screen {
    private static Screen instance = new Screen();
    public static Screen getInstance() {
    return instance;
    }
    public void up() {
    System.out.println(" Screen up ");
    }
    public void down() {
    System.out.println(" Screen down ");
    }
}
public class Stereo {
    private static Stereo instance = new Stereo();
    public static Stereo getInstance() {
    return instance;
    }
    public void on() {
    System.out.println(" Stereo on ");
    }
    public void off() {
    System.out.println(" Screen off ");
    }
    public void up() {
    System.out.println(" Screen up.. ");
    }
    //... 
}
public class TheaterLight {
    private static TheaterLight instance = new TheaterLight();
    public static TheaterLight getInstance() {
    return instance;
    }
    public void on() {
    System.out.println(" TheaterLight on ");
    }
    public void off() {
    System.out.println(" TheaterLight off ");
    }
    public void dim() {
    System.out.println(" TheaterLight dim.. ")
        }
    public void bright() {
    System.out.println(" TheaterLight bright.. ");
    }
}
//封装
public class HomeTheaterFacade {
    //定义各个子系统对象
    private TheaterLight theaterLight;
    private Popcorn popcorn;
    private Stereo stereo;
    private Projector projector;
    private Screen screen;
    private DVDPlayer dVDPlayer;
    //构造器
    public HomeTheaterFacade() {
    super();
    this.theaterLight = TheaterLight.getInstance();
    this.popcorn = Popcorn.getInstance();
    this.stereo = Stereo.getInstance();
    this.projector = Projector.getInstance();
    this.screen = Screen.getInstance();
    this.dVDPlayer = DVDPlayer.getInstanc();
    }
    //操作分成 4 步
    public void ready() {
    popcorn.on();
    popcorn.pop();
    screen.down();
    projector.on();
    stereo.on();
    dVDPlayer.on();
    theaterLight.dim();
    }
    public void play() {
    dVDPlayer.play();
    }
    public void pause() {
    dVDPlayer.pause();
    }
    public void end() {
    popcorn.off();
    theaterLight.bright();
    screen.up();
    projector.off();
    stereo.off();
    dVDPlayer.off();
    }
}
public class Client {
    public static void main(String[] args) {
    // TODO Auto-generated method stub
    //这里直接调用。。 很麻烦
    HomeTheaterFacade homeTheaterFacade = new HomeTheaterFacade();
    homeTheaterFacade.ready();
    homeTheaterFacade.play();
    homeTheaterFacade.end();
    }
}
```

##### 小结

 外观模式对外屏蔽了子系统的细节，因此外观模式降低了客户端对子系统使用的复杂性 

外观模式对客户端与子系统的耦合关系 - 解耦，让子系统内部的模块更易维护和扩展  

通过合理的使用外观模式，可以帮我们更好的划分访问的层次 

当系统需要进行分层设计时，可以考虑使用 Facade 模式 

在维护一个遗留的大型系统时，可能这个系统已经变得非常难以维护和扩展，此时可以考虑为新系统开发一个 Facade 类，来提供遗留系统的比较清晰简单的接口，让新系统与 Facade 类交互，提高复用性 

不能过多的或者不合理的使用外观模式，使用外观模式好，还是直接调用模块好。要以让系统有层次，利于维 护为目的。

#### 享元模式

##### 示例

```java
/*
小型的外包项目，给客户 A 做一个产品展示网站，客户 A 的朋友感觉效果不错，也希望做这样的产品展示网站，但是要求都有些不同：
1) 有客户要求以新闻的形式发布
2) 有客户人要求以博客的形式发布
3) 有客户希望以微信公众号的形式发布
*/
/*
基本介绍
1) 享元模式（Flyweight Pattern） 也叫 蝇量模式: 运用共享技术有效地支持大量细粒度的对象
2) 常用于系统底层开发，解决系统的性能问题。像数据库连接池，里面都是创建好的连接对象，在这些连接对象中有我们需要的则直接拿来用，避免重新创建，如果没有我们需要的，则创建一个
3) 享元模式能够解决重复对象的内存浪费的问题，当系统中有大量相似对象，需要缓冲池时。不需总是创建新对象，可以从缓冲池里拿。这样可以降低系统内存，同时提高效率
4) 享元模式经典的应用场景就是池技术了，String 常量池、数据库连接池、缓冲池等等都是享元模式的应用，享元模式是池技术的重要实现方式
*/
/*
内部状态和外部状态
1) 享元模式提出了两个要求：细粒度和共享对象。这里就涉及到内部状态和外部状态了，即将对象的信息分为两
个部分：内部状态和外部状态
2) 内部状态指对象共享出来的信息，存储在享元对象内部且不会随环境的改变而改变
3) 外部状态指对象得以依赖的一个标记，是随环境改变而改变的、不可共享的状态。
4) 举个例子：围棋理论上有 361 个空位可以放棋子，每盘棋都有可能有两三百个棋子对象产生，因为内存空间有限，一台服务器很难支持更多的玩家玩围棋游戏，如果用享元模式来处理棋子，那么棋子对象就可以减少到只有两个实例，这样就很好的解决了对象的开销问题
*/
// 外部共享
public class User {
    private String name;
    public User(String name) {
        super();
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
// 网站工厂类，根据需要返回压一个网站
public class WebSiteFactory {
    //集合， 充当池的作用
    private HashMap<String, ConcreteWebSite> pool = new HashMap<>();
    //根据网站的类型，返回一个网站, 如果没有就创建一个网站，并放入到池中,并返回
    public WebSite getWebSiteCategory(String type) {
            if(!pool.containsKey(type)) {
            //就创建一个网站，并放入到池中
            pool.put(type, new ConcreteWebSite(type));
            }
            return (WebSite)pool.get(type);
        }
        //获取网站分类的总数 (池中有多少个网站类型)
     public int getWebSiteCount() {
            return pool.size();
    }
}

public abstract class WebSite {
    public abstract void use(User user);//抽象方法
}
//具体网站
public class ConcreteWebSite extends WebSite {
    //共享的部分，内部状态
    private String type = ""; //网站发布的形式(类型)
        //构造器
        public ConcreteWebSite(String type) {
        this.type = type;
    }
    @Override
    public void use(User user) {
    // TODO Auto-generated method stub
    System.out.println("网站的发布形式为:" + type + " 在使用中 .. 使用者是" + user.getName());
    }
}
//客户端
public class Client {
    public static void main(String[] args) {
        // 创建一个工厂类
        WebSiteFactory factory = new WebSiteFactory();
        // 客户要一个以新闻形式发布的网站
        WebSite webSite1 = factory.getWebSiteCategory("新闻");
        webSite1.use(new User("tom"));
        // 客户要一个以博客形式发布的网站
        WebSite webSite2 = factory.getWebSiteCategory("博客");
        webSite2.use(new User("jack"));
        // 客户要一个以博客形式发布的网站
        WebSite webSite3 = factory.getWebSiteCategory("博客");
        webSite3.use(new User("smith"));
        // 客户要一个以博客形式发布的网站
        WebSite webSite4 = factory.getWebSiteCategory("博客");
        webSite4.use(new User("king"));
        System.out.println("网站的分类共=" + factory.getWebSiteCount());
    }
}
```

##### 小结

 在享元模式这样理解，“享”就表示共享，“元”表示对象 

系统中有大量对象，这些对象消耗大量内存，并且对象的状态大部分可以外部化时，我们就可以考虑选用享元 模式 

用唯一标识码判断，如果在内存中有，则返回这个唯一标识码所标识的对象，用 HashMap/HashTable 存储 

享元模式大大减少了对象的创建，降低了程序内存的占用，提高效率

 享元模式提高了系统的复杂度。需要分离出内部状态和外部状态，而外部状态具有固化特性，不应该随着内部 状态的改变而改变，这是我们使用享元模式需要注意的地方. 

使用享元模式时，注意划分内部状态和外部状态，并且需要有一个工厂类加以控制。 

享元模式经典的应用场景是需要缓冲池的场景，比如 String 常量池、数据库连接

#### 代理模式

##### 基本介绍

```java
/*
1) 代理模式：为一个对象提供一个替身，以控制对这个对象的访问。即通过代理对象访问目标对象.这样做的好处
是:可以在目标对象实现的基础上,增强额外的功能操作,即扩展目标对象的功能。
2) 被代理的对象可以是远程对象、创建开销大的对象或需要安全控制的对象
3) 代理模式有不同的形式, 主要有三种 静态代理、动态代理 (JDK 代理、接口代理)和 Cglib 代理 (可以在内存动态的创建对象，而不需要实现接口， 他是属于动态代理的范畴) 
*/
```

##### 静态代理

```java
/*
1) 定义一个接口:ITeacherDao
2) 目标对象 TeacherDAO 实现接口 ITeacherDAO
3) 使用静态代理方式,就需要在代理对象 TeacherDAOProxy 中也实现 ITeacherDAO
4) 调用的时候通过调用代理对象的方法来调用目标对象. 
5) 特别提醒：代理对象与目标对象要实现相同的接口,然后通过调用相同的方法来调用目标对象的方法
*/
//代理对象,静态代理
public class TeacherDaoProxy implements ITeacherDao{
    private ITeacherDao target; // 目标对象，通过接口来聚合
    //构造器
    public TeacherDaoProxy(ITeacherDao target) {
        this.target = target;
    }
    @Override
    public void teach() {
        // TODO Auto-generated method stub
        System.out.println("开始代理 完成某些操作。。。。。 ");//方法
        target.teach();
        System.out.println("提交。。。。。");//方法
    }
}
public class TeacherDao implements ITeacherDao {
    @Override
    public void teach() {
    // TODO Auto-generated method stub
    System.out.println(" 老师授课中 。。。。。");
    }
}
//接口
public interface ITeacherDao {
    void teach(); // 授课的方法
}
public class Client {
    public static void main(String[] args) {
    // TODO Auto-generated method stub
    //创建目标对象(被代理对象)
    TeacherDao teacherDao = new TeacherDao();
    //创建代理对象, 同时将被代理对象传递给代理对象
    TeacherDaoProxy teacherDaoProxy = new TeacherDaoProxy(teacherDao);
    //通过代理对象，调用到被代理对象的方法
    //即：执行的是代理对象的方法，代理对象再去调用目标对象的方法
    teacherDaoProxy.teach();
    }
}
/*
1) 优点：在不修改目标对象的功能前提下, 能通过代理对象对目标功能扩展
2) 缺点：因为代理对象需要与目标对象实现一样的接口,所以会有很多代理类
3) 一旦接口增加方法,目标对象与代理对象都要维护
*/
```

##### 动态代理

```java
/* 基本介绍
1) 代理对象,不需要实现接口，但是目标对象要实现接口，否则不能用动态代理
2) 代理对象的生成，是利用 JDK 的 API，动态的在内存中构建代理对象
3) 动态代理也叫做：JDK 代理、接口代理
*/
//接口
public interface ITeacherDao {
    void teach(); // 授课方法
    void sayHello(String name);
}
public class TeacherDao implements ITeacherDao {
    @Override
    public void teach() {
        // TODO Auto-generated method stub
        System.out.println(" 老师授课中.... ");
    }
    @Override
    public void sayHello(String name) {
        // TODO Auto-generated method stub
        System.out.println("hello " + name);
    }
}
public class ProxyFactory {
    //维护一个目标对象 , Object
    private Object target;
    //构造器 ， 对 target 进行初始化
    public ProxyFactory(Object target) {
        this.target = target;
    }
    //给目标对象 生成一个代理对象
    public Object getProxyInstance() {
    //说明
    /*
    * public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)
    //1. ClassLoader loader ： 指定当前目标对象使用的类加载器, 获取加载器的方法固定
    //2. Class<?>[] interfaces: 目标对象实现的接口类型，使用泛型方法确认类型
    //3. InvocationHandler h : 事情处理，执行目标对象的方法时，会触发事情处理器方法, 会把当前执行
    的目标对象方法作为参数传入
    */
    return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), new InvocationHandler() {
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // TODO Auto-generated method stub
        System.out.println("JDK 代理开始~~");
        //反射机制调用目标对象的方法
        Object returnVal = method.invoke(target, args);
        System.out.println("JDK 代理提交");
        return returnVal;
    }
    });
    }
}
public class Client {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        //创建目标对象
        ITeacherDao target = new TeacherDao();
        //给目标对象，创建代理对象, 可以转成 ITeacherDao,可以用泛型
            ITeacherDao proxyInstance = (ITeacherDao)new ProxyFactory(target).getProxyInstance();
        // proxyInstance=class com.sun.proxy.$Proxy0 内存中动态生成了代理对象
        System.out.println("proxyInstance=" + proxyInstance.getClass());
        //通过代理对象，调用目标对象的方法
        //proxyInstance.teach();
        proxyInstance.sayHello(" tom ");
        }
}
```

##### Cglib 代理

```java
/*
1) 静态代理和 JDK 代理模式都要求目标对象是实现一个接口,但是有时候目标对象只是一个单独的对象,并没有实现任何的接口,这个时候可使用目标对象子类来实现代理-这就是 Cglib 代理
2) Cglib代理也叫作子类代理,它是在内存中构建一个子类对象从而实现对目标对象功能扩展, 有些书也将Cglib代理归属到动态代理。
3) Cglib 是一个强大的高性能的代码生成包,它可以在运行期扩展 java 类与实现 java 接口.它广泛的被许多 AOP 的框架使用,例如 Spring AOP，实现方法拦截
4) 在 AOP 编程中如何选择代理模式：
    1. 目标对象需要实现接口，用 JDK 代理
    2. 目标对象不需要实现接口，用 Cglib 代理
5) Cglib 包的底层是通过使用字节码处理框架 ASM 来转换字节码并生成新的类
6) 在内存中动态构建子类，注意代理的类不能为 final，否则报错java.lang.IllegalArgumentException:
7) 目标对象的方法如果为 final/static,那么就不会被拦截,即不会执行目标对象额外的业务方法
*/
public class TeacherDao {
public String teach() {
    System.out.println(" 老师授课中 ， 我是 cglib 代理，不需要实现接口 ");
    return "hello";
    }
}
//代理类
import java.lang.reflect.Method;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;
public class ProxyFactory implements MethodInterceptor {
    //维护一个目标对象
    private Object target;
    //构造器，传入一个被代理的对象
    public ProxyFactory(Object target) {
        this.target = target;
    }
    //返回一个代理对象: 是 target 对象的代理对象
    public Object getProxyInstance() {
        //1. 创建一个工具类
        Enhancer enhancer = new Enhancer();
        //2. 设置父类
        enhancer.setSuperclass(target.getClass());
        //3. 设置回调函数
        enhancer.setCallback(this);
        //4. 创建子类对象，即代理对象
        return enhancer.create();
    }
        //重写 intercept 方法，会调用目标对象的方法
    @Override
    public Object intercept(Object arg0, Method method, Object[] args, MethodProxy arg3) throws Throwable {
        // TODO Auto-generated method stub
        System.out.println("Cglib 代理模式 ~~ 开始");
        Object returnVal = method.invoke(target, args);
        System.out.println("Cglib 代理模式 ~~ 提交");
        return returnVal;
    }
}
public class Client {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        //创建目标对象
        TeacherDao target = new TeacherDao();
        //获取到代理对象，并且将目标对象传递给代理对象
        TeacherDao proxyInstance = (TeacherDao)new ProxyFactory(target).getProxyInstance();
        //执行代理对象的方法，触发 intecept 方法，从而实现 对目标对象的调用
        String res = proxyInstance.teach();
        System.out.println("res=" + res);
    }
}    
```

##### 变体

防火墙代理 内网通过代理穿透防火墙，实现对公网的访问。 

缓存代理 比如：当请求图片文件等资源时，先到缓存代理取，如果取到资源则 ok,如果取不到资源，再到公网或者数据 库取，然后缓存

远程代理 远程对象的本地代表，通过它可以把远程对象当本地对象来调用。远程代理通过网络和真正的远程对象沟通信 息。 

同步代理：主要使用在多线程编程中，完成多线程间同步工作 同步代理：主要使用在多线程编程中，完成多线程间同步工作

### 行为型模式

#### 模版方法模式

##### 示例

```java
/*
1) 制作豆浆的流程 选材--->添加配料--->浸泡--->放到豆浆机打碎
2) 通过添加不同的配料，可以制作出不同口味的豆浆
3) 选材、浸泡和放到豆浆机打碎这几个步骤对于制作每种口味的豆浆都是一样的
4) 请使用 模板方法模式 完成 (说明：因为模板方法模式，比较简单，很容易就想到这个方案，因此就直接使用，不再使用传统的方案来引出模板方法模式 */
/*
1) 模板方法模式（Template Method Pattern），又叫模板模式(Template Pattern)，z 在一个抽象类公开定义了执行
它的方法的模板。它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行。
2) 简单说，模板方法模式 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中，使得子类可以不改变一
个算法的结构，就可以重定义该算法的某些特定步骤
3) 这种类型的设计模式属于行为型模式。
*/

public class Client {
public static void main(String[] args) {
    // TODO Auto-generated method stub
    //制作红豆豆浆
    System.out.println("----制作红豆豆浆----");
    SoyaMilk redBeanSoyaMilk = new RedBeanSoyaMilk();
    redBeanSoyaMilk.make();
    System.out.println("----制作花生豆浆----");
    SoyaMilk peanutSoyaMilk = new PeanutSoyaMilk();
    peanutSoyaMilk.make();
    }
}
//抽象类，表示豆浆
public abstract class SoyaMilk {
    //模板方法, make , 模板方法可以做成 final , 不让子类去覆盖. final void make() {
    select();
    addCondiments();
    soak();
    beat();
    }
    //选材料
    void select() {
    System.out.println("第一步：选择好的新鲜黄豆 ");
    }
    //添加不同的配料， 抽象方法, 子类具体实现
    abstract void addCondiments();
    //浸泡
    void soak() {
    System.out.println("第三步， 黄豆和配料开始浸泡， 需要 3 小时 ");
    }
    void beat() {
    System.out.println("第四步：黄豆和配料放到豆浆机去打碎 ");
    }
}

public class RedBeanSoyaMilk extends SoyaMilk {
    @Override
    void addCondiments() {
    // TODO Auto-generated method stub
    System.out.println(" 加入上好的红豆 ");
    }
}
public class PeanutSoyaMilk extends SoyaMilk {
    @Override
    void addCondiments() {
    // TODO Auto-generated method stub
    System.out.println(" 加入上好的花生 ");
    }
}
```

##### 钩子方法

```
/*
1) 在模板方法模式的父类中，我们可以定义一个方法，它默认不做任何事，子类可以视情况要不要覆盖它，该方法称为“钩子”。
2) 还是用上面做豆浆的例子来讲解，比如，我们还希望制作纯豆浆，不添加任何的配料，请使用钩子方法对前面的模板方法进行改造
*/
//抽象类，表示豆浆
public abstract class SoyaMilk {
    //模板方法, make , 模板方法可以做成 final , 不让子类去覆盖. final void make() {
    select();
    addCondiments();
    soak();
    beat();
    }
    //选材料
    void select() {
    System.out.println("第一步：选择好的新鲜黄豆 ");
    }
    //添加不同的配料， 抽象方法, 子类具体实现
    abstract void addCondiments();
    //浸泡
    void soak() {
    System.out.println("第三步， 黄豆和配料开始浸泡， 需要 3 小时 ");
    }
    void beat() {
    System.out.println("第四步：黄豆和配料放到豆浆机去打碎 ");
    }
    //钩子方法，决定是否需要添加配料
    boolean customerWantCondiments() {
    return true
    }
}
```

##### 小结

```html
1) 基本思想是：算法只存在于一个地方，也就是在父类中，容易修改。需要修改算法时，只要修改父类的模板方法或者已经实现的某些步骤，子类就会继承这些修改
2) 实现了最大化代码复用。父类的模板方法和已实现的某些步骤会被子类继承而直接使用。
3) 既统一了算法，也提供了很大的灵活性。父类的模板方法确保了算法的结构保持不变，同时由子类提供部分步骤的实现。
4) 该模式的不足之处：每一个不同的实现都需要一个子类实现，导致类的个数增加，使得系统更加庞大
5) 一般模板方法都加上 final 关键字， 防止子类重写模板方法. 6) 模板方法模式使用场景：当要完成在某个过程，该过程要执行一系列步骤 ，这一系列的步骤基本相同，但其个别步骤在实现时 可能不同，通常考虑用模板方法模式来处理
```

#### 命令模式

##### 示例

```java
/*
1) 我们买了一套智能家电，有照明灯、风扇、冰箱、洗衣机，我们只要在手机上安装 app 就可以控制对这些家电工作。
2) 这些智能家电来自不同的厂家，我们不想针对每一种家电都安装一个 App，分别控制，我们希望只要一个 app就可以控制全部智能家电。
3) 要实现一个 app 控制所有智能家电的需要，则每个智能家电厂家都要提供一个统一的接口给 app 调用，这时 就可以考虑使用命令模式。
4) 命令模式可将“动作的请求者”从“动作的执行者”对象中解耦出来. 5) 在我们的例子中，动作的请求者是手机 app，动作的执行者是每个厂商的一个家电产品
*/
/*介绍
1) 命令模式（Command Pattern）：在软件设计中，我们经常需要向某些对象发送请求，但是并不知道请求的接收
者是谁，也不知道被请求的操作是哪个，
我们只需在程序运行时指定具体的请求接收者即可，此时，可以使用命令模式来进行设计
2) 命名模式使得请求发送者与请求接收者消除彼此之间的耦合，让对象之间的调用关系更加灵活，实现解耦。
3) 在命名模式中，会将一个请求封装为一个对象，以便使用不同参数来表示不同的请求(即命名)，同时命令模式也支持可撤销的操作。
4) 通俗易懂的理解：将军发布命令，士兵去执行。其中有几个角色：将军（命令发布者）、士兵（命令的具体执
行者）、命令(连接将军和士兵)。Invoker 是调用者（将军），Receiver 是被调用者（士兵），MyCommand 是命令，实现了 Command 接口，持有接收对象
*/
//创建命令接口
public interface Command {
    //执行动作(操作)
    public void execute();
    //撤销动作(操作)
    public void undo();
}
public class LightReceiver {
    public void on() {
    System.out.println(" 电灯打开了.. ");
    }
    public void off() {
    System.out.println(" 电灯关闭了.. ");
    }
}
public class TVReceiver {
    public void on() {
    System.out.println(" 电视机打开了.. ");
    }
    public void off() {
    System.out.println(" 电视机关闭了.. ");
    }
}
public class LightOffCommand implements Command {
    // 聚合 LightReceiver
    LightReceiver light;
    // 构造器
    public LightOffCommand(LightReceiver light) {
    super();
    this.light = light;
    }
    @Override
    public void execute() {
    // TODO Auto-generated method stub
    // 调用接收者的方法
    light.off();
    }
    @Override
    public void undo() {
    // TODO Auto-generated method stub
    // 调用接收者的方法
    light.on();
    }
}
public class LightOnCommand implements Command {
    //聚合 LightReceiver
    LightReceiver light;
    //构造器
    public LightOnCommand(LightReceiver light) {
    super();
    this.light = light;
    }
    @Override
    public void execute() {
    // TODO Auto-generated method stub
    //调用接收者的方法
    light.on();
    }
    @Override
    public void undo() {//调用接收者的方法
    light.off();
    }
}
public class TVOnCommand implements Command {
        // 聚合 TVReceiver
    TVReceiver tv;
    // 构造器
    public TVOnCommand(TVReceiver tv) {
    super();
    this.tv = tv;
    }
    @Override
    public void execute() {
    // TODO Auto-generated method stub
    // 调用接收者的方法
    tv.on();
    }
    @Override
    public void undo() {
    // TODO Auto-generated method stub
    // 调用接收者的方法
    tv.off();
    }
}

public class TVOffCommand implements Command {
    // 聚合 TVReceiver
    TVReceiver tv;
    // 构造器
    public TVOffCommand(TVReceiver tv) {
    super();
    this.tv = tv;
    }
    @Override
    public void execute() {
    // TODO Auto-generated method stub
    // 调用接收者的方法
    tv.off();
    }
    @Override
    public void undo() {
    // TODO Auto-generated method stub
    // 调用接收者的方法
    tv.on();
    }
}
//初始化，省去对空的判断
public class NoCommand implements Command {
    @Override
    public void execute() {
    // TODO Auto-generated method stub
    }
    @Override
    public void undo() {
    // TODO Auto-generated method stub
    }
}
public class RemoteController {
    // 开 按钮的命令数组
    Command[] onCommands;
    Command[] offCommands;
        // 执行撤销的命令
    Command undoCommand;
    // 构造器，完成对按钮初始化
    public RemoteController() {
    onCommands = new Command[5];
    offCommands = new Command[5];
    for (int i = 0; i < 5; i++) {
    onCommands[i] = new NoCommand();
    offCommands[i] = new NoCommand();
    }
    }
    // 给我们的按钮设置你需要的命令
    public void setCommand(int no, Command onCommand, Command offCommand) {
    onCommands[no] = onCommand;
    offCommands[no] = offCommand;
    }
    // 按下开按钮
    public void onButtonWasPushed(int no) { // no 0
    // 找到你按下的开的按钮， 并调用对应方法
    onCommands[no].execute();
    // 记录这次的操作，用于撤销
    undoCommand = onCommands[no];
    }
    // 按下开按钮
    public void offButtonWasPushed(int no) { // no 0
    // 找到你按下的关的按钮， 并调用对应方法
    offCommands[no].execute();
    // 记录这次的操作，用于撤销
    undoCommand = offCommands[no];
    }
    // 按下撤销按钮
    public void undoButtonWasPushed() {
    undoCommand.undo();
    }
}
public class Client {
    public static void main(String[] args) {
    // TODO Auto-generated method stub
    //使用命令设计模式，完成通过遥控器，对电灯的操作
    //创建电灯的对象(接受者)
    LightReceiver lightReceiver = new LightReceiver();
    //创建电灯相关的开关命令
    LightOnCommand lightOnCommand = new LightOnCommand(lightReceiver);
    LightOffCommand lightOffCommand = new LightOffCommand(lightReceiver);
    //需要一个遥控器
    RemoteController remoteController = new RemoteController();
    //给我们的遥控器设置命令, 比如 no = 0 是电灯的开和关的操作
    remoteController.setCommand(0, lightOnCommand, lightOffCommand);
    System.out.println("--------按下灯的开按钮-----------");
    remoteController.onButtonWasPushed(0);
    System.out.println("--------按下灯的关按钮-----------");
    remoteController.offButtonWasPushed(0);
    System.out.println("--------按下撤销按钮-----------");
    remoteController.undoButtonWasPushed();
    System.out.println("=========使用遥控器操作电视机==========");
    TVReceiver tvReceiver = new TVReceiver();
    TVOffCommand tvOffCommand = new TVOffCommand(tvReceiver);
    TVOnCommand tvOnCommand = new TVOnCommand(tvReceiver);
    //给我们的遥控器设置命令, 比如 no = 1 是电视机的开和关的操作
    remoteController.setCommand(1, tvOnCommand, tvOffCommand);
    System.out.println("--------按下电视机的开按钮-----------");
    remoteController.onButtonWasPushed(1);
    System.out.println("--------按下电视机的关按钮-----------");
    remoteController.offButtonWasPushed(1);
    System.out.println("--------按下撤销按钮-----------");
    remoteController.undoButtonWasPushed();
    }
}
```

##### 小结

```java
1)将发起请求的对象与执行请求的对象解耦。发起请求的对象是调用者，调用者只要调用命令对象的 execute()方法就可以让接收者工作，而不必知道具体的接收者对象是谁、是如何实现的，命令对象会负责让接收者执行请求的动作，也就是说：”请求发起者”和“请求执行者”之间的解耦是通过命令对象实现的，命令对象起到了纽带桥梁的作用。
2) 容易设计一个命令队列。只要把命令对象放到列队，就可以多线程的执行命令
3) 容易实现对请求的撤销和重做
4) 命令模式不足：可能导致某些系统有过多的具体命令类，增加了系统的复杂度，这点在在使用的时候要注意
5) 空命令也是一种设计模式，它为我们省去了判空的操作。在上面的实例中，如果没有用空命令，我们每按下一
个按键都要判空，这给我们编码带来一定的麻烦。
6) 命令模式经典的应用场景：界面的一个按钮都是一条命令、模拟 CMD（DOS 命令）订单的撤销/恢复、触发- 反馈机制
```

#### 访问者模式

##### 示例

```java
/*
1) 将观众分为男人和女人，对歌手进行测评，当看完某个歌手表演后，得到他们对该歌手不同的评价(评价 有不同的种类，比如 成功、失败 等
*/
/*
基本介绍
1) 访问者模式（Visitor Pattern），封装一些作用于某种数据结构的各元素的操作，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作。
2) 主要将数据结构与数据操作分离，解决 数据结构和操作耦合性问题
3) 访问者模式的基本工作原理是：在被访问的类里面加一个对外提供接待访问者的接口
4) 访问者模式主要应用场景是：需要对一个对象结构中的对象进行很多不同操作(这些操作彼此没有关联)，同时需要避免让这些操作"污染"这些对象的类，可以选用访问者模式解决
*/
public abstract class Action {
    //得到男性 的测评
    public abstract void getManResult(Man man)
        //得到女的 测评
    public abstract void getWomanResult(Woman woman);
}
public abstract class Person {
    //提供一个方法，让访问者可以访问
    public abstract void accept(Action action);
}
public class Fail extends Action {
    @Override
    public void getManResult(Man man) {
    // TODO Auto-generated method stub
    System.out.println(" 男人给的评价该歌手失败 !");
    }
    @Override
    public void getWomanResult(Woman woman) {
    // TODO Auto-generated method stub
    System.out.println(" 女人给的评价该歌手失败 !");
    }
}
public class Success extends Action {
    @Override
    public void getManResult(Man man) {
    // TODO Auto-generated method stub
    System.out.println(" 男人给的评价该歌手很成功 !");
    }
    @Override
    public void getWomanResult(Woman woman) {
    // TODO Auto-generated method stub
    System.out.println(" 女人给的评价该歌手很成功 !");
    }
}
public class Wait extends Action {
    @Override
    public void getManResult(Man man) {
    // TODO Auto-generated method stub
    System.out.println(" 男人给的评价是该歌手待定 ..");
    }
    @Override
    public void getWomanResult(Woman woman) {
    // TODO Auto-generated method stub
    System.out.println(" 女人给的评价是该歌手待定 ..");
    }
}
public class Man extends Person {
    @Override
    public void accept(Action action) {
    // TODO Auto-generated method stub
    action.getManResult(this);
    }
}
//说明
//1. 这里我们使用到了双分派, 即首先在客户端程序中，将具体状态作为参数传递 Woman 中(第一次分派)
//2. 然后 Woman 类调用作为参数的 "具体方法" 中方法 getWomanResult, 同时将自己(this)作为参数
// 传入，完成第二次的分派
public class Woman extends Person{
    @Override
    public void accept(Action action) {
    // TODO Auto-generated method stub
    action.getWomanResult(this);
    }
}
//数据结构，管理很多人（Man , Woman）
public class ObjectStructure {
    //维护了一个集合
    private List<Person> persons = new LinkedList<>();
    //增加到 list
    public void attach(Person p) {
    persons.add(p);
    }
    //移除
    public void detach(Person p) {
    persons.remove(p);
    }
    //显示测评情况
    public void display(Action action) {
    for(Person p: persons) {
    p.accept(action);
    }
    }
}
public class Client {
    public static void main(String[] args) {
    // TODO Auto-generated method stub
    //创建 ObjectStructure
    ObjectStructure objectStructure = new ObjectStructure();
    objectStructure.attach(new Man());
    objectStructure.attach(new Woman());
    //成功
    Success success = new Success();
    objectStructure.display(success);
    System.out.println("===============");
    Fail fail = new Fail();
    objectStructure.display(fail);
    System.out.println("=======给的是待定的测评========");
    Wait wait = new Wait();
    objectStructure.display(wait);
    }
}
/*
以上述实例为例，假设我们要添加一个 Wait 的状态类，考察 Man 类和 Woman 类的反应，由于使用了双分派，只需增加一个 Action 子类即可在客户端调用即可，不需要改动任何其他类的代码
*/
```

##### 小结

```
 优点
1) 访问者模式符合单一职责原则、让程序具有优秀的扩展性、灵活性非常高
2) 访问者模式可以对功能进行统一，可以做报表、UI、拦截器与过滤器，适用于数据结构相对稳定的系统
 缺点
1) 具体元素对访问者公布细节，也就是说访问者关注了其他类的内部细节，这是迪米特法则所不建议的, 这样造
成了具体元素变更比较困难
2) 违背了依赖倒转原则。访问者依赖的是具体元素，而不是抽象元素
3) 因此，如果一个系统有比较稳定的数据结构，又有经常变化的功能需求，那么访问者模式就是比较合适的.
```

#### 迭代器模式

##### 示例

```java
/*
编写程序展示一个学校院系结构：需求是这样，要在一个页面中展示出学校的院系组成，一个学校有多个学院，一个学院有多个系,统一遍历问题
*/
/*基本介绍
1) 迭代器模式（Iterator Pattern）是常用的设计模式，属于行为型模式
2) 如果我们的集合元素是用不同的方式实现的，有数组，还有 java 的集合类，或者还有其他方式，当客户端要遍历这些集合元素的时候就要使用多种遍历方式，而且还会暴露元素的内部结构，可以考虑使用迭代器模式解决。
3) 迭代器模式，提供一种遍历集合元素的统一接口，用一致的方法遍历集合元素，不需要知道集合对象的底层表示，即：不暴露其内部的结构。
*/
public interface College {
    public String getName();
    //增加系的方法
    public void addDepartment(String name, String desc);
    //返回一个迭代器,遍历
    public Iterator createIterator();
}
public class ComputerCollege implements College {
    Department[] departments;
    int numOfDepartment = 0 ;// 保存当前数组的对象个数
    public ComputerCollege() {
        departments = new Department[5];
        addDepartment("Java 专业", " Java 专业 ");
        addDepartment("PHP 专业", " PHP 专业 ");
        addDepartment("大数据专业", " 大数据专业 ");
     }
    @Override
    public String getName() {
        // TODO Auto-generated method stub
        return "计算机学院";
    }
    @Override
    public void addDepartment(String name, String desc) {
        // TODO Auto-generated method stub
        Department department = new Department(name, desc);
        departments[numOfDepartment] = department;
        numOfDepartment += 1;
    }
    @Override
    public Iterator createIterator() {
        // TODO Auto-generated method stub
        return new ComputerCollegeIterator(departments);
    }
}
public class InfoCollege implements College {
    List<Department> departmentList;
    public InfoCollege() {
    departmentList = new ArrayList<Department>();
    addDepartment("信息安全专业", " 信息安全专业 ");
    addDepartment("网络安全专业", " 网络安全专业 ");
    addDepartment("服务器安全专业", " 服务器安全专业 ");
    }
    @Override
    public String getName() {
    // TODO Auto-generated method stub
    return "信息工程学院";
    }
    @Override
    public void addDepartment(String name, String desc) {
    // TODO Auto-generated method stub
    Department department = new Department(name, desc);
    departmentList.add(department);
    }
    @Override
    public Iterator createIterator() {
    // TODO Auto-generated method stub
    return new InfoColleageIterator(departmentList);
    }
}
public class ComputerCollegeIterator implements Iterator {
    //这里我们需要 Department 是以怎样的方式存放=>数组
    Department[] departments;
    int position = 0; //遍历的位置
    public ComputerCollegeIterator(Department[] departments) {
        this.departments = departments;
    }
    //判断是否还有下一个元素
    @Override
    public boolean hasNext() {
    // TODO Auto-generated method stub
        if(position >= departments.length || departments[position] == null) {
        return false;
        }else {
        return true;
        }
    }
    @Override
    public Object next() {
        // TODO Auto-generated method stub
        Department department = departments[position];
        position += 1;
        return department;
    }
    //删除的方法，默认空实现
    public void remove() {
    }
}
public class InfoColleageIterator implements Iterator {
    List<Department> departmentList; // 信息工程学院是以 List 方式存放系
    int index = -1;//索引
    public InfoColleageIterator(List<Department> departmentList) {
    this.departmentList = departmentList;
    }
    //判断 list 中还有没有下一个元素
    @Override
    public boolean hasNext() {
    // TODO Auto-generated method stub
    if(index >= departmentList.size() - 1) {
    return false;
    } else {
    index += 1;
    return true;
    }
    }
    @Override
    public Object next() {
    // TODO Auto-generated method stub
    return departmentList.get(index);
    }
    //空实现 remove
    public void remove() {
    }
}
public class Department {
    private String name;
    private String desc;
    public Department(String name, String desc) {
        super();
        this.name = name;
        this.desc = desc;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getDesc() {
        return desc;
    }
    public void setDesc(String desc) {
        this.desc = desc;
    }
}
public class OutPutImpl {
    //学院集合
    List<College> collegeList;
    public OutPutImpl(List<College> collegeList) {
    this.collegeList = collegeList;
    }
    //遍历所有学院,然后调用 printDepartment 输出各个学院的系
    public void printCollege() {
    //从 collegeList 取出所有学院, Java 中的 List 已经实现 Iterator
    Iterator<College> iterator = collegeList.iterator();
    while(iterator.hasNext()) {
    //取出一个学院
    College college = iterator.next();
    System.out.println("=== "+college.getName() +"=====" );
    printDepartment(college.createIterator()); //得到对应迭代器
    }
    }
        public void printDepartment(Iterator iterator) {
    while(iterator.hasNext()) {
    Department d = (Department)iterator.next();
    System.out.println(d.getName());
    }
    }
}
public class Client {
    public static void main(String[] args) {
    // TODO Auto-generated method stub
    //创建学院
    List<College> collegeList = new ArrayList<College>();
    ComputerCollege computerCollege = new ComputerCollege();
    InfoCollege infoCollege = new InfoCollege();
    collegeList.add(computerCollege);
    //collegeList.add(infoCollege);
    OutPutImpl outPutImpl = new OutPutImpl(collegeList);
    outPutImpl.printCollege();
    }
}
```

##### 小结

```
 优点
1) 提供一个统一的方法遍历对象，客户不用再考虑聚合的类型，使用一种方法就可以遍历对象了。
2) 隐藏了聚合的内部结构，客户端要遍历聚合的时候只能取到迭代器，而不会知道聚合的具体组成。
3) 提供了一种设计思想，就是一个类应该只有一个引起变化的原因（叫做单一责任原则）。在聚合类中，我们把迭代器分开，就是要把管理对象集合和遍历对象集合的责任分开，这样一来集合改变的话，只影响到聚合对象。而如果遍历方式改变的话，只影响到了迭代器。
4) 当要展示一组相似对象，或者遍历一组相同对象时使用, 适合使用迭代器模式
 缺点
每个聚合对象都要一个迭代器，会生成多个迭代器不好管理类
```

#### 观察者模式

##### 示例

```java
/*
1) 气象站可以将每天测量到的温度，湿度，气压等等以公告的形式发布出去(比如发布到自己的网站或第三方)。
2) 需要设计开放型 API，便于其他第三方也能接入气象站获取数据。
3) 提供温度、气压和湿度的接口
4) 测量数据更新时，要能实时的通知给第三方
*/
//接口, 让 WeatherData 来实现
public interface Subject {
    public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObservers();
}
/**
* 类是核心
* 1. 包含最新的天气情况信息
* 2. 含有 观察者集合，使用 ArrayList 管理
* 3. 当数据有更新时，就主动的调用 ArrayList, 通知所有的（接入方）就看到最新的信息
* @author Administrator
*
*/
public class WeatherData implements Subject {
    private float temperatrue;
    private float pressure;
    private float humidity;
    //观察者集合
    private ArrayList<Observer> observers;
    //加入新的第三方
    public WeatherData() {
    observers = new ArrayList<Observer>();
    }
    public float getTemperature() {
    return temperatrue;
    }
    public float getPressure() {
    return pressure;
    }
    public float getHumidity() {
    return humidity;
    }
    public void dataChange() {
    //调用 接入方的 update
    notifyObservers();
    }
    //当数据有更新时，就调用 setData
    public void setData(float temperature, float pressure, float humidity) {
    this.temperatrue = temperature;
    this.pressure = pressure;
    this.humidity = humidity;
    //调用 dataChange， 将最新的信息 推送给 接入方 currentConditions
    dataChange();
    }
    //注册一个观察者
    @Override
    public void registerObserver(Observer o) {
    // TODO Auto-generated method stub
    observers.add(o);
    }
    //移除一个观察者
    @Override
    public void removeObserver(Observer o) {
    // TODO Auto-generated method stub
    if(observers.contains(o)) {
    observers.remove(o);
    }
    }
    //遍历所有的观察者，并通知
    @Override
    public void notifyObservers() {
    // TODO Auto-generated method stub
    for(int i = 0; i < observers.size(); i++) {
    observers.get(i).update(this.temperatrue, this.pressure, this.humidity);
    }
    }
}
public class BaiduSite implements Observer {
    // 温度，气压，湿度
    private float temperature;
    private float pressure;
    private float humidity;
    // 更新 天气情况，是由 WeatherData 来调用，我使用推送模式
    public void update(float temperature, float pressure, float humidity) {
    this.temperature = temperature
    this.pressure = pressure;
    this.humidity = humidity;
    display();
    }
    // 显示
    public void display() {
    System.out.println("===百度网站====");
    System.out.println("***百度网站 气温 : " + temperature + "***");
    System.out.println("***百度网站 气压: " + pressure + "***");
    System.out.println("***百度网站 湿度: " + humidity + "***");
    }
}
public class CurrentConditions implements Observer {
    // 温度，气压，湿度
    private float temperature;
       private float pressure;
    private float humidity;
    // 更新 天气情况，是由 WeatherData 来调用，我使用推送模式
    public void update(float temperature, float pressure, float humidity) {
    this.temperature = temperature;
    this.pressure = pressure;
    this.humidity = humidity;
    display();
    }
    // 显示
    public void display() {
    System.out.println("***Today mTemperature: " + temperature + "***");
    System.out.println("***Today mPressure: " + pressure + "***");
    System.out.println("***Today mHumidity: " + humidity + "***");
    }
}
public class Client {
    public static void main(String[] args) {
    // TODO Auto-generated method stub
    //创建一个 WeatherData
    WeatherData weatherData = new WeatherData();
    //创建观察者
    CurrentConditions currentConditions = new CurrentConditions();
    BaiduSite baiduSite = new BaiduSite();
    //注册到 weatherData
    weatherData.registerObserver(currentConditions);
    weatherData.registerObserver(baiduSite);
    //测试
    System.out.println("通知各个注册的观察者, 看看信息");
    weatherData.setData(10f, 100f, 30.3f);
    weatherData.removeObserver(currentConditions);
    //测试
    System.out.println();
    System.out.println("通知各个注册的观察者, 看看信息");
    weatherData.setData(10f, 100f, 30.3f);
    }
}
```

##### 小结

```
1) 观察者模式设计后，会以集合的方式来管理用户(Observer)，包括注册，移除和通知。
2) 这样，我们增加观察者(这里可以理解成一个新的公告板)，就不需要去修改核心类 WeatherData 不会修改代码，遵守了 ocp 原则。
```

#### 中介者模式

##### 示例

```java
/*
1) 智能家庭包括各种设备，闹钟、咖啡机、电视机、窗帘 等
2) 主人要看电视时，各个设备可以协同工作，自动完成看电视的准备工作，比如流程为：闹铃响起->咖啡机开始做咖啡->窗帘自动落下->电视机开始播放
*/
/* 基本介绍
基本介绍
1) 中介者模式（Mediator Pattern），用一个中介对象来封装一系列的对象交互。中介者使各个对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互
2) 中介者模式属于行为型模式，使代码易于维护
3) 比如 MVC 模式，C（Controller 控制器）是 M（Model 模型）和 V（View 视图）的中介者，在前后端交互时起到了中间人的作用
*/
//同事抽象类
public abstract class Colleague {
    private Mediator mediator;
    public String name;
    public Colleague(Mediator mediator, String name) {
    this.mediator = mediator;
    this.name = name;
    }
    public Mediator GetMediator() {
    return this.mediator;
    }
    public abstract void SendMessage(int stateChange);
}
public abstract class Mediator {
    //将给中介者对象，加入到集合中
    public abstract void Register(String colleagueName, Colleague colleague);
    //接收消息, 具体的同事对象发出
    public abstract void GetMessage(int stateChange, String colleagueName);
    public abstract void SendMessage();
}
public class TV extends Colleague {
    public TV(Mediator mediator, String name) {
    super(mediator, name);
    // TODO Auto-generated constructor stub
    mediator.Register(name, this);
    }
    @Override
    public void SendMessage(int stateChange) {
    // TODO Auto-generated method stub
    this.GetMediator().GetMessage(stateChange, this.name);
    }
    public void StartTv() {
    // TODO Auto-generated method stub
    System.out.println("It's time to StartTv!");
    }
    public void StopTv() {
    // TODO Auto-generated method stub
    System.out.println("StopTv!");
    }
}
public class Curtains extends Colleague {
    public Curtains(Mediator mediator, String name) {
    super(mediator, name);
    // TODO Auto-generated constructor stub
    mediator.Register(name, this);
    }
    @Override
    public void SendMessage(int stateChange) {
    // TODO Auto-generated method stub
    this.GetMediator().GetMessage(stateChange, this.name);
    }
    public void UpCurtains() {
    System.out.println("I am holding Up Curtains!");
    }
}
public class CoffeeMachine extends Colleague {
    public CoffeeMachine(Mediator mediator, String name) {
    super(mediator, name);
    // TODO Auto-generated constructor stub
    mediator.Register(name, this);
    }
    @Override
    public void SendMessage(int stateChange) {
    // TODO Auto-generated method stub
    this.GetMediator().GetMessage(stateChange, this.name);
    }
    public void StartCoffee() {
    System.out.println("It's time to startcoffee!");
    }
    public void FinishCoffee() {
    System.out.println("After 5 minutes!");
    System.out.println("Coffee is ok!");
    SendMessage(0);
    }
}
//具体的同事类
public class Alarm extends Colleague {
    //构造器
    public Alarm(Mediator mediator, String name){
    super(mediator, name);
    // TODO Auto-generated constructor stub
    //在创建 Alarm 同事对象时，将自己放入到 ConcreteMediator 对象中[集合]
    mediator.Register(name, this);
    }
    public void SendAlarm(int stateChange) {
    SendMessage(stateChange);
    }
    @Override
    public void SendMessage(int stateChange) {
    // TODO Auto-generated method stub
    //调用的中介者对象的 getMessage
    this.GetMediator().GetMessage(stateChange, this.name);
    }
}
//具体的中介者类
public class ConcreteMediator extends Mediator {
    //集合，放入所有的同事对象
    private HashMap<String, Colleague> colleagueMap;
    private HashMap<String, String> interMap;
    public ConcreteMediator() {
    colleagueMap = new HashMap<String, Colleague>();
    interMap = new HashMap<String, String>();
    }
    @Override
    public void Register(String colleagueName, Colleague colleague) {
    // TODO Auto-generated method stub
    colleagueMap.put(colleagueName, colleague);
    // TODO Auto-generated method stub
    if (colleague instanceof Alarm) {
    interMap.put("Alarm", colleagueName);
    } else if (colleague instanceof CoffeeMachine) {
    interMap.put("CoffeeMachine", colleagueName);
    } else if (colleague instanceof TV) {
    interMap.put("TV", colleagueName);
    } else if (colleague instanceof Curtains) {
    interMap.put("Curtains", colleagueName);
    }
    }
    //具体中介者的核心方法
    //1. 根据得到消息，完成对应任务
    //2. 中介者在这个方法，协调各个具体的同事对象，完成任务
    @Override
    public void GetMessage(int stateChange, String colleagueName) {
    // TODO Auto-generated method stub
    //处理闹钟发出的消息
    if (colleagueMap.get(colleagueName) instanceof Alarm) {
    if (stateChange == 0) {
    ((CoffeeMachine) (colleagueMap.get(interMap
    .get("CoffeeMachine")))).StartCoffee();
    ((TV) (colleagueMap.get(interMap.get("TV")))).StartTv();
    } else if (stateChange == 1) {
    ((TV) (colleagueMap.get(interMap.get("TV")))).StopTv();
    }
    } else if (colleagueMap.get(colleagueName) instanceof CoffeeMachine) {
    ((Curtains) (colleagueMap.get(interMap.get("Curtains"))))
    .UpCurtains();
    } else if (colleagueMap.get(colleagueName) instanceof TV) {//如果 TV 发现消息
    } else if (colleagueMap.get(colleagueName) instanceof Curtains) {
    //如果是以窗帘发出的消息，这里处理... }
    }
    @Override
    public void SendMessage() {
    // TODO Auto-generated method stub
    }
}
public class ClientTest {
    public static void main(String[] args) {
    //创建一个中介者对象
    Mediator mediator = new  ConcreteMediator();
    //创建 Alarm 并且加入到 ConcreteMediator 对象的 HashMap
    Alarm alarm = new Alarm(mediator, "alarm");
    //创建了 CoffeeMachine 对象，并 且加入到 ConcreteMediator 对象的 HashMap
    CoffeeMachine coffeeMachine = new CoffeeMachine(mediator, "coffeeMachine");
    //创建 Curtains , 并 且加入到 ConcreteMediator 对象的 HashMap
    Curtains curtains = new Curtains(mediator, "curtains");
    TV tV = new TV(mediator, "TV");
    //让闹钟发出消息
    alarm.SendAlarm(0);
    coffeeMachine.FinishCoffee();
    alarm.SendAlarm(1);
    }
}
```

##### 小结

```
1) 多个类相互耦合，会形成网状结构, 使用中介者模式将网状结构分离为星型结构，进行解耦
2) 减少类间依赖，降低了耦合，符合迪米特原则
3) 中介者承担了较多的责任，一旦中介者出现了问题，整个系统就会受到影响
4) 如果设计不当，中介者对象本身变得过于复杂，这点在实际使用时，要特别注意
```

#### 备忘录模式

##### 示例

```java
/*
游戏角色有攻击力和防御力，在大战 Boss 前保存自身的状态(攻击力和防御力)，当大战 Boss 后攻击力和防御力下降，从备忘录对象恢复到大战前的状态
对原理类图的说明-即(备忘录模式的角色及职责)
1) originator : 对象(需要保存状态的对象)
2) Memento ： 备忘录对象,负责保存好记录，即 Originator 内部状态
3) Caretaker: 守护者对象,负责保存多个备忘录对象， 使用集合管理，提高效率
4) 说明：如果希望保存多个 originator 对象的不同时间的状态，也可以，只需要要 HashMap <String, 集合
*/
/*
基本介绍
1) 备忘录模式（Memento Pattern）在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态
2) 可以这里理解备忘录模式：现实生活中的备忘录是用来记录某些要去做的事情，或者是记录已经达成的共同意见的事情，以防忘记了。而在软件层面，备忘录模式有着相同的含义，备忘录对象主要用来记录一个对象的某种状态，或者某些数据，当要做回退时，可以从备忘录对象里获取原来的数据进行恢复操作
3) 备忘录模式属于行为型模式
*/
public class Memento {
    private String state;
    //构造器
    public Memento(String state) {
    super();
    this.state = state;
    }
    public String getState() {
    return state;
    }
}
public class Originator {
    private String state;//状态信息
    public String getState() {
    return state;
    }
    public void setState(String state) {
    this.state = state;
    }
    //编写一个方法，可以保存一个状态对象 Memento
    //因此编写一个方法，返回 Memento
    public Memento saveStateMemento() {
    return new Memento(state);
    }
    //通过备忘录对象，恢复状态
    public void getStateFromMemento(Memento memento) {
    state = memento.getState();
    }
}


public class Caretaker {
    //在 List 集合中会有很多的备忘录对象
    private List<Memento> mementoList = new ArrayList<Memento>();
    public void add(Memento memento) {
    mementoList.add(memento);
    }
    //获取到第 index 个 Originator 的 备忘录对象(即保存状态)
    public Memento get(int index) {
    return mementoList.get(index);
    }
}
public class Client {
    public static void main(String[] args) {
    // TODO Auto-generated method stub
    Originator originator = new Originator();
    Caretaker caretaker = new Caretaker();
    originator.setState(" 状态#1 攻击力 100 ");
    //保存了当前的状态
    caretaker.add(originator.saveStateMemento());
    originator.setState(" 状态#2 攻击力 80 ");
    caretaker.add(originator.saveStateMemento());
    originator.setState(" 状态#3 攻击力 50 ");
    caretaker.add(originator.saveStateMemento());
    System.out.println("当前的状态是 =" + originator.getState());
    //希望得到状态 1, 将 originator 恢复到状态 1
    originator.getStateFromMemento(caretaker.get(0));
    System.out.println("恢复到状态 1System.out.println("当前的状态是 =" + originator.getState());
    }
}
```

##### 小结

```
1) 给用户提供了一种可以恢复状态的机制，可以使用户能够比较方便地回到某个历史的状态
2) 实现了信息的封装，使得用户不需要关心状态的保存细节
3) 如果类的成员变量过多，势必会占用比较大的资源，而且每一次保存都会消耗一定的内存, 这个需要注意
4) 适用的应用场景：1、后悔药。 2、打游戏时的存档。 3、Windows 里的 ctri + z。 4、IE 中的后退。 4、数据库的事务管理
```

#### 解释器模式（Interpreter 模式）

##### 示例

```java
/*
通过解释器模式来实现四则运算，如计算 a+b-c 的值，具体要求
1) 先输入表达式的形式，比如 a+b+c-d+e, 要求表达式的字母不能重复
2) 在分别输入 a ,b, c, d,e的值
*/
/*
基本介绍
1) 在编译原理中，一个算术表达式通过词法分析器形成词法单元，而后这些词法单元再通过语法分析器构建语法分析树，最终形成一颗抽象的语法分析树。这里的词法分析器和语法分析器都可以看做是解释器
2) 解释器模式（Interpreter Pattern）：是指给定一个语言(表达式)，定义它的文法的一种表示，并定义一个解释器，使用该解释器来解释语言中的句子(表达式)
3) 应用场景
-应用可以将一个需要解释执行的语言中的句子表示为一个抽象语法树
-一些重复出现的问题可以用一种简单的语言来表达
-一个简单语法需要解释的场景
4) 这样的例子还有，比如编译器、运算表达式计算、正则表达式、机器人等
1) Context: 是环境角色,含有解释器之外的全局信息. 
2) AbstractExpression: 抽象表达式， 声明一个抽象的解释操作,这个方法为抽象语法树中所有的节点所共享
3) TerminalExpression: 为终结符表达式, 实现与文法中的终结符相关的解释操作
4) NonTermialExpression: 为非终结符表达式，为文法中的非终结符实现解释操作. 
5) 说明： 输入 Context he TerminalExpression 信息通过 Client 
*/
/**
* 抽象类表达式，通过 HashMap 键值对, 可以获取到变量的值
*
* @author Administrator
*
*/
public abstract class Expression {
    // a + b - c
    // 解释公式和数值, key 就是公式(表达式) 参数[a,b,c], value 就是就是具体值
    // HashMap {a=10, b=20}
    public abstract int interpreter(HashMap<String, Integer> var);
}
public class SubExpression extends SymbolExpression {
    public SubExpression(Expression left, Expression right) 
    super(left, right);
    }
    //求出 left 和 right 表达式相减后的结果
    public int interpreter(HashMap<String, Integer> var) {
    return super.left.interpreter(var) - super.right.interpreter(var);
    }
}
/**
* 抽象运算符号解析器 这里，每个运算符号，都只和自己左右两个数字有关系，
* 但左右两个数字有可能也是一个解析的结果，无论何种类型，都是 Expression 类的实现类
*
* @author Administrator
*
*/
public class SymbolExpression extends Expression {
    protected Expression left;
    protected Expression right;
    public SymbolExpression(Expression left, Expression right) {
    this.left = left;
    this.right = right;
    }
    //因为 SymbolExpression 是让其子类来实现，因此 interpreter 是一个默认实现
    @Override
    public int interpreter(HashMap<String, Integer> var) {
    // TODO Auto-generated method stub
    return 0;
    }
}
/**
* 变量的解释器
* @author Administrator
*
*/
public class VarExpression extends Expression {
    private String key; // key=a,key=b,key=c
    public VarExpression(String key) {
    this.key = key;
    }
    // var 就是{a=10, b=20}
    // interpreter 根据 变量名称，返回对应值
    @Override
    public int interpreter(HashMap<String, Integer> var) {
    return var.get(this.key);
    }
}
/**
* 加法解释器
* @author Administrator
*
*/
public class AddExpression extends SymbolExpression {
    public AddExpression(Expression left, Expression right) {
    super(left, right);
    }
    //处理相加
    //var 仍然是 {a=10,b=20}.. //一会我们 debug 源码,就 ok
    public int interpreter(HashMap<String, Integer> var) {
    //super.left.interpreter(var) ： 返回 left 表达式对应的值 a = 10
    //super.right.interpreter(var): 返回 right 表达式对应值 b = 20
    return super.left.interpreter(var) + super.right.interpreter(var);
    }
}
public class Calculator {
    // 定义表达式
    private Expression expression;
    // 构造函数传参，并解析
    public Calculator(String expStr) { // expStr = a+b
    // 安排运算先后顺序
    Stack<Expression> stack = new Stack<>();
    // 表达式拆分成字符数组
    char[] charArray = expStr.toCharArray();// [a, +, b]
    Expression left = null;
    Expression right = null;
    //遍历我们的字符数组， 即遍历 [a, +, b]
    //针对不同的情况，做处理
    for (int i = 0; i < charArray.length; i++) {
    switch (charArray[i]) {
    case '+': //
    left = stack.pop();// 从 stack 取出 left => "a"
    right = new VarExpression(String.valueOf(charArray[++i]));// 取出右表达式 "b"
    stack.push(new AddExpression(left, right));// 然后根据得到 left 和 right 构建 AddExpresson 加入
    stack
    break;
    case '-': //
    left = stack.pop();
    right = new VarExpression(String.valueOf(charArray[++i]));
    stack.push(new SubExpression(left, right));
    break;
    default:
    //如果是一个 Var 就创建要给 VarExpression 对象，并 push 到 stack
    stack.push(new VarExpression(String.valueOf(charArray[i])));
    break;
    }
    }
    //当遍历完整个 charArray 数组后，stack 就得到最后 Expression
    this.expression = stack.pop();
    }
    public int run(HashMap<String, Integer> var) {
    //最后将表达式 a+b 和 var = {a=10,b=20}
    //然后传递给 expression 的 interpreter 进行解释执行
    return this.expression.interpreter(var);
    }
}
public class ClientTest {
    public static void main(String[] args) throws IOException {
    // TODO Auto-generated method stub
    String expStr = getExpStr(); // a+b
    HashMap<String, Integer> var = getValue(expStr);// var {a=10, b=20}
    Calculator calculator = new Calculator(expStr);
    System.out.println("运算结果：" + expStr + "=" + calculator.run(var));
    }
    // 获得表达式
    public static String getExpStr() throws IOException {
    System.out.print("请输入表达式：");
    return (new BufferedReader(new InputStreamReader(System.in))).readLine();
    }
    // 获得值映射
    public static HashMap<String, Integer> getValue(String expStr) throws IOException {
    HashMap<String, Integer> map = new HashMap<>();
    for (char ch : expStr.toCharArray()) {
    if (ch != '+' && ch != '-') {
    if (!map.containsKey(String.valueOf(ch))) {
    System.out.print("请输入" + String.valueOf(ch) + "的值：");
    String in = (new BufferedReader(new InputStreamReader(System.in))).readLine();
    map.put(String.valueOf(ch), Integer.valueOf(in));
    }
    }
    }
    return map;
    }
}
```

##### 小结

```
1) 当有一个语言需要解释执行，可将该语言中的句子表示为一个抽象语法树，就可以考虑使用解释器模式，让程
序具有良好的扩展性
2) 应用场景：编译器、运算表达式计算、正则表达式、机器人等
3) 使用解释器可能带来的问题：解释器模式会引起类膨胀、解释器模式采用递归调用方法，将会导致调试非常复杂、效率可能降低
```

#### 状态模式

##### 示例

```java
/*
请编写程序完成 APP 抽奖活动 具体要求如下:
1) 假如每参加一次这个活动要扣除用户 50 积分，中奖概率是 10%
2) 奖品数量固定，抽完就不能抽奖
3) 活动有四个状态: 可以抽奖、不能抽奖、发放奖品和奖品领完
*/
/*
基本介绍
1) 状态模式（State Pattern）：它主要用来解决对象在多种状态转换时，需要对外输出不同的行为的问题。状态
和行为是一一对应的，状态之间可以相互转换
2) 当一个对象的内在状态改变时，允许改变其行为，这个对象看起来像是改变了其类
 Context 类为环境角色, 用于维护 State 实例,这个实例定义当前状态
 State 是抽象状态角色,定义一个接口封装与 Context 的一个特点接口相关行为
 ConcreteState 具体的状态角色，每个子类实现一个与 Context 的一个状态相关行
*/
/**
* 状态抽象类
* @author Administrator
*
*/
public abstract class State {
    // 扣除积分 - 50
    public abstract void deductMoney();
    // 是否抽中奖品
    public abstract boolean raffle();
    // 发放奖品
    public abstract void dispensePrize();
    }

/**
* 抽奖活动 //
*
* @author Administrator
*
*/
public class RaffleActivity {
    // state 表示活动当前的状态，是变化
    State state = null;
        // 奖品数量
    int count = 0;
    // 四个属性，表示四种状态
    State noRafflleState = new NoRaffleState(this);
    State canRaffleState = new CanRaffleState(this);
    State dispenseState = new DispenseState(this);
    State dispensOutState = new DispenseOutState(this);
    //构造器
    //1. 初始化当前的状态为 noRafflleState（即不能抽奖的状态）
    //2. 初始化奖品的数量
    public RaffleActivity( int count) {
    this.state = getNoRafflleState();
    this.count = count;
    }
    //扣分, 调用当前状态的 deductMoney
    public void debuctMoney(){
    state.deductMoney();
    }
    //抽奖
    public void raffle(){
    // 如果当前的状态是抽奖成功
    if(state.raffle()){
    //领取奖品
    state.dispensePrize();
    }
    }
    public State getState() {
    return state;
    }
    public void setState(State state) {
    this.state = state;
    }
    //这里请大家注意，每领取一次奖品，count-- public int getCount() {
    int curCount = count;
    count--;
    return curCount;
    }
    public void setCount(int count) {
    this.count = count;
    }
    public State getNoRafflleState() {
    return noRafflleState;
    }
    public void setNoRafflleState(State noRafflleState) {
    this.noRafflleState = noRafflleState;
    }
    public State getCanRaffleState() {
    return canRaffleState;
    }
    public void setCanRaffleState(State canRaffleState) {
    this.canRaffleState = canRaffleState;
    }
    public State getDispenseState() {
    return dispenseState;
    }
    public void setDispenseState(State dispenseState) {
    this.dispenseState = dispenseState;
    }
    public State getDispensOutState() {
    return dispensOutState;
        }
    public void setDispensOutState(State dispensOutState) {
    this.dispensOutState = dispensOutState;
    }
}
/**
* 不能抽奖状态
* @author Administrator
*
*/
public class NoRaffleState extends State {
    // 初始化时传入活动引用，扣除积分后改变其状态
    RaffleActivity activity;
    public NoRaffleState(RaffleActivity activity) {
    this.activity = activity;
    }
    // 当前状态可以扣积分 , 扣除后，将状态设置成可以抽奖状态
    @Override
    public void deductMoney() {
    System.out.println("扣除 50 积分成功，您可以抽奖了");
    activity.setState(activity.getCanRaffleState());
    }
    // 当前状态不能抽奖
    @Override
    public boolean raffle() {
    System.out.println("扣了积分才能抽奖喔！");
    return false;
    }
    // 当前状态不能发奖品
    @Override
    public void dispensePrize() {
    System.out.println("不能发放奖品");
    }
}
/**
* 奖品发放完毕状态
* 说明，当我们 activity 改变成 DispenseOutState， 抽奖活动结束
* @author Administrator
*
*/
public class DispenseOutState extends State {
    // 初始化时传入活动引用
    RaffleActivity activity;
    public DispenseOutState(RaffleActivity activity) {
    this.activity = activity;
    }
    @Override
    public void deductMoney() {
    System.out.println("奖品发送完了，请下次再参加");
    }
    @Override
    public boolean raffle() {
    System.out.println("奖品发送完了，请下次再参加");
    return false;
    }
    @Override
    public void dispensePrize() {
    System.out.println("奖品发送完了，请下次再参加");
    }
}
/**
* 发放奖品的状态
* @author Administrator
*
*/
public class DispenseState extends State {
    // 初始化时传入活动引用，发放奖品后改变其状态
    RaffleActivity activity;
    public DispenseState(RaffleActivity activity) {
    this.activity = activity;
    }
    //
    @Override
    public void deductMoney() {
    System.out.println("不能扣除积分");
    }
    @Override
    public boolean raffle() {
    System.out.println("不能抽奖");
    return false;
    }
    //发放奖品
    @Override
    public void dispensePrize() {
    if(activity.getCount() > 0){
    System.out.println("恭喜中奖了");
    // 改变状态为不能抽奖
    activity.setState(activity.getNoRafflleState());
    }else{
    System.out.println("很遗憾，奖品发送完了");
    // 改变状态为奖品发送完毕, 后面我们就不可以抽奖
    activity.setState(activity.getDispensOutState());
    //System.out.println("抽奖活动结束");
    //System.exit(0);
    }
    }
}
/**
* 可以抽奖的状态
* @author Administrator
*
*/
public class CanRaffleState extends State {
    RaffleActivity activity;
    public CanRaffleState(RaffleActivity activity) {
    this.activity = activity;
    }
    //已经扣除了积分，不能再扣
    @Override
    public void deductMoney() {
    System.out.println("已经扣取过了积分");
    }
    //可以抽奖, 抽完奖后，根据实际情况，改成新的状态
    @Override
    public boolean raffle() {
    System.out.println("正在抽奖，请稍等！");
    Random r = new Random();
    int num = r.nextInt(10);
    // 10%中奖机会
    if(num == 0){
    // 改变活动状态为发放奖品 context
    activity.setState(activity.getDispenseState());
    return true;
    }else{
    System.out.println("很遗憾没有抽中奖品！");
    // 改变状态为不能抽奖
    activity.setState(activity.getNoRafflleState());
    return false;
    }
    }
    // 不能发放奖品
    @Override
    public void dispensePrize() {
    System.out.println("没中奖，不能发放奖品");
    }
}
/**
* 状态模式测试类
* @author Administrator
*
*/
public class ClientTest {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        // 创建活动对象，奖品有 1 个奖品
        RaffleActivity activity = new RaffleActivity(1);
        // 我们连续抽 300 次奖
        for (int i = 0; i < 30; i++) {
        System.out.println("--------第" + (i + 1) + "次抽奖----------");
        // 参加抽奖，第一步点击扣除积分
        activity.debuctMoney();
        // 第二步抽奖
        activity.raffle();
        }
    }
}
```

##### 小结

```
1) 代码有很强的可读性。状态模式将每个状态的行为封装到对应的一个类中
2) 方便维护。将容易产生问题的 if-else 语句删除了，如果把每个状态的行为都放到一个类中，每次调用方法时都
要判断当前是什么状态，不但会产出很多 if-else 语句，而且容易出错
3) 符合“开闭原则”。容易增删状态
4) 会产生很多类。每个状态都要一个对应的类，当状态过多时会产生很多类，加大维护难度
5) 应用场景：当一个事件或者对象有很多种状态，状态之间会相互转换，对不同的状态要求有不同的行为的时候，可以考虑使用状态模式
```

#### 策略模式

##### 示例

```java
/*
1) 有各种鸭子(比如 野鸭、北京鸭、水鸭等， 鸭子有各种行为，比如 叫、飞行等)
2) 显示鸭子的信息
*/
/*
1) 策略模式（Strategy Pattern）中，定义算法族（策略组），分别封装起来，让他们之间可以互相替换，此模式让算法的变化独立于使用算法的客户
2) 这算法体现了几个设计原则，第一、把变化的代码从不变的代码中分离出来；第二、针对接口编程而不是具体类（定义了策略接口）；第三、多用组合/聚合，少用继承（客户通过组合方式使用策略）。
*/
public interface FlyBehavior {
    void fly(); // 子类具体实现
}
public class GoodFlyBehavior implements FlyBehavior {
    @Override
    public void fly() {
    // TODO Auto-generated method stub
    System.out.println(" 飞翔技术高超 ~~~");
    }
}
public class NoFlyBehavior implements FlyBehavior{
    @Override
    public void fly() {
    // TODO Auto-generated method stub
    System.out.println(" 不会飞翔 ");
    }
}
public class BadFlyBehavior implements FlyBehavior {
    @Override
    public void fly() {
    // TODO Auto-generated method stub
    System.out.println(" 飞翔技术一般 ");
    }
}
public abstract class Duck {
    //属性, 策略接口
    FlyBehavior flyBehavior;
    //其它属性<->策略接口
    QuackBehavior quackBehavior;
    public Duck() {
    }
    public abstract void display();//显示鸭子信息
    public void quack() {
    System.out.println("鸭子嘎嘎叫~~");
    }
    public void swim() {
    System.out.println("鸭子会游泳~~");
    }
    public void fly() {
    //改进
    if(flyBehavior != null) {
    flyBehavior.fly();
    }
    }
    public void setFlyBehavior(FlyBehavior flyBehavior) {
    this.flyBehavior = flyBehavior;
    }
    public void setQuackBehavior(QuackBehavior quackBehavior) {
    this.quackBehavior = quackBehavior;
    }
}
public class PekingDuck extends Duck {
    //假如北京鸭可以飞翔，但是飞翔技术一般
    public PekingDuck() {
    // TODO Auto-generated constructor stub
    flyBehavior = new BadFlyBehavior();
    }
    @Override
    public void display() {
    // TODO Auto-generated method stub
    System.out.println("~~北京鸭~~~");
    }
}
public class WildDuck extends Duck {
    //构造器，传入 FlyBehavor 的对象
    public WildDuck() {
    // TODO Auto-generated constructor stub
    flyBehavior = new GoodFlyBehavior();
    }
    @Override
    public void display() {
    // TODO Auto-generated method stub
    System.out.println(" 这是野鸭 ");
    }
}

public class ToyDuck extends Duck{
    public ToyDuck() {
        // TODO Auto-generated constructor stub
        flyBehavior = new NoFlyBehavior();
    }
    @Override
    public void display() {
    // TODO Auto-generated method stub
    System.out.println("玩具鸭");
    }
    //需要重写父类的所有方法
    public void quack() {
    System.out.println("玩具鸭不能叫~~");
    }
    public void swim() {
    System.out.println("玩具鸭不会游泳~~");
    }
}
public class Client {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        WildDuck wildDuck = new WildDuck();
        wildDuck.fly();//
        ToyDuck toyDuck = new ToyDuck();
        toyDuck.fly();
        PekingDuck pekingDuck = new PekingDuck();
        pekingDuck.fly();
        //动态改变某个对象的行为, 北京鸭 不能飞
        pekingDuck.setFlyBehavior(new NoFlyBehavior());
        System.out.println("北京鸭的实际飞翔能力");
        pekingDuck.fly();
        }
}
```

##### 小结

```java
1) 策略模式的关键是：分析项目中变化部分与不变部分
2) 策略模式的核心思想是：多用组合/聚合 少用继承；用行为类组合，而不是行为的继承。更有弹性
3) 体现了“对修改关闭，对扩展开放”原则，客户端增加行为不用修改原有代码，只要添加一种策略（或者行为）
即可，避免了使用多重转移语句（if..else if..else）
4) 提供了可以替换继承关系的办法： 策略模式将算法封装在独立的 Strategy 类中使得你可以独立于其 Context 改变它，使它易于切换、易于理解、易于扩展
5) 需要注意的是：每添加一个策略就要增加一个类，当策略过多是会导致类数目庞
```

#### 职责链模式(责任链模式)

##### 示例

```java
/*
采购员采购教学器材
1) 如果金额 小于等于 5000, 由教学主任审批 （0<=x<=5000）
2) 如果金额 小于等于 10000, 由院长审批 (5000<x<=10000)
3) 如果金额 小于等于 30000, 由副校长审批 (10000<x<=30000)
4) 如果金额 超过 30000 以上，有校长审批 ( 30000<x)
请设计程序完成采购审批项目
*/
/*
基本介绍
1) 职责链模式（Chain of Responsibility Pattern）, 又叫 责任链模式，为请求创建了一个接收者对象的链。这种模式对请求的发送者和接收者进行解耦。
2) 职责链模式通常每个接收者都包含对另一个接收者的引用。如果一个对象不能处理该请求，那么它会把相同的请求传给下一个接收者，依此类推。
3) 这种类型的设计模式属于行为型模式
*/
//请求类
public class PurchaseRequest {
    private int type = 0; //请求类型
    private float price = 0.0f; //请求金额
    private int id = 0;
    //构造器
    public PurchaseRequest(int type, float price, int id) {
    this.type = type;
    this.price = price;
    this.id = id
    }
    public int getType() {
    return type;
    }
    public float getPrice() {
    return price;
    }
    public int getId() {
    return id;
}
}
public abstract class Approver {
    Approver approver; //下一个处理者
    String name; // 名字
    public Approver(String name) {
    // TODO Auto-generated constructor stub
    this.name = name;
    }
    //下一个处理者
    public void setApprover(Approver approver) {
    this.approver = approver;
    }
    //处理审批请求的方法，得到一个请求, 处理是子类完成，因此该方法做成抽象
    public abstract void processRequest(PurchaseRequest purchaseRequest);
}
public class SchoolMasterApprover extends Approver {
    public SchoolMasterApprover(String name) {
    // TODO Auto-generated constructor stub
    super(name);
    }
    @Override
    public void processRequest(PurchaseRequest purchaseRequest) {
    // TODO Auto-generated method stub
    if(purchaseRequest.getPrice() > 30000) {
    System.out.println(" 请求编号 id= " + purchaseRequest.getId() + " 被 " + this.name + " 处理");
    }else {
    approver.processRequest(purchaseRequest);
    }
    }
}
public class CollegeApprover extends Approver {
    public CollegeApprover(String name) {
    // TODO Auto-generated constructor stub
    super(name);
    }
    @Override
    public void processRequest(PurchaseRequest purchaseRequest) {
    // TODO Auto-generated method stub
    if(purchaseRequest.getPrice() < 5000 && purchaseRequest.getPrice() <= 10000) {
    System.out.println(" 请求编号 id= " + purchaseRequest.getId() + " 被 " + this.name + " 处理");
    }else {
    approver.processRequest(purchaseRequest);
    }
    }
}
public class DepartmentApprover extends Approver {
    public DepartmentApprover(String name) {
    // TODO Auto-generated constructor stub
    super(name);
    }
    @Override
    public void processRequest(PurchaseRequest purchaseRequest) {
    // TODO Auto-generated method stub
    if(purchaseRequest.getPrice() <= 5000) {
    System.out.println(" 请求编号 id= " + purchaseRequest.getId() + " 被 " + this.name + " 处理");
    }else {
    approver.processRequest(purchaseRequest);
    }
    }
}
public class ViceSchoolMasterApprover extends Approver {
    public ViceSchoolMasterApprover(String name) {
    // TODO Auto-generated constructor stub
    super(name);
    }
    @Override
    public void processRequest(PurchaseRequest purchaseRequest) {
    // TODO Auto-generated method stub
    if(purchaseRequest.getPrice() < 10000 && purchaseRequest.getPrice() <= 30000) {
    System.out.println(" 请求编号 id= " + purchaseRequest.getId() + " 被 " + this.name + " 处理");
    }else {approver.processRequest(purchaseRequest);
    }
    }
}
public class Client {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        //创建一个请求
        PurchaseRequest purchaseRequest = new PurchaseRequest(1,31000,1)
                                                             //创建相关的审批人
        DepartmentApprover departmentApprover = new DepartmentApprover("张主任");
        CollegeApprover collegeApprover = new CollegeApprover("李院长");
        ViceSchoolMasterApprover viceSchoolMasterApprover = new ViceSchoolMasterApprover("王副校");
        SchoolMasterApprover schoolMasterApprover = new SchoolMasterApprover("佟校长");
        //需要将各个审批级别的下一个设置好 (处理人构成环形: )
        departmentApprover.setApprover(collegeApprover);
        collegeApprover.setApprover(viceSchoolMasterApprover);
        viceSchoolMasterApprover.setApprover(schoolMasterApprover);
        schoolMasterApprover.setApprover(departmentApprover);
        departmentApprover.processRequest(purchaseRequest);
        viceSchoolMasterApprover.processRequest(purchaseRequest);
    }
}
```

##### 小结

```java
1) 将请求和处理分开，实现解耦，提高系统的灵活性
2) 简化了对象，使对象不需要知道链的结构
3) 性能会受到影响，特别是在链比较长的时候，因此需控制链中最大节点数量，一般通过在 Handler 中设置一个最大节点数量，在 setNext()方法中判断是否已经超过阀值，超过则不允许该链建立，避免出现超长链无意识地破坏系统性能
4) 调试不方便。采用了类似递归的方式，调试时逻辑可能比较复杂
5) 最佳应用场景：有多个对象可以处理同一个请求时，比如：多级请求、请假/加薪等审批流程、Java Web 中 Tomcat对 Encoding 的处理、拦截
```
