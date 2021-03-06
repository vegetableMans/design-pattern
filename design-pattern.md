# 设计模式

## 一、策略模式  --行为型模式

**面对不同的情况选用不同的策略方案**

**在策略模式（Strategy Pattern）中，一个类的行为或其算法可以在运行时更改。这种类型的设计模式属于行为型模式。**

**在策略模式中，我们创建表示各种策略的对象和一个行为随着策略对象改变而改变的 context 对象。策略对象改变 context 对象的执行算法**

- 使用场景
  
  - 一个系统中有很多类，他们的区别仅在于他们的行为，可以使用策略模式动态地让一个对象在许多行为中选择一种。
  
  - 一个系统需要动态地在几种算法中选择一种。

- 图示：
  ![](http://119.3.236.138:9090/images/pic1.png)

- 栗子
  
  - 接口:
    
    ```java
    public interface Strategy {
    
        int doOpreation(int a,int b);
    }
    ```
  
  - 不同行为：
    
    ```java
    public class SubStrategy implements Strategy {
        @Override
        public int doOpreation(int a, int b) {
            return a-b;
        }
    }
    ```
    
    ```java
    public class AddStrategy implements Strategy {
        @Override
        public int doOpreation(int a, int b) {
            return a+b;
        }
    }
    ```
  
  - 具体运行时：
    
    ```java
    public class StrategyDemo {
        public static void main(String[] args) {
            Context context = new Context(new AddStrategy());
            Context context1 = new Context(new SubStrategy());
            System.out.println(context.executeStrategy(1,2));
            System.out.println(context1.executeStrategy(1,2));
        }
    }
    ```

## 二、观察者模式  --行为型模式

**对象之间存在一对多的关系，例如，当一个对象的状态改变时，所有依赖于它的对象都得到通知并被自动更新**

- 三个类
  
  - Subject 带有绑定观察者到 Client 对象和从 Client 对象解绑观察者的方法
  
  - Observer 观察者
  
  - Client  观察对象

- ![](https://www.runoob.com/wp-content/uploads/2014/08/observer_pattern_uml_diagram.jpg)

- 栗子：

```java
public class Subject {
    /**
     * 储存所有的观察者
     */
    private List<Observer> observers = new ArrayList<>();


    private int state;


    public int getState() {
        return state;
    }

    public void setState(int state) {
        this.state = state;
        notifyAllObservers();
    }
    public void attach(Observer observer){
        observers.add(observer);
    }
    public void notifyAllObservers(){
        for (Observer observer : observers){
            observer.update();
        }
    }
}
```

```java
public abstract class Observer {

    protected Subject subject;

    /**
     * 观察者收到变化后的行为
     */
    public abstract void update();

}
```

```java
public class FirstObserver extends Observer {

    public FirstObserver(Subject subject) {
        this.subject = subject;
        subject.attach(this);
    }

    @Override
    public void update() {
        System.out.println("firstObserver knows："+subject.getState());
    }
}
```

```java
public class SecondObserver extends Observer {

    public SecondObserver(Subject subject) {
        this.subject = subject;
        subject.attach(this);
    }

    @Override
    public void update() {
        System.out.println("secondObserver knows："+subject.getState());
    }
}
```

```java
public class ObserverDemo {
    public static void main(String[] args) {
        Subject subject = new Subject();
        FirstObserver firstObserver = new FirstObserver(subject);
        SecondObserver secondObserver = new SecondObserver(subject);
        subject.setState(200);
    }
}
```

- 结果：
  ![](http://119.3.236.138:9090/images/2020-07-05-11-15-09-image.png)

## 三、单例模式  --创建型模式

使用场景：控制实例数目；节省系统资源

- 单例类只能有一个实例****

- 单例类必须自己创建自己的唯一实例

- 单例类必须给所有对象提供这一实例  通过静态方法

**单例模式实现的关键：<mark>构造函数是私有的</mark>**

![](https://www.runoob.com/wp-content/uploads/2014/08/62576915-36E0-4B67-B078-704699CA980A.jpg)

栗子：

```java
public class SingletonObject {

    /**
     * 唯一实例
     */
    private static SingletonObject instance = new SingletonObject();

    private int data;

    /**
     * 私有的构造方法
     */
    private SingletonObject(){

    }

    /**
     * 获取唯一实例
     */

    public static SingletonObject getInstance(){
        return instance;
    }

    public void setData(int data) {
        this.data = data;
    }

    public void showData(){
        System.out.println("data："+data);
    }
}
```

```java
public class SingletonDemo {
    public static void main(String[] args) {
        SingletonObject objectOne = SingletonObject.getInstance();
        objectOne.setData(1);
        SingletonObject objectTwo = SingletonObject.getInstance();
        objectTwo.setData(2);
        objectOne.showData();
        objectTwo.showData();
    }
}
```

运行结果：

![](http://119.3.236.138:9090/images/2020-07-05-11-38-31-image.png)

**objectOne 和 objectTwo showData() 的结果是一样的，说明他们引用的确实是同一个对象**

### 3.2 单例模式的几种实现方式

#### 1、懒汉式 线程不安全

```java
public class Singleton {
    private static Singleton instance;
    private Singleton(){};

    public static Singleton getInstance(){
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

#### 2、懒汉式 线程安全

```java
public class Singleton {
    private static Singleton instance;
    private Singleton(){};

    public static synchronized Singleton getInstance(){
       if (instance == null) {  
           instance = new Singleton();  
       } 
        return instance;
    }
}
```

**关键字synchronized可以保证在同一时刻，只有一个线程可以执行某个方法或某个代码块，同时synchronized可以保证一个线程的变化可见（可见性），即可以代替volatile。**

#### 3、饿汉式 线程安全

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}
```

#### 4、双检锁/双重校验锁（DCL）lazy初始化 线程安全

```java
public class Singleton {  
    private volatile static Singleton singleton;  
    private Singleton (){}  
    public static Singleton getSingleton() {  
    if (singleton == null) {  
        synchronized (Singleton.class) {  
        if (singleton == null) {  
            singleton = new Singleton();  
        }  
        }  
    }  
    return singleton;  
    }  
}
```

#### 5、登记式/静态内部类  lazy初始化 线程安全

```java
public class Singleton {  
    private static class SingletonHolder {  
    private static final Singleton INSTANCE = new Singleton();  
    }  
    private Singleton (){}  
    public static final Singleton getInstance() {  
    return SingletonHolder.INSTANCE;  
    }  
}
```

#### 6、枚举  线程安全但不是lazy初始化

```java
public enum Singleton {  
    INSTANCE;  
    public void whateverMethod() {  
    }  
}
```

## 四、装饰者模式  --结构型模式

**向一个现有的对象添加新的功能，同时又不改变其结构**

怎么感觉有点像代理呢...

![](https://www.runoob.com/wp-content/uploads/2014/08/decorator_pattern_uml_diagram.jpg)

**看下面的例子理解装饰器模式**

```java
public interface Shape {
    void draw();
}
```

```java
public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("shape : circle");
    }
}
```

```java
public class Square implements Shape {
    @Override
    public void draw() {
        System.out.println("shape : square");
    }
}
```

```java
public abstract class ShapeDecorator implements Shape {
    protected Shape DecoratedShape;

    public ShapeDecorator(Shape shape){
        this.DecoratedShape = shape;
    }


}
```

```java
public class CircleDecorator extends ShapeDecorator {

  public CircleDecorator(Circle shape){
      super(shape);
  }

    @Override
    public void draw() {
        DecoratedShape.draw();
        System.out.println("decorator operation...");
    }
}
```

```java
public class SquareDecorator extends ShapeDecorator {

    public SquareDecorator(Square shape) {
        super(shape);
    }

    @Override
    public void draw() {
        DecoratedShape.draw();
        System.out.println("decorator operation...");
    }
}
```

****

```java
public class DecoratorDemo {
    public static void main(String[] args) {
        Circle circle = new Circle();
        circle.draw();
        CircleDecorator circleDecorator = new CircleDecorator(new Circle());
        SquareDecorator squareDecorator = new SquareDecorator(new Square());
        circleDecorator.draw();
        squareDecorator.draw();
    }
}
```

- 结果：

![](http://119.3.236.138:9090/images/2020-07-05-19-11-00-image.png)

这个装饰器模式的例子我觉得举得太复杂了

## 五、适配器模式  --结构型模式

**使用第三个类作为两个不兼容的接口之间的桥梁，核心就是在第三个类中添加一个需要的功能所在的接口的成员变量**                                 两个字概括就是<mark>依赖</mark>

- 何时使用：
  
  - 系统需要使用现有的类，而此类的接口不符合要求

![](https://www.runoob.com/wp-content/uploads/2014/08/adapter_pattern_uml_diagram.jpg)

栗子：

```java
/**
 * 只能播放MP3格式
 */
public interface MediaPlayer {
    void playMp3(String audioType,String fileName);
}
```

```java
/**
 * 可以播放mp4 格式
 */
public interface AdvancedMediaPlayer {
    void playMp4(String fileName);
}
```

```java
public class Mp3Player implements MediaPlayer {
    @Override
    public void playMp3(String audioType, String fileName) {
        System.out.println("playing mp3 : "+ fileName);
    }
}
```

```java
public class Mp4Player implements AdvancedMediaPlayer{
    @Override
    public void playMp4(String fileName) {
        System.out.println("playing mp4 : "+fileName);
    }
}
```

```java
public class MediaAdapter  implements MediaPlayer{

    private AdvancedMediaPlayer advancedMediaPlayer;

    @Override
    public void playMp3(String audioType, String fileName) {
        if(audioType.equals("mp3")){
            System.out.println("playing mp3 : "+ fileName);
        }
        else if(audioType.equals("mp4")){
            advancedMediaPlayer = new Mp4Player();
            advancedMediaPlayer.playMp4(fileName);
        }
    }
}
```

```java
public class AdapterDemo {
    public static void main(String[] args) {
        Mp3Player mp3Player = new Mp3Player();
        mp3Player.playMp3("mp3","不浪漫罪名");
        Mp4Player mp4Player = new Mp4Player();
        mp4Player.playMp4("速度与激情9");
        MediaAdapter mediaAdapter = new MediaAdapter();
        mediaAdapter.playMp3("mp3","不浪漫罪名");
        mediaAdapter.playMp3("mp4","速度与激情9");
    }
}
```

结果：

![](http://119.3.236.138:9090/images/2020-07-06-10-48-01-image.png)

**mediaAdapter只实现了mediaPlayer接口 但是仍然可以播放 MP4文件 就是因为采用了适配器模式 将AdvancedMediaPlayer接口的功能加了进来**

## 六、桥接模式  --结构型模式

![](https://img2018.cnblogs.com/blog/1475571/201901/1475571-20190112180712208-505786819.png)

还不是很懂

**桥接模式有以下几种角色：**

**抽象角色(Abstraction)：** 抽象的定义，并保存一个Implementor对象的引用。

**扩展抽象角色(RefineAbstraction)：** 拓展Abstraction。

**抽象实现角色(Implementor)：** 定义实现类的接口，提供基本操作，其实现交给子类实现。

**具体实现角色(ConcreteImplementor)：** 实现Implementor接口，在程序运行时，子类对象将替换其父类对象，提供给Abstraction具体的业务操作方法。

栗子：

```java
/**
 * 抽象角色Abstraction
 */
public abstract class Pen {
    private Color color;
    public String penType;

    public void setColor(Color color){
        this.color = color;
    }
    public void draw(){
        System.out.println("draw a pic with penType : "+penType +"   color : "+ color.color());
    }
}
```

```java
/**
 * **抽象实现角色(Implementor)
 */
public interface Color {
    String color();
}
```

```java
/**
 * **扩展抽象角色(RefineAbstraction)
 */
public class BigPen extends Pen {
    public BigPen() {
        this.penType = "bigPen";
    }
}
```

```java
/**
 * 扩展抽象角色(RefineAbstraction)
 */
public class SmallPen extends Pen {

    public SmallPen() {
        this.penType = "smallPen";
    }
}
```

```java
/**
 * 具体实现角色(ConcreteImplementor)
 */
public class  Red  implements Color{

    @Override
    public String color() {
        return "red";
    }
}
```

```java
/**
 * 具体实现角色(ConcreteImplementor)
 */
public class Blue implements Color {
    @Override
    public String color() {
        return "blue";
    }
}
```

```java
public class BridgeDemo {
    public static void main(String[] args) {
        //红色的大画笔
        Pen pen = new BigPen();
        pen.setColor(new Red());
        //红色的小画笔
        Pen pen1 = new SmallPen();
        pen1.setColor(new Red());
        //蓝色的大画笔
        Pen pen2 = new BigPen();
        pen2.setColor(new Blue());
        //蓝色的小画笔
        Pen pen3 = new SmallPen();
        pen3.setColor(new Blue());

        //画画
        pen.draw();
        pen1.draw();
        pen2.draw();
        pen3.draw();
    }
}
```

结果：

![](http://119.3.236.138:9090/images/2020-07-06-11-57-13-image.png)

## 七、过滤器模式  --结构型模式

直接看代码 很简单

```java
public interface Criteria {

    List<Person> meetCriteria(List<Person> personList);

}
```

```java
public class AgeCriteria implements Criteria{
    @Override
    public List<Person> meetCriteria(List<Person> personList) {
        return personList.stream().filter(person -> person.getAge() > 18).collect(Collectors.toList());
    }
}
```

```java
public class MaleCriteria implements Criteria {

    @Override
    public List<Person> meetCriteria(List<Person> personList) {
        return personList.stream().filter(person -> person.getSex().equals("male")).collect(Collectors.toList());
    }
}
```

```java
public class Person {
    private int age;
    private String sex;

    public Person(int age, String sex) {
        this.age = age;
        this.sex = sex;
    }

    public int getAge() {
        return age;
    }

    public String getSex() {
        return sex;
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", sex='" + sex + '\'' +
                '}';
    }
}
```

```java
public class FilterDemo {
    public static void main(String[] args) {
        List<Person> personList = Arrays.asList(new Person(20, "male"),
                new Person(17, "female"),
                new Person(20, "female"));
        AgeCriteria ageCriteria = new AgeCriteria();
        //过滤年龄大于18岁的
        System.out.println(ageCriteria.meetCriteria(personList));
        MaleCriteria maleCriteria = new MaleCriteria();
        //过滤男人
        System.out.println(maleCriteria.meetCriteria(personList));
    }
}
```

## 八、迭代器模式  --行为型模式

**用于顺序访问集合对象**

直接看栗子：

```java
public interface Iterator {
    boolean hasNext();

    Object next();
}
```

```java
public interface Container {
    Iterator getIterator();
}
```

```java
public class NameRepository implements Container {

    private String names[];

    public void setNames(String[] names) {
        this.names = names;
    }


    @Override
    public Iterator getIterator() {
        return new nameIterator();
    }

    private class nameIterator implements Iterator{

        private int index = 0;
        @Override
        public boolean hasNext() {
            if(index < names.length)
                return true;
            return false;
        }

        @Override
        public Object next() {
            if (hasNext()){
                return names[index++];
            }
            return null;
        }
    }
}
```

```java
public class IteratorDemo {
    public static void main(String[] args) {
        String names[] = {"jack","mark","tom","tony"};
        NameRepository nameRepository = new NameRepository();
        nameRepository.setNames(names);
        Iterator iterator = nameRepository.getIterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }
    }
}
```

![](http://119.3.236.138:9090/images/2020-07-06-22-56-36-image.png)

## 九、中介者模式  --行为型模式

![](https://www.runoob.com/wp-content/uploads/2014/08/mediator_pattern_uml_diagram.jpg)

```java
public class User {
    private String name;

    public String getName() {
        return name;
    }

    public User(String name) {
        this.name = name;
    }

    public void sendMsg(String msg){
        ChatRoom.sendMsg(this,msg);
    }
}
```

```java
public class ChatRoom {
    public static void sendMsg(User user,String msg){
        System.out.println(new Date().toString() + user.getName() + msg);
    }
}
```

结果：

![](http://119.3.236.138:9090/images/2020-07-06-22-58-44-image.png)

## 十、外观模式  --结构型模式

为多个复杂的子系统提供一个一致的接口

![](http://c.biancheng.net/uploads/allimg/181115/3-1Q115152143509.gif)

栗子：

```java
public class Facade {
    private System01 system01;
    private System02 system02;

    public Facade(System01 system01, System02 system02) {
        this.system01 = system01;
        this.system02 = system02;
    }

    public void system01Method(){
        system01.method();
    }
    public void system02Method(){
        system02.method();
    }
}
```

```java
public class System01 {
    public void method(){
        System.out.println("system01 's method");
    }
}
```

```java
public class System02 {
    public void method(){
        System.out.println("system02 's method");
    }
}
```

```java
public class FacadeDemo {
    public static void main(String[] args) {
        Facade facade = new Facade(new System01(),new System02());
        facade.system01Method();
        facade.system02Method();
    }
}
```

![](http://119.3.236.138:9090/images/2020-07-07-11-08-58-image.png)

## 十一、建造者模式  --创建型模式

**建造者（Builder）模式由产品、抽象建造者、具体建造者、指挥者等 4 个要素构成**

![](http://c.biancheng.net/uploads/allimg/181114/3-1Q1141H441X4.gif)

栗子：

![](http://c.biancheng.net/uploads/allimg/181114/3-1Q1141H553419.gif)

```java
/**
 * 产品
 */
public class Product {
    private String sofa;
    private String wall;
    private String tv;

    public void setSofa(String sofa) {
        this.sofa = sofa;
    }

    public void setWall(String wall) {
        this.wall = wall;
    }

    public void setTv(String tv) {
        this.tv = tv;
    }

    public void show() {
        System.out.println(
                "Product{" +
                        "sofa='" + sofa + '\'' +
                        ", wall='" + wall + '\'' +
                        ", tv='" + tv + '\'' +
                        '}'
        );
    }
}
```

```java
/**
 * 抽象建造者
 */
public abstract class Decorator {

    protected Product product = new Product();

    public abstract void buildWall();

    public abstract void buildSofa();

    public abstract void buildTv();

    public Product getResult(){
        return product;
    }
}
```

```java
/**
 * 具体建造者
 */
public class ConcreteDecorator01 extends Decorator {

    @Override
    public void buildWall() {
        product.setWall("01's wall");
    }

    @Override
    public void buildSofa() {
        product.setSofa("01's sofa");
    }

    @Override
    public void buildTv() {
        product.setTv("01's tv");
    }
}
```

```java
/**
 * 具体建造者
 */
public class ConcreteDecorator02 extends Decorator {
    @Override
    public void buildWall() {
        product.setWall("02's wall");
    }

    @Override
    public void buildSofa() {
        product.setSofa("02's sofa");
    }

    @Override
    public void buildTv() {
        product.setTv("02's tv");
    }
}
```

```java
/**
 * 指挥者
 */
public class Director {
    private Decorator builder;

    public Director(Decorator builder) {
        this.builder = builder;
    }

    public Product decorate(){
        builder.buildSofa();
        builder.buildTv();
        builder.buildWall();
        return builder.getResult();
    }
}
```

```java
public class BuilderDemo {
    public static void main(String[] args) {
        Director director01 = new Director(new ConcreteDecorator01());
        Director director02 = new Director(new ConcreteDecorator02());
        Product product01 = director01.decorate();
        Product product02 = director02.decorate();

        product01.show();

        product02.show();

    }
}
```

结果：

![](http://119.3.236.138:9090/images/2020-07-07-12-27-22-image.png)

## 十二、组合模式  --结构型模式

**将对象组合成树状的层次结构的模式，使得用户对单个对象和组合对象具有一致的访问性**

- 组合模式中的角色
  
  - 抽象构件（Component）角色
    
    - 为树叶和树枝提供公共接口
  
  - 树叶构件（Leaf）角色
    
    - 组合中的叶子节点对象  实现抽象构件提供的接口
  
  - 树枝构件（Composite）角色
    
    - 组合中的分支节点对象  实现抽象构件提供的接口

- 组合模式又分为两种模式
  
  - 透明式
    
    - 简单来说就是 树叶和树枝构件基本是一样的 但是由于都实现了抽象构件提供的的方法，导致树叶构件多了逻辑上本应该没有的 add，remove方法，会带来一些安全性问题。
    
    - ![](http://c.biancheng.net/uploads/allimg/181115/3-1Q1151G62L17.gif)
  
  - 安全式
    
    - 将管理子构件的方法移动到树枝构件中，避免了透明式模式中的安全性问题
    
    - ![](http://c.biancheng.net/uploads/allimg/181115/3-1Q1151GF5221.gif)

- 栗子：（透明式）

```java
/**
 * 抽象构件
 */
public interface Component {

    void add(Component c);

    void remove(Component c);

    void operation();

}
```

```java
/**
 * 树枝构件
 */
public class Composite implements Component {

    private List<Component> children = new ArrayList<>();

    @Override
    public void add(Component c) {
        children.add(c);
    }

    @Override
    public void remove(Component c) {
        children.remove(c);
    }

    @Override
    public void operation() {
        children.stream().forEach(c -> c.operation());
    }
}
```

```java
/**
 * 树叶构件
 */
public class Leaf implements Component {
    private String name;

    public Leaf(String name) {
        this.name = name;
    }

    /**
     * 在透明式中
     * 由于树枝和树叶构件都实现相同的方法，导致树叶中多了逻辑上不应该有的方法
     * @param c
     */
    @Override
    public void add(Component c) {

    }

    @Override
    public void remove(Component c) {

    }

    @Override
    public void operation() {
        System.out.println("叶子 " + name + "被访问");
    }
}
```

```java
public class CompositeDemo {
    public static void main(String[] args) {
        Component c1 = new Composite();
        Component c2 = new Composite();
        Component l1 = new Leaf("1号叶子");
        Component l2 = new Leaf("2号叶子");
        Component l3 = new Leaf("3号叶子");

        c1.add(l1);
        c2.add(l2);
        c2.add(l3);
        c1.add(c2);
        c1.operation();
    }
}
```

![](http://119.3.236.138:9090/images/2020-07-10-13-45-13-image.png)

栗子：（安全式）

```java
/**
 * 抽象构件
 */
public interface Component {
    void operation();
}
```

```java
/**
 * 叶子构件
 */
public class Leaf implements Component {

    private String name;

    public Leaf(String name) {
        this.name = name;
    }

    @Override
    public void operation() {
        System.out.println(name + "被访问");
    }
}
```

```java
/**
 * 树枝构件
 */
public class Composite implements Component {

    private List<Component> children = new ArrayList();

    public void add(Component c){
        children.add(c);
    }

    public void remove(Component c){
        children.remove(c);
    }
    @Override
    public void operation() {
        children.stream().forEach(c -> c.operation());
    }
}
```

```java
public class CompositeDemo {
    public static void main(String[] args) {
        Component c1 = new Composite();
        Component c2 = new Composite();

        Component l1 = new Leaf("一号叶子");
        Component l2 = new Leaf("二号叶子");
        Component l3 = new Leaf("三号叶子");

        ((Composite)c1).add(l1);
        ((Composite)c2).add(l2);
        ((Composite)c2).add(l3);
        ((Composite)c1).add(c2);

        c1.operation();


    }
}
```

结果：

![](http://119.3.236.138:9090/images/2020-07-10-13-55-29-image.png)

## 十三、享元模式

**通过共享已经存在的对象来大幅减少需要创建的对象数量，提高系统资源的利用率**

**享元模式中的角色**

1. 抽象享元角色（Flyweight）:是所有的具体享元类的基类，为具体享元规范需要实现的公共接口，非享元的外部状态以参数的形式通过方法传入。
2. 具体享元（Concrete Flyweight）角色：实现抽象享元角色中所规定的接口。
3. 非享元（Unsharable Flyweight)角色：是不可以共享的外部状态，它以参数的形式注入具体享元的相关方法中。
4. 享元工厂（Flyweight Factory）角色：负责创建和管理享元角色。当客户对象请求一个享元对象时，享元工厂检査系统中是否存在符合要求的享元对象，如果存在则提供给客户；如果不存在的话，则创建一个新的享元对象。
- 享元模式中的两种状态：
  
  - 内部状态：不会随环境的改变而改变的共享部分
  
  - 外部状态： 会改变的不能共享的部分
  
  - 享元模式的实现关键就是要区分这两种状态，并将外部状态外部化（外部状态单独放到一个类中）

![](http://c.biancheng.net/uploads/allimg/181115/3-1Q115161342242.gif)

栗子：

```java
/**
 * 抽象享元
 */
public interface Flyweight {
    void operation(UnSharedConcreteFlyweight unSharedConcreteFlyWeight);
}
```

```java
public class ConcreteFlyweight implements Flyweight {

    private String key;

    public ConcreteFlyweight(String key) {
        System.out.println("具体享元 " + key + "被创建");
        this.key = key;
    }

    @Override
    public void operation(UnSharedConcreteFlyweight unSharedConcreteFlyWeight) {
        System.out.println("具体享元 " + key + "被调用");
        System.out.println("非享元中的数据为 ： " + unSharedConcreteFlyWeight.getInfo());
    }
}
```

```java
/**
 * 非享元角色
 */
public class UnSharedConcreteFlyweight {
    private String info;

    public UnSharedConcreteFlyweight(String info) {
        this.info = info;
    }

    public String getInfo() {
        return info;
    }

    public void setInfo(String info) {
        this.info = info;
    }
}
```

```java
/**
 * 享元工厂角色
 */
public class FlyweightFactory {
    private HashMap<String,Flyweight> flyweights= new HashMap<String,Flyweight>();

    public Flyweight getFlyweight(String key){
        Flyweight flyweight = flyweights.get(key);
        if (flyweight == null){
            System.out.println("具体享元 " + key + "不存在   正在创建");
            flyweight = new ConcreteFlyweight(key);
            flyweights.put(key,flyweight);
            return flyweight;
        }
        System.out.println("具体享元 " + key + "已存在");
        return flyweight;
    }
}
```

```java
public class FlyweightDemo {
    public static void main(String[] args) {
        FlyweightFactory flyweightFactory = new FlyweightFactory();
        flyweightFactory.getFlyweight("a");
        flyweightFactory.getFlyweight("a");
        Flyweight a = flyweightFactory.getFlyweight("a");
        flyweightFactory.getFlyweight("b");
        flyweightFactory.getFlyweight("c");
        a.operation(new UnSharedConcreteFlyweight("hello"));
        a.operation(new UnSharedConcreteFlyweight("world"));
    }
}
```

结果：
![](http://119.3.236.138:9090/images/2020-07-10-22-27-19-image.png)

## 十四、模板方法模式  --行为型模式

**定义一个操作中的算法骨架，而将算法的一些步骤延迟到子类中，使得子类可以不改变该算法结构的情况下重定义该算法的某些特定步骤。**

![](http://c.biancheng.net/uploads/allimg/181116/3-1Q116095405308.gif)

```java
public abstract class AbstractClass {


    public void templateMethod(){
        SpecificMethod();
        abstractMethod1();
        abstractMethod2();
    }

    public void SpecificMethod() //具体方法
    {
        System.out.println("抽象类中的具体方法被调用...");
    }
    public abstract void abstractMethod1(); //抽象方法1
    public abstract void abstractMethod2(); //抽象方法2
}
```

```java
public class ConcreteClass extends AbstractClass {

    @Override
    public void abstractMethod1() {
        System.out.println("抽象方法1的实现被调用...");
    }

    @Override
    public void abstractMethod2() {
        System.out.println("抽象方法2的实现被调用...");
    }
}
```

```java
public class TemplateDemo {
    public static void main(String[] args) {
        AbstractClass abstractClass = new ConcreteClass();
        abstractClass.templateMethod();
    }
}
```

![](http://119.3.236.138:9090/images/2020-07-11-11-14-56-image.png)

## 十五、命令模式  -- 行为型模式

**将方法的请求者与方法的实现者解耦**

**命令模式包含以下主要角色。**

1. 抽象命令类（Command）角色：声明执行命令的接口，拥有执行命令的抽象方法 execute()。
2. 具体命令角色（Concrete    Command）角色：是抽象命令类的具体实现类，它拥有接收者对象，并通过调用接收者的功能来完成命令要执行的操作。
3. 实现者/接收者（Receiver）角色：执行命令功能的相关操作，是具体命令对象业务的真正实现者。
4. 调用者/请求者（Invoker）角色：是请求的发送者，它通常拥有很多的命令对象，并通过访问命令对象来执行相关请求，它不直接访问接收者。

![](http://c.biancheng.net/uploads/allimg/181116/3-1Q11611335E44.gif)

```java
package command;
public class CommandPattern
{
    public static void main(String[] args)
    {
        Command cmd=new ConcreteCommand();
        Invoker ir=new Invoker(cmd);
        System.out.println("客户访问调用者的call()方法...");
        ir.call();
    }
}
//调用者
class Invoker
{
    private Command command;
    public Invoker(Command command)
    {
        this.command=command;
    }
    public void setCommand(Command command)
    {
        this.command=command;
    }
    public void call()
    {
        System.out.println("调用者执行命令command...");
        command.execute();
    }
}
//抽象命令
interface Command
{
    public abstract void execute();
}
//具体命令
class ConcreteCommand implements Command
{
    private Receiver receiver;
    ConcreteCommand()
    {
        receiver=new Receiver();
    }
    public void execute()
    {
        receiver.action();
    }
}
//接收者
class Receiver
{
    public void action()
    {
        System.out.println("接收者的action()方法被调用...");
    }
}
```

栗子：

```java
/**
 * 命令模式的实例 --> 吃早饭
 * 抽象命令类
 */
public interface Breakfast {
    void cooking();
}
```

```java
/**
 * 肠粉
 * command 角色
 */
public class ChangFen implements Breakfast{

    private ChangFenChef changFenChef;

    public ChangFen() {
        this.changFenChef = new ChangFenChef();
    }

    @Override
    public void cooking() {
        changFenChef.cooking();
    }
}
```

```java
/**
 * 馄饨
 * command 角色
 */
public class HunTun  implements Breakfast{

    private HunTunChef hunTunChef;

    public HunTun() {
        this.hunTunChef = new HunTunChef();
    }

    @Override
    public void cooking() {
        hunTunChef.cooking();
    }
}
```

```java
/**
 * 肠粉厨师
 * receiver 角色
 */
public class ChangFenChef {

    public void cooking(){
        System.out.println("肠粉厨师做出了肠粉");
    }
}
```

```java
/**
 * 馄饨厨师
 * receiver 角色
 */
public class HunTunChef {

    public void cooking(){
        System.out.println("馄饨厨师做出了馄饨");
    }
}
```

```java
/**
 * 服务员
 * 充当调用者
 */
public class Waiter {

    private Breakfast changFen,hunTun;

    public Waiter(ChangFen changFen, HunTun hunTun) {
        this.changFen = changFen;
        this.hunTun = hunTun;
    }
    public void chooseChangFen(){
        changFen.cooking();
    }
    public void chooseHunTun(){
        hunTun.cooking();
    }
}
```

```java
public class BreakfastDemo {
    public static void main(String[] args) {
        Waiter waiter = new Waiter(new ChangFen(), new HunTun());
        //客人要肠粉
        waiter.chooseChangFen();
        //客人要馄饨
        waiter.chooseHunTun();
    }
}
```

结果：

![](http://119.3.236.138:9090/images/2020-07-13-19-40-40-image.png)

## 十六、备忘录模式  -- 行为型模式

**备忘录模式能够记录一个对象的内部状态，后悔时可以撤销当前操作，使数据恢复到之前的状态**

备忘录模式的主要角色如下。

1. 发起人（Originator）角色：记录当前时刻的内部状态信息，提供创建备忘录和恢复备忘录数据的功能，实现其他业务功能，它可以访问备忘录里的所有信息。
2. 备忘录（Memento）角色：负责存储发起人的内部状态，在需要的时候提供这些内部状态给发起人。
3. 管理者（Caretaker）角色：对备忘录进行管理，提供保存与获取备忘录的功能，但其不能对备忘录的内容进行访问与修改。

![](http://c.biancheng.net/uploads/allimg/181119/3-1Q119130413927.gif)
