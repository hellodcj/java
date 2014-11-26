##类的加载
当程序主动使用某个类的时候，如果JVM中没有该类的实例，系统会通过加载、连接、初始化三个步骤，将类加载到内存中。
> * 类的加载 将class文件，加载到JVM，加载的过程中会使用到类加载器，也就是classloader
> * 类的连接 将二进制的数据合并到JRE中
> * 类的初始化 对静态属性进行初始化

###类的初始化时机
以下几种情况，会进行类的初始化。
> * 实例化类
> * 调用静态方法，访问静态类
> * 使用反射强制生成某个类或者接口的class对象。比如Class.forName("Person")。
> * 初始化子类，该子类的所有父类会进行初始化操作。


##反射
###1.获得class对象
有三种方法可以获得class对象。
> * Class.forName("")  参数为一个字符串，包含包名，但是找不到的时候会报异常
> * 调用某个类的class属性。 eg. Person.class
> * 对象的getClass()方法。 `推荐`

获得class对象后，此对象中包含了大量的类的详细信息。
```
//获取ClassTest类的class
Class<ClassTest> clazz =ClassTest.class;
//获取构造方法
Constructor[] constructors = clazz.getDeclaredConstructors();
//获取方法
Method[] methods = clazz.getMethods();
//获取域
Field[] fileds = clazz.getDeclaredFields();
```

###2.使用反射生成并操作对象
创建实例有两种方法
> * class.newInstance()  这个方法，要求类有默认的构造方法。 （比较常用，从配置文件中读取类的信息，并生成对象）
> * 获取constructor后，调用constructor对象的newInstance() 方法。

通过反射，生成对象，并调用方法的过程步骤
1. 获得class对象
2. 通过获得的class，创建实例 class.newInstance()
3. 获得method对象
4. method.invoke()

```
Class clazz = Class.forName(p.getProperty("c"));
Object o = clazz.newInstance();
Method m = clazz.getMethod("fun1", String.class);
m.invoke(o, "bbb");
```

###3.使用Proxy和InvocationHandler生成动态代理
生成动态代理的过程如下
1. 自己写一个InvocationHanler实现InvocationHandler接口，在invoke方法中，执行实际想要做的操作
2. 实例化被代理对象，并将该对象传入自己定义的InvocationHanler类
3. 调用Proxy.newProxyInstance()方法，返回代理对象
4. 代理对象调用想要调用的方法

自定义的InvocationHandler
```
public Object invoke(Object proxy, Method method, Object[] args)
			throws Throwable {
		LogUtil log = new LogUtil();
		log.startLog();
		Object result = method.invoke(target, args);
		log.endLog();
		return result;
	}
```

main()方法中
```
MyInvocationHandler handler = new MyInvocationHandler();
handler.setTarget(p);
return (Person) Proxy.newProxyInstance(p.getClass().getClassLoader(), p.getClass().getInterfaces(), handler);
```