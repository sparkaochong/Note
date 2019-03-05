# 第四十三章：开发支持类库

## 1 知识点
1. 观察者设计模式的支持类库；

## 2 具体内容
### 2.1 观察者设计模式
观察者设计模式可以利用多线程实现的，不过这样的实现模式会比较麻烦，所以在Java中专门提供了与观察者有关的实现类：`public class Observable extends Object`、`Observer`
首先来观察Observer接口定义：
```java
public interface Observer{
	public void update(Observable o,Object arg);
}
```
如果要实现具体的观察者的操作在，则还需要使用Observable类，这个类中定义有如下的方法：

| No  | 方法名称                               | 类型 | 描述                   |
| --- | -------------------------------------- | ---- | ---------------------- |
| 1   | public void addObserver(Observer o)    | 普通 | 追加观察者             |
| 2   | public void deleteObserver(Observer o) | 普通 | 删除观察者             |
| 3   | public void notifyObservers()          | 普通 | 激活观察者             |
| 4   | protected void setChanged()            | 普通 | 被观察的操作发生了改变 |

下面模拟一个房子价格的观察者操作。
#### 范例：定义被观察者
```java
class House extends Observable{
    //房子价钱
    private double price;
    public House(double price){
        this.price = price;
    }

    public void setPrice(double price) {
        //房价上涨才有人关注
        if(price > this.price){
            //已经发生了改变
            super.setChanged();
            //通知所有观察者房价发生了改变
            super.notifyObservers();
        }
        this.price = price;
    }
}
```
于是再准备出观察者，观察者要关注着房子的价格变化。
#### 范例：定义观察者
```java
class HousePriceObserver implements Observer {
    private String name;

    public HousePriceObserver(String name){
        this.name = name;
    }

    @Override
    public void update(Observable o, Object arg) {
        //房子的信息发生了变化
        if(o instanceof House){
            if(arg instanceof Double){
                double newPrice = (Double) arg;
                System.out.println("房价上涨至：" + newPrice);
            }
        }
    }
}
```
#### 范例：测试程序
```java
public class TestDemo {
    public static void main(String[] args) {
        House house = new House(3000000);
        HousePriceObserver housePriceObserver1 = new HousePriceObserver("张三");
        HousePriceObserver housePriceObserver2 = new HousePriceObserver("李四");
        HousePriceObserver housePriceObserver3 = new HousePriceObserver("王二");
        house.addObserver(housePriceObserver1);
        house.addObserver(housePriceObserver2);
        house.addObserver(housePriceObserver3);
        house.setPrice(5000000);
    }
}
```
如果说现在不使用以上的类或者接口能否实现同样的观察者模式？**实现思路：** 所有的对象保存在一个数组里面，而后做一个判断，如果判断成功，则执行数组中的特定的操作方法。

### 2.2 定时调度
定时调度指的是每到一个时刻，都会自动的产生某些特定的操作形式。

如果要想实现定时调度可以使用两个类：Timer、TimerTask类。
* Timer类：设置具体的调度时刻
  * 调度：`public void schedule(TimerTask 调度操作类,Date 延迟开始时间,long 重复间隔)`
* TimerTask类：主要负责调度的具体要求
  * 类的定义：`public abstract class TimerTask extends Object implements Runnable`，这是一个Runnable接口的子类，在这个子类中定义偶一个抽象方法：`public abstract void run()`，定时调度的本质 = 延迟 + 递归 。

```java
class MyTask extends TimerTask {

    @Override
    public void run() {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:ss:SSS");
        System.out.println("当前系统日期时间：" + sdf.format(new Date()));
    }
}
public class TestDemo1 {
    public static void main(String[] args) {
        Timer timer = new Timer();
        timer.schedule(new MyTask(),1000,3000);
    }
}
```
定时调度不可能离开服务器的运行环境。

在许多的互联网公司中会经常使用到定时调度的操作。
### 2.3 UUID类
如果说现在需要你随机生成一个不会重复的字符串，你的首选是什么？
* 电脑IP地址 + 时间戳 + 任意几位的随机数 + 移位操作 = 几乎不会重复的数据。

可是如果由用户自己来处理这些操作实在是过于麻烦了，所以为了方便处理，专门提供有一个java.util.UUID的程序类，这个类可以生成以上格式的字符串信息。
这个类主要使用一个`public static UUID randomUUID()`
```java
public class TestDemo2 {
    public static void main(String[] args) {
        UUID uid = UUID.randomUUID();
        System.out.println(uid);
    }
}
```
正因为UUID产生的数据几乎没有重复的信息，所以在开发中可以使用它生成唯一的字符串，这种方式可以在文件的自动命名上使用，或者数据库中的主键也可以使用。

## 3 知识点总结
以后的开发一定会使用到UUID类。
