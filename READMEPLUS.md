## 一 spotbugs配置
```
打包 gradlew 
gradlew clean
gradlew assemble（不要build全部执行 越过test）执行路径sptbugs
spotbugs/spotbugs 生成spotbugs.jar
将原来的下载的spotbugs.jar进行替换（注意：报错缺少包log... 找到相应的jar文件进行复制到同级目录
```
执行gui：java -jar $SPOTBUGS_HOME/lib/spotbugs.jar -gui1（option）
## 二 spotbugs检测类说明
2. FindOpenStream只检查流 Stream类 即使继承也是只检查了流对非流资源失效 
3. FindOpenStream检测了all path以及一些常见的工具类的方法中的资源释放 并且全面的考虑了参数 工厂方法 closable 以及all_path的问题

FindOpenStream检测器会找出退出方法时没及时关闭的I/O流对象：未能成功关闭的数据库资源及流，异常发生时未能关闭的数据库资源及流。

4. UnreleasedLock检查锁但是检查all——path锁 ）在所有的路径上没有释放锁及在所有的异常路径上没有释放锁。锁的单独出现以及锁的数量不关注
着重于代码中已经获得的但退出方法没有释放的锁java.util.concurrent，检测器需要早辅助的classpath中导入java.util.concurrent包
5. Obligation 可以指定classname addResourceMethod(args)ret DelResourceMethod(args)ret 
6. Obligation 数据库问题 数据库用于存储大量的三元组对 也可以通过addBuiltInPolicies()进行指定 找出所有执行路径上没有对使用的I/O流和数据库资源进行清理的方法，包含方法可能未能成功清理的流或者资源以及异常处理时候没有成功清理的流或者资源
7. spotbugs/spotbugs/src/main/java/edu/umd/cs/findbugs/detect/FindUnsatisfiedObligation.java

[usful doc](https://max.book118.com/html/2017/0928/135110766.shtm)
## 三 关于jar包文件 

打成jar包后，jar包是一个单独的文件而不是文件夹，所以通过文件路径是无法定位到资源文件的。此时，可通过类加载器读取jar包中的资源文件。[blog](https://blog.csdn.net/weixin_32285357/article/details/114194460)
14. 本地jar包查找文件路径踩坑 gradle 配置resource路径在config下
```
this.class.getResource("");打印的是本类所在包目录下的URL
this.class.getResource("/");打印的是字节码class根目录下的URL
class.getResource("/") == class.getClassLoader().getResource("")
```
## 四 实现说明
api元组路径 
```aidl
/home/fdse/jiananliu/myspotbugs/spotbugs/spotbugs/src/main/java/edu/umd/cs/findbugs/detect/proc_obligations
```
修改
```/home/fdse/jiananliu/myspotbugs/spotbugs/spotbugs/src/main/java/edu/umd/cs/findbugs/detect/BuildObligationPolicyDatabase.java```

修改创建obligation的规则进行读api元组文件
```java
addEntries();
addMatchMenthodEntry();
parseCSV();
```
