SpringBoot操作数据：spring-data jpa jdbc mongodb redis!

SpringData也是和SpringBoot齐名的项目

注意：在SpringBoot2.X之后，原来使用的jedis被替换为了lettuce

jedis：采用的直连：多个线程操作的话，是不安全的，如果想要避免不安全的，使用jedis pool连接池！更像BIO模式

lettuce：采用netty，实例可以再多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据了，更像NIO模式

源码分析

```java

	@Bean
	@ConditionalOnMissingBean(name = "redisTemplate")//我们可以自己定义一个redisTemplate来替换这个默认的
	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
        //默认的RedisTemplate没有过多的设置，redis对象都是需要序列化
        //两个泛型都是Object。Object类型，我们使用需要强制转换<String, Object>
		RedisTemplate<Object, Object> template = new RedisTemplate<>();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}


	@Bean
	@ConditionalOnMissingBean//由于String是redis中最常使用的类型，所以单独提出来一个bean
	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
			throws UnknownHostException {
		StringRedisTemplate template = new StringRedisTemplate();
		template.setConnectionFactory(redisConnectionFactory);
		return template;
	}
```

整合测试一下

1. 导入依赖

   ```xml
   <!--操作redis-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-data-redis</artifactId>
           </dependency>
   ```

2. 配置连接

   ```properties
   #配置redis
   spring.redis.host=127.0.0.1
   spring.redis.port=6379
   ```

3. 测试