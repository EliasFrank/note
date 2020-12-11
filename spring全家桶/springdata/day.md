###  ORM思想

主要目的：操作实体类就相当于操作数据库表

建立两个映射关系：

​	实体类和表的映射关系

​	实体类中属性和表中字段的映射关系

### JPA规范

jpa规范，实现jpa规范，内部是由接口和抽象类组成的

```java
public class test {
    /**
     * 测试jpa的保存
     *  案例：保存一个客户到数据库中
     * jpa的操作步骤
     *  1. 加载配置文件创建工厂（实体管理类工厂）对象
     *  2. 通过实体管理类工厂获取实体管理器
     *  3. 获取事务对象，开启事务
     *  4. 完成增删改查操作
     *  5. 提交事务（回滚事务）
     *  6. 释放资源
     */
    @Test
    public void testSave() {
        /*//1. 加载配置文件创建工厂（实体管理器工厂）对象
        EntityManagerFactory factory =
                Persistence.createEntityManagerFactory("myJpa");
        //2. 通过实体管理器工厂获取实体管理器
        EntityManager em = factory.createEntityManager();*/
        EntityManager em = JpaUtils.getEntityManager();
        //3. 获取事务对象，开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();//开启事务
        //完成增删改查
        Customer customer = new Customer();
        customer.setCustName("雷佳音");
        customer.setCustIndustry("男团");
        //保存
        em.persist(customer);
        //提交事务
        tx.commit();

        //释放资源
        em.close();
//        factory.close();
    }
}
```

```java
/**
 * 客户的实体类
 *  配置映射关系
 *      1. 实体类和表的映射关系
 *          @Entity 声明实体类
 *          @Table 配置实体类和表的映射关系
 *              name: 配置数据库表的名称
 *      2. 实体类中属性和表中字段的映射关系
 * @author hl2333
 *
 */
@Entity
@Table(name = "cst_customer")
public class Customer {
    /**
     * 客户主键
     * @Id 声明主键的配置
     * @GeneratedValue 配置主键的生成策略
     *  GenerationType.IDENTITY 自增
     *      底层数据库必须支持自动增长
     *  GenerationType.SEQUENCE 序列
     *      底层数据库必须支持序列
     *  GenerationType.TABLE
     *      jpa提供的一种机制，通过一张数据库表的形式帮助我们完成主键自增
     *  GenerationType.AUTO
     *      由程序自动的帮助 我们选择主键生成策略
     * @Column 配置属性和字段的映射关系
     *  name：数据库表中字段的名称
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId;
    /**
     * 客户名称
     */
    @Column(name = "cust_name")
    private String custName;
    /**
     * 客户来源
     */
    @Column(name = "cust_source")
    private String custSource;
    /**
     * 客户级别
     */
    @Column(name = "cust_level")
    private String custLevel;
    /**
     * 客户所属行业
     */
    @Column(name = "cust_industry")
    private String custIndustry;
    /**
     * 客户联系方式
     */
    @Column(name = "cust_phone")
    private String custPhone;
    /**
     * 客户地址
     */
    @Column(name = "cust_address")
    private String custAddress;
}
```

