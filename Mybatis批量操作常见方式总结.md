
# 前言
**Mybatis**是目前比较流行的持久层框架,在日常工作的过程中经常会使用到批量操作,一般较为常见批量操作可以分成,批量更新,批量查询,批量插入,IN查询或更新,批量操作一般都会存在,较为复杂的整合逻辑,如果操作不当,有可能会造成事务问题,或者性能问题;

- 软件准备: IDEA
- Mybatis
- MacOs/Windows

# 优化
### 1.批量插入
1.一般mysql在创建表的时候对于主键一般设置AUTO_INCREMENT,提示主键按照自动递增,因此在做批量插入时,只针对主键外其他的字段进行操作,先定义mapper接口文件:
```java
@Component
public interface CaseFileItemEntityMapper extends GenericMapper<CaseFileItemEntity, Long> {
    int insertBatch(@Param("list") List<CaseFileItemEntity> list);
}
```
2.对应接口方法中mapper.xml文件,
```java
<insert id="insertBatch" parameterType="java.util.List" useGeneratedKeys="true" keyProperty="id">
  insert into t_case_file_item (file_name, file_key, md5,
  out_file_key, business_id, case_id, type, created_at, updated_at) values
  <foreach collection="list" index="index" item="result" separator=",">
    (
      #{result.fileName,jdbcType=VARCHAR}, #{result.fileKey,jdbcType=VARCHAR}, #{result.md5,jdbcType=VARCHAR},
      #{result.outFileKey,jdbcType=VARCHAR}, #{result.businessId,jdbcType=BIGINT}, #{result.caseId,jdbcType=BIGINT},
      #{result.type,jdbcType=VARCHAR}, #{result.createdAt,jdbcType=TIMESTAMP}, #{result.updatedAt,jdbcType=TIMESTAMP}
    )
  </foreach>
</insert>
```
这里在使用过程中总是存在这样疑问,如果数据库中在建表的时候允许字段非空.这里同样如果设置了该属性为空会不会报异常信息;实践证明在的mapper.xml文件中设置属性的jdbcType可以保证插入null值不会报错;

### 2.批量更新
批量更新使用的场景可能要做一下区分,主要分成两个应用场景,一个是属性对象全字段更新,另外是针对某个属性进行更新,比如更新状态,更新操作人,操作时间等场景;
#### 2.1 全字段更新
1.先照常定义接口中的接口方法:
```java
@Component
public interface CaseFileItemEntityMapper extends GenericMapper<CaseFileItemEntity, Long> {
  Integer updateCaseFileItemsByIds(@Param("list") List<CaseFileItemEntity> list);
}
```
2.对应接口中Mapper.xml文件
```java
<update id="updateCaseFileItemsByIds" parameterType="java.util.List">
     UPDATE t_case_file_item
     <trim prefix="set" suffixOverrides=",">
       <trim prefix="file_name =case" suffix="end,">
         <foreach collection="list" item="item" index="index">
           <if test="item.fileName!=null">
             when id=#{item.id} then #{item.fileName}
           </if>
         </foreach>
       </trim>
       <trim prefix="file_key =case" suffix="end,">
         <foreach collection="list" item="item" index="index">
           <if test="item.fileKey!=null">
             when id=#{item.id} then #{item.fileKey}
           </if>
         </foreach>
       </trim>
       <trim prefix="md5 =case" suffix="end," >
         <foreach collection="list" item="item" index="index">
           <if test="item.md5!=null">
             when id=#{item.id} then #{item.md5}
           </if>
         </foreach>
       </trim>
       <trim prefix="out_file_key =case" suffix="end," >
         <foreach collection="list" item="item" index="index">
           <if test="item.outFileKey!=null">
             when id=#{item.id} then #{item.outFileKey}
           </if>
         </foreach>
       </trim>
       <trim prefix="business_id =case" suffix="end," >
         <foreach collection="list" item="item" index="index">
           <if test="item.businessId!=null">
             when id=#{item.id} then #{item.businessId}
           </if>
         </foreach>
       </trim>
       <trim prefix="case_id =case" suffix="end," >
         <foreach collection="list" item="item" index="index">
           <if test="item.caseId!=null">
             when id=#{item.id} then #{item.caseId}
           </if>
         </foreach>
       </trim>
       <trim prefix="file_item_type =case" suffix="end," >
         <foreach collection="list" item="item" index="index">
           <if test="item.fileItemType!=null">
             when id=#{item.id} then #{item.fileItemType}
           </if>
         </foreach>
       </trim>
       <trim prefix="created_at =case" suffix="end," >
         <foreach collection="list" item="item" index="index">
           <if test="item.createdAt!=null">
             when id=#{item.id} then #{item.createdAt}
           </if>
         </foreach>
       </trim>
       <trim prefix="updated_at =case" suffix="end," >
         <foreach collection="list" item="item" index="index">
           <if test="item.updatedAt!=null">
             when id=#{item.id} then #{item.updatedAt}
           </if>
         </foreach>
       </trim>
     </trim>
     WHERE
     <foreach collection="list" separator="or" item="item" index="index" >
       id=#{item.id}
     </foreach>
 </update>
```
#### 2.2 根据主键更新某个字段
1.先照常定义接口中的接口方法:
```java
@Repository
public interface TContractMapper extends GenericMapper<TContract, Long> {
  Integer deleteContractBatchList(@Param("list")List<Long> contractIds);
}
```
2.对应接口中Mapper.xml文件
```java
<update id="deleteContractBatchList" parameterType="java.lang.Long">
  update t_contract SET status = 7 WHERE id IN
  <foreach collection="list" item="item" index="index" open="(" separator="," close=")">
    #{item,jdbcType=BIGINT}
  </foreach>
</update>
```
这种操作同样试用于使用主键列表进行批量查询,批量删除(物理删除,虽然的很少使用) ,下面给出几个批量删除和批量查询的mapper.xml文件,以作参考:
### 3.批量删除
1.先照常定义接口中的接口方法:
```java
@Repository
public interface TContractMapper extends GenericMapper<TContract, Long> {
  Integer deleteContractBatchList(@Param("list")List<Long> contractIds);
}
```
2.对应接口中Mapper.xml文件
```java
<delete id="deleteContractBatchList" parameterType="java.lang.Long">
  DELETE FROM t_contract WHERE id IN
  <foreach collection="list" item="item" index="index" open="(" separator="," close=")">
    #{item,jdbcType=BIGINT}
  </foreach>
</delete>
```
### 4.批量查询
1.先照常定义接口中的接口方法:
```java
@Repository
public interface TContractMapper extends GenericMapper<TContract, Long> {
  List<ContractBatchResponse> selectContractBatchList(@Param("list")List<Long> contractIds);
}
```
2.对应接口中Mapper.xml文件
```java
<select id="selectContractBatchList" resultMap="BatchResultMap">
  SELECT
  <include refid="Batch_Column_list"/>
  FROM t_contract WHERE id IN
  <foreach collection="list" item="item" index="index" open="(" separator="," close=")">
    #{item,jdbcType=BIGINT}
  </foreach>
</select>
```
# 结语
业务开发过程,关于批量操作,差不多也就是涉及这些,批量查询,删除,更新操作,记录一下,希望可以帮助到有需要的朋友.
# 关于我
Hello,我是[球小爷](https://blog.csdn.net/weixin_44611567),热爱生活,求学七年,工作三载,而今已快入而立之年,如果您觉得对您有帮助那就一切都有价值,赠人玫瑰,手有余香❤️.最后把我最真挚的祝福送给您及其家人,愿众生一生喜悦,一世安康!
# 附录

- [技巧|结合业务一些常用开发技巧记录手札](https://blog.csdn.net/weixin_44611567/article/details/100137446)
- [技巧|Mybatis批量操作常见方式总结](https://blog.csdn.net/weixin_44611567/article/details/100124740)
- [技巧|简单重构Mybatis基本CRUD的使用](https://blog.csdn.net/weixin_44611567/article/details/100123865)
- [技巧|业务代码简单重构记录手札](https://blog.csdn.net/weixin_44611567/article/details/100153064)
- [技巧|腾讯云Mysql简单配置记录手札](https://blog.csdn.net/weixin_44611567/article/details/100593064)
- [技巧|JAVA中时间操作](https://blog.csdn.net/weixin_44611567/article/details/102484351)