# 状态模式


## 抽象状态
```
//定义和Context中的状态相对应的行为
public interface State {
    //获取天气情况
    String getState();
}
```

## 上下文，依赖于状态
```
//定义当前的状态
public class Context {
 
    private State state;
 
    public State getState() {
        return state;
    }
 
    public void setState(State state) {
        this.state = state;
    }
    public String stateMessage(){
        return state.getState();
    }
}
```

## 具体状态
```
class Sunshine implements State{
 
     @Override
     public String getState() {
         
         return "晴天";
     }
      
 }
class Rain implements State{
 
    @Override
    public String getState() {
        return "下雨";
    }
     
}
```

## 客户端
```
public class StateTest {
    public static void main(String args[]){
        Context context=new Context();
        context.setState(new Rain());
        System.out.println(context.stateMessage());
        context.setState(new Sunshine());
        System.out.println(context.stateMessage());
    }
}
```

# 工厂方法模式

```
//抽象产品角色
public interface Moveable {
    void run();
}
//具体产品角色
public class Plane implements Moveable {
    @Override
    public void run() {
        System.out.println("plane....");
    }
}
//具体产品角色
public class Broom implements Moveable {
    @Override
    public void run() {
        System.out.println("broom.....");
    }
}

//抽象工厂
public abstract class VehicleFactory {
    abstract Moveable create();
}
//具体工厂
public class PlaneFactory extends VehicleFactory{
    public Moveable create() {
        return new Plane();
    }
}
//具体工厂
public class BroomFactory extends VehicleFactory{
    public Moveable create() {
        return new Broom();
    }
}
//测试类
public class Test {
    public static void main(String[] args) {
        VehicleFactory factory = new BroomFactory();
        Moveable m = factory.create();
        m.run();
    }
}
```

# 抽象工厂模式

```

// 省略了抽象产品和具体产品的类代码
//抽象工厂类
public abstract class AbstractFactory {
    public abstract Vehicle createVehicle();
    public abstract Weapon createWeapon();
    public abstract Food createFood();
}
//具体工厂类，其中Food,Vehicle，Weapon是抽象类，
public class DefaultFactory extends AbstractFactory{
    @Override
    public Food createFood() {
        return new Apple();
    }
    @Override
    public Vehicle createVehicle() {
        return new Car();
    }
    @Override
    public Weapon createWeapon() {
        return new AK47();
    }
}
//测试类
public class Test {
    public static void main(String[] args) {
        AbstractFactory f = new DefaultFactory();
        Vehicle v = f.createVehicle();
        v.run();
        Weapon w = f.createWeapon();
        w.shoot();
        Food a = f.createFood();
        a.printName();
    }
}
```

# 观察者模式
```
package com.jstao.observer;

/***
 * 抽象被观察者接口
 * 声明了添加、删除、通知观察者方法
 * @author jstao
 *
 */
public interface Observerable {
    
    public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObserver();
    
}
```

```
package com.jstao.observer;

/***
 * 抽象观察者
 * 定义了一个update()方法，当被观察者调用notifyObservers()方法时，观察者的update()方法会被回调。
 * @author jstao
 *
 */
public interface Observer {
    public void update(String message);
}
```

```
package com.jstao.observer;

import java.util.ArrayList;
import java.util.List;

/**
 * 被观察者，也就是微信公众号服务
 * 实现了Observerable接口，对Observerable接口的三个方法进行了具体实现
 * @author jstao
 *
 */
public class WechatServer implements Observerable {
    
    //注意到这个List集合的泛型参数为Observer接口，设计原则：面向接口编程而不是面向实现编程
    private List<Observer> list;
    private String message;
    
    public WechatServer() {
        list = new ArrayList<Observer>();
    }
    
    @Override
    public void registerObserver(Observer o) {
        
        list.add(o);
    }
    
    @Override
    public void removeObserver(Observer o) {
        if(!list.isEmpty())
            list.remove(o);
    }

    //遍历
    @Override
    public void notifyObserver() {
        for(int i = 0; i < list.size(); i++) {
            Observer oserver = list.get(i);
            oserver.update(message);
        }
    }
    
    public void setInfomation(String s) {
        this.message = s;
        System.out.println("微信服务更新消息： " + s);
        //消息更新，通知所有观察者
        notifyObserver();
    }

}
```

```
package com.jstao.observer;

/**
 * 观察者
 * 实现了update方法
 * @author jstao
 *
 */
public class User implements Observer {

    private String name;
    private String message;
    
    public User(String name) {
        this.name = name;
    }
    
    @Override
    public void update(String message) {
        this.message = message;
        read();
    }
    
    public void read() {
        System.out.println(name + " 收到推送消息： " + message);
    }
    
}

```
package com.jstao.observer;

public class Test {
    
    public static void main(String[] args) {
        WechatServer server = new WechatServer();
        
        Observer userZhang = new User("ZhangSan");
        Observer userLi = new User("LiSi");
        Observer userWang = new User("WangWu");
        
        server.registerObserver(userZhang);
        server.registerObserver(userLi);
        server.registerObserver(userWang);
        server.setInfomation("PHP是世界上最好用的语言！");
        
        System.out.println("----------------------------------------------");
        server.removeObserver(userZhang);
        server.setInfomation("JAVA是世界上最好用的语言！");
        
    }
}
```
```
