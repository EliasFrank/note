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

   ```java
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


### 获取请求体

```java
@Controller
public class AjaxController {
    @Autowired
    BookDao bookDao;


    @RequestMapping("/download")
    public ResponseEntity<byte[]> download()throws Exception{
        FileInputStream fis  = new FileInputStream("H:\\test\\rs\\userPage.jsp");
        byte[] body = new byte[fis.available()];
        fis.read(body);
        fis.close();
        MultiValueMap<String, String> headers = new HttpHeaders();
        headers.set("Content-Disposition", "attachment;filename=aaaa.html");
        return new ResponseEntity<byte[]>(body, headers, HttpStatus.OK);
    }
    /**
    * 向本地文件中写入cookie
    * */
    @RequestMapping("/test02")
    public ResponseEntity<String> test02(){
        String body = "<h1>success</h1>";
        MultiValueMap<String, String> headers = new HttpHeaders();
        headers.add("Set-Cookie", "username = 2222");

        return new ResponseEntity<String>(body, headers, HttpStatus.OK);
    }
    /**
     * 如果参数位置写HttpEntity<String> str
     * 比@RequestBody更加强大
     * 表示HTTP请求或响应实体，由标头和正文组成
    * */

    @RequestMapping("/test")
//    @RequestBody
    public String test(HttpEntity<String> str){
        System.out.println(str);
        return "success";
    }
    /*
    * @ReuqestBody:请求体，获取一个请求的请求体
    * @ResponseBody:返回json数据
    * @RequestBody: 接受json数据
    * */
    @RequestMapping("/testRequestMapping")
//    @RequestBody
    public String testRequestMapping(@RequestBody Book book){
        System.out.println(book);
        return "success";
    }

    /**
    * 将返回的数据放在响应体中
    * 如果是对象，jackson包自动将对象转为json模式
    * */
    @ResponseBody
    @RequestMapping("/getAllAjax")
    public List<Book> getAll(){
        ArrayList<Book> books = bookDao.getAllBooks();
        return books;
    }
}
```

