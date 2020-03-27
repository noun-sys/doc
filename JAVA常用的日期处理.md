![在这里插入图片描述](https://img-blog.csdnimg.cn/20191115172738466.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDYxMTU2Nw==,size_16,color_FFFFFF,t_70)
# 前言

**日期**处理,在<code>JDK1.8</code>版本之后,有了很多改变,时间处理上在日常项目中使用比比较频繁,各个公司,各个项目组针对日期处理都有不同工具类处理,但是好多都是<code>jdk1.8</code>之前的版本为主,虽然在日常使用过程中不受影响,但是本着与时俱进,跟随潮流的趋势,还是需要进行一定更新;

# 基本概念

### 格林威治时间

摘自维基百科:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010160244243.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDYxMTU2Nw==,size_16,color_FFFFFF,t_70)

最后需要了解的是,这个是世界市区的起点;定义的时区为0时区;

### 北京时间

北京时间指的是东八区的时间,与格林威治时间相差八个小时,如果格林威治时间是1日0点,那么北京时间为1日8点;

### 时间戳

摘自百度百科:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010160304588.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDYxMTU2Nw==,size_16,color_FFFFFF,t_70)

## 回顾

首先针对<code>JDK8</code>以前的操作方式,对日期进行简单操作流程进行回顾一下:

- 获取时间

  ```java
  // 方式一
  Date date = new Date();
  System.out.println(date);
  // 方式二
  Calendar calendar = Calendar.getInstance();
  Date time = calendar.getTime();
  System.out.println(time);
  ```

- 获取时间戳

  ```java
  // 方式一
  Date date = new Date();
  long time1 = date.getTime();
  System.out.println(time1);
  // 方式二
  long time2 = System.currentTimeMillis();
  System.out.println(time2);
  // 方式三
  long timeInMillis = Calendar.getInstance().getTimeInMillis();
  System.out.println(timeInMillis);
  ```

- 时间格式化

  ```JAVA
  // 时间格式
  SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss E Z D");
  System.out.println(sf.format(new Date()));  // 2019-10-10 15:06:05 星期四 +0800 283
  // 星期格式
  SimpleDateFormat sf2 = new SimpleDateFormat("E");
  System.out.println(sf2.format(new Date()));
  ```

  <code>SimpleDateFormat</code>构造函数中数据参数意义按照下面格式给出:

  | 字符 |       含义       |   示例    |
  | :--: | :--------------: | :-------: |
  |  y   |        年        | yyyy-2019 |
  |  M   |        月        |   MM-10   |
  |  d   |     月中天数     |   dd-10   |
  |  D   |    年中的天数    |    283    |
  |  E   |      星期几      |  星期四   |
  |  H   | 小时数(24小时制) |   HH-15   |
  |  h   | 小时数(12小时制) |   hh-03   |
  |  m   |      分钟数      |   mm-06   |
  |  s   |       秒数       |   ss-05   |
  |  Z   |       时区       |   +0800   |

- 时间转换

  ```JAVA
  SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
  //String转Date
  String str = "2019-10-10 15:06:05";
  System.out.println(sf.parse(str));
  //时间戳的字符串转Date
  String tsString = "1556788591462";
  //import java.sql
  Timestamp ts = new Timestamp(Long.parseLong(tsString));
  System.out.println(sf.format(ts));
  ```

  Tips:使用方法<code>parse</code>会有异常需要处理<code>ParseException</code>,继承<code>Exception</code>,需要人为进行处理;这里就要求字符串的格式必须与构造函数中给定一致,不然会报错;

## JDK1.8

在<code>jdk1.8</code>之后为了方便时间操作,新增三个与时间相关操作的类,并且都是线程安全的,可以降低代码在多线程下风险也可以极大的方便代码操作;

主要是针对下面三个类进行展开:

> - LocalDate 只包含日期，不包含时间，不可变类，且线程安全。
> - LocalTime 只包含时间，不包含日期，不可变类，且线程安全。
> - LocalDateTime 既包含了时间又包含了日期，不可变类，且线程安全。

- 获取时间

  ```java
  // 获取日期
  LocalDate localDate = LocalDate.now();
  System.out.println(localDate);
  // 获取时间
  LocalTime localTime = LocalTime.now();
  System.out.println(localTime);
  // 获取日期和时间
  LocalDateTime localDateTime = LocalDateTime.now();
  System.out.println(localDateTime);
  ```

- 获取时间戳

  ```java
  // 方式一
  long milli = Instant.now().toEpochMilli(); // 获取当前时间戳（精确到毫秒）
  // 方式二
  long second = Instant.now().getEpochSecond(); // 获取当前时间戳（精确到秒）
  System.out.println(milli);  // output:1565932435792
  System.out.println(second); // output:1565932435
  ```

- 时间格式化

  ```java
  // 方式一
  DateTimeFormatter dateTimeFormatter = DateTimeFormatter.
									      ofPattern("yyyy-MM-dd HH:mm:ss");
  String timeFormat = dateTimeFormatter.format(LocalDateTime.now());
  System.out.println(timeFormat); // 2019-10-10 15:38:00
  // 方式二
  DateTimeFormatter dateTimeFormatter1 = DateTimeFormatter.
    									   ofPattern("yyyy-MM-dd HH:mm:ss");
  String timeFormat2 = LocalDateTime.now().format(dateTimeFormatter1);
  System.out.println(timeFormat2);// 2019-10-10 15:38:00
  ```

- 时间转换

  上面时间格式化已经将日期格式转化成<code>String</code>类型,主要就是将<code>String</code>转换成日期;

  ```java
  String timeStr = "2019-10-10 15:38:00";
  LocalDateTime dateTime = LocalDateTime.
  						 parse(timeStr,DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
  System.out.println(dateTime);
  ```

## 其他使用

- 比较日期常用的手段

  > 方式1: 获取时间戳,然后计算差值,为正表示时间更大,为负表示时间更小;
  >
  > 方式2:通过<code>Date</code>自带的方法<code>before(),after(),equals()</code>方式进行判定;

- 计算日期间隔

  使用<code>jdk1.8</code>提供的类<code>Period</code>计算两个日期的间隔;

  ```java
  LocalDate d1 = LocalDate.now();
  LocalDate d2 = d1.plusDays(2);
  Period period = Period.between(d1, d2);
  System.out.println(period.getDays());   //2
  ```

# 总结

**日期**操作是平时业务开发过程中,经常使用到基本操作,经常会遇到各种各样不同的使用场景,这里对此进行简单总结,日常的开发的过程中,掌握了这些基本用法,其他日期操作的过程中都可以通过各种转化达到应用场景;

# 关于我

Hello,我是[球小爷](https://blog.csdn.net/weixin_44611567),热爱生活,求学七年,工作三载,而今已快入而立之年,如果您觉得对您有帮助那就一切都有价值,赠人玫瑰,手有余香❤️.最后把我最真挚的祝福送给您及其家人,愿众生一生喜悦,一世安康!

# 附录

- [技巧|结合业务一些常用开发技巧记录手札](https://blog.csdn.net/weixin_44611567/article/details/100137446)
- [技巧|Mybatis批量操作常见方式总结](https://blog.csdn.net/weixin_44611567/article/details/100124740)
- [技巧|简单重构Mybatis基本CRUD的使用](https://blog.csdn.net/weixin_44611567/article/details/100123865)
- [技巧|业务代码简单重构记录手札](https://blog.csdn.net/weixin_44611567/article/details/100153064)
- [技巧|腾讯云Mysql简单配置记录手札](https://blog.csdn.net/weixin_44611567/article/details/100593064)
- [技巧|JAVA中时间操作](https://blog.csdn.net/weixin_44611567/article/details/102484351)

