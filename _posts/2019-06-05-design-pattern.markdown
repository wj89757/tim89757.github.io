---
layout:     post
title:      "设计模式"
subtitle:   "几种设计模式分享"
date:       2019-06-05 15:00:00
author:     "Tim"
header-img: "img/pattern.jpg"
tags:
    - 年度总结
---

### 一、设计模式

### 二、什么是设计模式

​	设计模式（Design pattern）是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结。**在某些场景下，针对某类问题的某种通用的解决方案。**

​	https://www.cnblogs.com/pony1223/p/7608955.html

### 三、设计模式的六大原则

- 开闭原则（Open Close Principle） 开闭原则就是说对扩展开放，对修改关闭。
- 里氏代换原则（Liskov Substitution Principle） 里氏代换原则(Liskov Substitution Principle LSP)面向对象设计的基本原则之一。 里氏代换原则中说，任何基类可以出现的地方，子类一定可以出现。 LSP是继承复用的基石，只有当衍生类可以替换掉基类，软件单位的功能不受到影响时，基类才能真正被复用，而衍生类也能够在基类的基础上增加新的行为。里氏代换原则是对“开-闭”原则的补充。实现“开-闭”原则的关键步骤就是抽象化。而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。—— From Baidu 百科
- 依赖倒转原则（Dependence Inversion Principle） 这个是开闭原则的基础，具体内容：真对接口编程，依赖于抽象而不依赖于具体。
- 接口隔离原则（Interface Segregation Principle） 这个原则的意思是：使用多个隔离的接口，比使用单个接口要好。还是一个降低类之间的耦合度的意思，从这儿我们看出，其实设计模式就是一个软件的设计思想，从大型软件架构出发，为了升级和维护方便。所以上文中多次出现：降低依赖，降低耦合。
- 迪米特法则（最少知道原则）（Demeter Principle） 为什么叫最少知道原则，就是说：一个实体应当尽量少的与其他实体之间发生相互作用，使得系统功能模块相对独立。
- 合成复用原则（Composite Reuse Principle） 原则是尽量使用合成/聚合的方式，而不是使用继承。

### 四、说说设计模式

**创建型模式：对象实例化的模式，创建型模式用于解耦对象的实例化过程。**

**结构型模式：把类或对象结合在一起形成一个更大的结构。**

**行为型模式：类和对象如何交互，及划分责任和算法。**

![img](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqciktfj30rx0d1t9m.jpg)

#### 1.策略模式

1. 基本概念

   其用意是针对一组算法，**将每一个算法封装到具有共同接口的独立的类中，从而使得它们可以相互替换**。

2. 问题

   策略模式和工厂模式的区别？

   

   

   

   

   

   

   

   

   

   **工厂模式**：创建型的设计模式／关注对象创建／无需知道具体的实现过程

   **策略模式**：行为型的设计模式／关注行为的选择／知道具体的实现过程

3. 结构

![](https://github.com/ZXZxin/ZXBlog/raw/master/%E6%9D%82%E9%A1%B9/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/images/01_stratgy_01.png)

- **环境(Context)角色**：持有一个`Strategy`的引用。
- **抽象策略(Strategy)角色**：这是一个抽象角色，通常由一个接口或抽象类实现。此角色给出所有的具体策略类所需的接口。
- **具体策略(ConcreteStrategy)角色**：包装了相关的算法或行为。

4. 代码

定义分配算法，可以实现对用户按照一定策略进行分配，例如随机／分数从高到低等

```java
public class Context {
    private AssignStrategy strategy;
    public Integer executeStrategy(List<ProjectUserScore> scoreList){
        return strategy.assign(scoreList);
    }
}
```

```java
public interface AssignStrategy {
    Integer assign(List<ProjectUserScore> userScoreList);
}
```

 随机算法实现：

```java
public class RandomAssignStrategy implements AssignStrategy {
    @Override
    public Integer assign(List<ProjectUserScore> userScoreList) {
        List<Integer> userIdList = userScoreList.stream().map(ProjectUserScore::getUserId).collect(Collectors.toList());
        Collections.shuffle(userIdList);
        return userIdList.get(0);
    }
}
```

分数从高到低算法实现：

```java
public class ScoreAssignStrategy implements AssignStrategy {
    @Override
    public Integer assign(List<ProjectUserScore> userScoreList) {
        userScoreList.sort(Comparator.comparing(ProjectUserScore::getScore).reversed());
        return userScoreList.get(0).getUserId();
    }
}
```

具体调用

```java
public class MainApp {
    public static void main(String[] args) {
        Context context = new Context(new RandomAssignStrategy());
        System.out.println("random assign result is : " + context.executeStrategy(getParams()));
        context = new Context(new ScoreAssignStrategy());
        System.out.println("score assign result is : " + context.executeStrategy(getParams()));
    }
}
```

![3333](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqcdv3mj31cm0rsju9.jpg)

5. 优缺点

   **优点**：在相同的行为抽象下有不同的具体实现策略，耦合度相对较低，扩展方便；操作封装也更为彻底，数据更安全

   **缺点**：随着策略的增加，子类也会变得繁多

6. 总结
   - 重心不是实现算法，而是如何组织、调用这些算法；
   - 各个策略算法的平等性。对于一系列具体的策略算法，大家的地位是完全一样的
   - 如果一个系统的策略多于四个，就需要考虑使用混合模式，解决策略类膨胀的问题。
   - https://www.cnblogs.com/otomedaybreak/archive/2012/01/20/2327879.html【[混合模式（工厂方法模式+策略模式+门面模式）](https://www.cnblogs.com/otomedaybreak/archive/2012/01/20/2327879.html)】
   - [https://xujiaojie.github.io/2017/08/12/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B9%8B%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8F%E5%8F%8A%E6%BA%90%E7%A0%81%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8/](https://xujiaojie.github.io/2017/08/12/设计模式之策略模式及源码中的应用/)

#### 2.观察者模式

1. 基本概念

  属于行为型模式的一种，它定义了一种一对多的依赖关系，**让多个观察者对象同时监听某一个主题对象**。这个主题对象在状态变化时，会通知所有的观察者对象，使他们能够自动更新自己

2. 问题

  消息队列是否为观察者模式？

  

  

  

  

  

  

  

  

  ![111](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqc8775j30dp0dldfu.jpg)

  观察者模式

  - 观察者 + 被观察者，是松耦合的关系
  - 应用于单个应用内部

  发布／订阅模式

  - 除了发布者和订阅者两个角色，还有Broker（**信息中介**这个角色），发布者和订阅者不存在耦合
  - 应用于跨应用（消息队列）https://github.com/jasonGeng88/blog/blob/master/201705/MQ.md

3. 结构

![111](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqckvd0j30go09kwel.jpg)



![333](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqciwt6j30iu0e4glp.jpg)

​		● **抽象主题(Subject)角色：抽象主题角色把所有对观察者对象**的引用保存在一个聚集（比如`List`对象）里。抽象主题提供一个接口，可以增加和删除观察者对象，抽象主题角色又叫做抽象被观察者(Observable)角色；

　　● **具体主题(ConcreteSubject)角色**：将有关状态存入具体观察者对象；在具体主题的内部状态改变时，给所有登记过的观察者发出通知。具体主题角色又叫做具体被观察者(Concrete Observable)角色；

　　● **抽象观察者(Observer)角色**：为所有的具体观察者定义一个接口，在得到主题的通知时更新自己，这个接口叫做更新接口。

　　● **具体观察者(ConcreteObserver)角色**：存储与主题的状态自恰的状态。具体观察者角色实现抽象观察者角色所要求的更新接口，以便使本身的状态与主题的状态相协调。如果需要，具体观察者角色可以保持一个指向具体主题对象(Concrete Subject)的引用；

4. 代码

   上面描述感觉有点晦涩，我们举个例子：

   以我们的项目状态更新通知为例，自定义观察者设计模式，当项目状态更新时，通知日志服务和企业微信服务。

   ```java
   public interface Subject {
       void attach(Observer observer);
       
       void detach(Observer observer);
   
       void notify(String message);
   }
   ```

   更新项目信息是具体主题（具体被观察者），里面存储了订阅该主题的观察者，并实现了抽象主题中的方法： 

   ```java
   public class UpdateInfoSubject implements Subject{
       private List<Observer> updateInfoList = Lists.newArrayList();
   
       @Override
       public void attach(Observer observer) {
           updateInfoList.add(observer);
       }
   
       @Override
       public void detach(Observer observer) {
           updateInfoList.remove(observer);
       }
   
       @Override
       public void notify(String message) {
           for (Observer observer : updateInfoList) {
               observer.update(message);
           }
       }
   }
   ```

   定义观察者接口

   ```java
   public interface Observer {
       void update(String message);
   }
   ```

   定义具体观察者实现，更新日志观察者

   ```java
   public class ProjectUpdateLogObserver implements Observer {
       private Integer projectId;
       private Integer userId;
       private Integer type;
       private String note;
     
       @Override
       public void update(String message) {
           // TODO insert updateLog
           ProjectUpdateLog projectUpdateLog = new ProjectUpdateLog();
           projectUpdateLog.setProjectId(this.projectId);
           projectUpdateLog.setCreateUserId(this.userId);
           projectUpdateLog.setType(this.type);
           projectUpdateLog.setNote(this.note);
           System.out.println("=======this is a update log insert transaction========" + JsonUtils.toJson(projectUpdateLog));
       }
   
   }
   ```

   定义具体观察者实现，企业微信观察者

   ```java
   public class WeChatSendMsgObserver implements Observer {
       private Integer userId;
       private String msg;
   
       public WeChatSendMsgObserver(Integer userId, String msg) {
           this.userId = userId;
           this.msg = msg;
       }
   
       @Override
       public void update(String message) {
           System.out.println("=======this is a notify user wechat transaction========userId is：" + userId + "，msg is ：" + msg);
       }
   }
   ```

   具体调用实现：

   ```java
   public class MainApp {
       public static void main(String[] args) {
           UpdateInfoSubject infoSubject = new UpdateInfoSubject();
           // 创建更新日志观察者
           ProjectUpdateLogObserver o1 = new ProjectUpdateLogObserver(16273, 37, ProjectUpdateLogTypeEnum.UPDATE.getValue(), "更新测试1");
           // 创建企业微信观察者
           WeChatSendMsgObserver o2 = new WeChatSendMsgObserver(37, "更新信息后的测试消息");
           // 订阅更新subject
           infoSubject.attach(o1);
           infoSubject.attach(o2);
           // 业务逻辑完成发出消息给订阅的更新日志
           infoSubject.notify("");
       }
   }
   ```

   ![4444](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqcejskj31h414ywk7.jpg)

5. 优缺点

   **优点**：松耦合，观察者和被观察者可以独立改变，容易扩展；观察者模式符合“开闭原则”的要求。

   **缺点**：如果一个subject被大量订阅，在通知时可能会有效率问题，一个观察者出现问题，可能会影响整体的执行效率

6. 总结

   - 应用于关联行为场景，应该是可拆分的，而不是“组合”关系。如（业务逻辑完成后记录操作日志／发送企业微信／微信公众号服务订阅）
   - 创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……
   - spring事件驱动模型[https://www.cnblogs.com/fingerboy/p/6393644.html]
   - 微博feed系统的推(push)模式和拉(pull)模式和时间分区拉模式架构探讨 https://www.cnblogs.com/sunli/archive/2010/08/24/twitter_feeds_push_pull.html
   - 设计模式之——观察者模式(触发联动) https://blog.csdn.net/fang323619/article/details/74010936
   - 设计模式（五）观察者模式https://blog.csdn.net/itachi85/article/details/50773358
   - [https://github.com/ZXZxin/ZXBlog/blob/master/%E6%9D%82%E9%A1%B9/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/02%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F.md](https://github.com/ZXZxin/ZXBlog/blob/master/杂项/设计模式/02观察者模式.md)

#### 3.装饰者模式

1. 基本概念

   　装饰模式又名包装(Wrapper)模式。装饰模式以对客户端透明的方式扩展对象的功能，是继承关系的一个替代方案。

2. 问题

   这是一种装饰者模式？

   ```java
   new ObjectOutputStream(new BufferedOutputStream(new FileOutputStream( "io.txt" )));
   ```

3. 结构

   ![](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqc9iyrj30n90dxaaw.jpg)

   - 抽象构件(Component)角色：给出一个抽象接口，以规范准备接收附加责任的对象。
   - 具体构件(ConcreteComponent)角色：定义一个将要接收附加责任的类。
   - 装饰(Decorator)角色：持有一个构件(Component)对象的实例，并定义一个与抽象构件接口一致的接口。
   - 具体装饰(ConcreteDecorator)角色：负责给构件对象“贴上”附加的责任。

4. 代码

   假设一家甜品店，出售蛋糕，除了蛋糕外，还可以在蛋糕上布置水果，蜡烛等，但是水果和蜡烛需要额外收费，**假设一个蛋糕的价格是66元，水果和蜡烛分别需要额外付10元，那么怎么样来动态的计算价格呢？**

   ```java
   public abstract class BaseDecorator extends Sweet {
       public abstract String getDescription();
   }
   ```

   定义被装饰者蛋糕类

   ```java
   public class Cake extends Sweet {
       @Override
       public String getDescription() {
           return "一个蛋糕";
       }
   
       @Override
       public double cost() {
           return 66;
       }
   }
   ```

   **定义抽象装饰者类Decorator**，最主要代码

   ```java
   public abstract class BaseDecorator extends Sweet {
       public abstract String getDescription();
   }
   ```

   定义具体装饰者水果类

   ```java
   public class CandleDecorator extends BaseDecorator {
       private Sweet sweet;
   
       public CandleDecorator(Sweet sweet) {
           this.sweet = sweet;
       }
   
       @Override
       public String getDescription() {
           return sweet.getDescription() + "，蜡烛";
       }
   
       @Override
       public double cost() {
           return sweet.cost() + 10;
       }
   }
   ```

   定义具体装饰者蜡烛类

   ```java
   public class FruitDecorator extends BaseDecorator {
       private Sweet sweet;
   
       public FruitDecorator(Sweet sweet) {
           this.sweet = sweet;
       }
   
       @Override
       public String getDescription() {
           return sweet.getDescription() + "，水果";
       }
   
       @Override
       public double cost() {
           return sweet.cost() + 10;
       }
   }
   ```

   最后根据不同的选择来结算价格：

   ```java
   public class MainApp {
       public static void main(String[] args) {
           Cake cake = new Cake();
           System.out.println(cake.getDescription() + "总共花费" + cake.cost());
   
           FruitDecorator fruitDecorator = new FruitDecorator(cake);
           System.out.println(fruitDecorator.getDescription() + "总共花费" + fruitDecorator.cost());
   
           CandleDecorator candleDecorator = new CandleDecorator(fruitDecorator);
           System.out.println(candleDecorator.getDescription() + "总共花费" + candleDecorator.cost());
       }
   }
   ```

   ![2222](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqcckhrj310k0tydj3.jpg)

5. 优缺点

   **优点** : 

   ​		对于扩展一个对象的功能，装饰模式比继承更加灵活性，不会导致类的个数急剧增加。

   ​		可以通过一种动态的方式在运行时选择不同的具体装饰类，从而实现不同的行为。

   ​		具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类，原有类库代码无须改变，符合“开闭原则”。

   **缺点** : 多层装饰比较复杂。

6. 总结

- https://www.cnblogs.com/java-my-life/archive/2012/04/20/2455726.html  【[《JAVA与模式》之装饰模式](https://www.cnblogs.com/java-my-life/archive/2012/04/20/2455726.html)】

- https://www.jianshu.com/p/c26b9b4a9d9e

- https://juejin.im/post/5ba0fb04e51d450e67494256【装饰者模式及典型应用】

- 在IO用得最多的就是装饰模式了，比如Java的输入输出流，为了能让输入输出流适合n多的应用场景，使用装饰者模式优于继承

  ![666](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqchifbj30ys14wjvp.jpg)

- Mybatis 缓存中的装饰者模式

  ![image-20190605085419873](http://ww1.sinaimg.cn/large/007OGB2sgy1g3qfqcqllnj31km0v4at1.jpg)





### 五、OTHERS

https://ice.work/scaffold 【丰富模板一键创建，提供多种垂直领域模板，基于 GUI 工具快速创建项目，支持风格切换，满足个性化需求】

https://www.infoq.cn/article/alibaba-freshhema-ddd-practice【领域驱动设计 ddd】

https://mp.weixin.qq.com/s/FfOhvMML-x-QMggyroXZrg【关于CodeReview的一些思考】

https://github.com/ZXZxin/ZXBlog 【记录各种学习笔记(算法、Java、数据库、并发......)】

https://github.com/javagrowing/JGrowing【Java成长路线】

https://github.com/doocs/technical-books【国内外互联网技术大牛们都写了哪些书籍：前端、后端、数据库、架构、大数据、...】

https://juejin.im/post/5cea1f705188250640005472【大型项目前端架构浅谈】

https://juejin.im/post/5ce515fb518825642c3f42dd【Flutter Web初体验】