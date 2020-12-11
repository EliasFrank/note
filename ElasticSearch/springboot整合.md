1. 找到原生依赖

    ```xml
    <dependency>
    	<groupId>org.elasticsearch.client</groupId>
        <artifactId>elasticsearch-rest-high-level-client</artifactId>
        <version>7.6.0</version>
    </dependency>
    ```

2. 找对象

    ```java
    @Bean
    public RestHighLevelClient restHighLevelClient(){
    RestHighLevelClient restHighLevelClient = new RestHighLevelClient(
            RestClient.builder(
                new HttpHost("localhost", 9200, "http")
            )
        );
        return restHighLevelClient;
    }
    ```
    
    