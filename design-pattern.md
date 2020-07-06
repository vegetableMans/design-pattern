# 设计模式

## 一、策略模式  --行为型模式

**面对不同的情况选用不同的策略方案**

**在策略模式（Strategy Pattern）中，一个类的行为或其算法可以在运行时更改。这种类型的设计模式属于行为型模式。**

**在策略模式中，我们创建表示各种策略的对象和一个行为随着策略对象改变而改变的 context 对象。策略对象改变 context 对象的执行算法**

- 使用场景
  
  - 一个系统中有很多类，他们的区别仅在于他们的行为，可以使用策略模式动态地让一个对象在许多行为中选择一种。
  
  - 一个系统需要动态地在几种算法中选择一种。

- 图示：
  ![](http://119.3.236.138:9090/images/2020-07-05-09-16-54-image.png)

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


