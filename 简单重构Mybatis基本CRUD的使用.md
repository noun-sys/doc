# 前言
**Mybatis**是目前比较流行的持久层框架,一般使用过程都会依赖逆向工程生成基本CRUD操作,各个Mapper文件中都会存在相同类型的代码,虽然不影响使用,但是对于这类代码看起来比较难受,随着Mapper文件中接口数量变多,更为显得臃肿,这里使用接口继承,泛型实现一个小小的优化,记录一下优化过程.

- 软件准备: IDEA
- Mybatis
- MacOs/Windows

# 优化
1.建立一个接口,方法包含的所有逆向工程实现的的所有的泛型方法,返回与参数类型均采用泛型设置:
```java
public interface GenericMapper<T> {
    int deleteByPrimaryKey(Long id);
    long insert(T record);
    long insertSelective(T record);
    T selectByPrimaryKey(Long id);
    List<T> selectAll();
    int updateByPrimaryKey(T record);
    int updateByPrimaryKeySelective(T record);
}
```
2.建立xxMapper.java接口文件与xxMapper.xml文件一一对应,然后继承上面的接口GenericMapper接口,泛型参数选择Entiy实体类对象;
```java
@Repository
public interface UserMapper extends GenericMapper<User> {
    User selectPersonalUserByMobile(@Param("mobile") String mobile);
    User selectEnterpriseUserByMobile(@Param("mobile") String mobile);
    User selectMasterTrusteeUserByEmail(@Param("email") String email);
}
```
这样在实际使用过程中就可以直接使用GenericMapper中定义的由逆向工程生成的基本方法;
# 结语
虽然是一个小小的优化,但是比起之前老是在各个Mapper接口中看到相同的代码,这样感觉更为清爽一点.
今天算是正式加入CSDN,记录生活,记录人生,希望大家可以多多交流,还请各位多多指导.
# 关于我
Hello,我是[球小爷](https://blog.csdn.net/weixin_44611567),热爱生活,求学七年,工作三载,而今已快入而立之年,如果您觉得对您有帮助那就一切都有价值,赠人玫瑰,手有余香❤️.最后把我最真挚的祝福送给您及其家人,愿众生一生喜悦,一世安康!
# 附录

- [技巧|结合业务一些常用开发技巧记录手札](https://blog.csdn.net/weixin_44611567/article/details/100137446)
- [技巧|Mybatis批量操作常见方式总结](https://blog.csdn.net/weixin_44611567/article/details/100124740)
- [技巧|简单重构Mybatis基本CRUD的使用](https://blog.csdn.net/weixin_44611567/article/details/100123865)
- [技巧|业务代码简单重构记录手札](https://blog.csdn.net/weixin_44611567/article/details/100153064)
- [技巧|腾讯云Mysql简单配置记录手札](https://blog.csdn.net/weixin_44611567/article/details/100593064)
- [技巧|JAVA中时间操作](https://blog.csdn.net/weixin_44611567/article/details/102484351)