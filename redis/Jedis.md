redis是官方推荐的java连接开发工具，使用java操作redis中间件，如果你要使用java操作redis，那么一定要对redis十分的熟悉

使用步骤：

1. 导入对应的依赖

   ```xml
   <dependencies>
           <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
           <dependency>
               <groupId>redis.clients</groupId>
               <artifactId>jedis</artifactId>
               <version>3.3.0</version>
           </dependency>
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>fastjson</artifactId>
               <version>1.2.47</version>
           </dependency>
       </dependencies>
   ```

2. 编码测试：

   * 连接数据库

     ```java
     public class TestPing {
         public static void main(String[] args) {
             Jedis jedis = new Jedis("localhost",6379);
             System.out.println(jedis.ping());
         }
     }
     ```

     

   * 操作命令

   * 断开连接