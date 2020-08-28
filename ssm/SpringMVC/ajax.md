1. 导包

   ```xml
    <dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
       <version>2.9.9.3</version>
    </dependency>
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-core</artifactId>
         <version>2.9.9</version>
       </dependency>
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-annotations</artifactId>
         <version>2.9.9</version>
       </dependency>
   
   ```

2. 写配置

3. 测试

   ```
    /*
       * 将返回的数据放在响应体中
       * 如果是对象，jackson包自动将对象转为json模式
       * */
       @ResponseBody
       @RequestMapping("/getAllAjax")
       public List<Book> getAll(){
           ArrayList<Book> books = bookDao.getAllBooks();
           return books;
       }
   ```

   