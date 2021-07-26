1. Scanner类不适用于从控制台读取密码，java se6特别引入了Console类实现这个目的

    ```java
    /*
    如果有可能进行交互操作，就通过控制台窗口为交互的用户返回一个Console对象，否则返回null。对于任何一个通过控制台窗口启动的程序，都可以使用Console对象，否则，其可用性将与所使用的系统有关。（如果JVM是在交互式命令行（比如Windows的 cmd，linux/unix的 terminal）中启动的，并且输入输出没有重定向到另外的地方，才可得到一个可用的Console实例。）
    */
    Console cons = System.console();
    String username = cons.readLine("uesr name: ");
    char[] password = cons.readPassword("Password: ")
    ```

    为了安全起见，返回的密码存放在一维字符数组中，而不是字符串中。在堆密码进行处理之后，应该马上用一个填充值覆盖数组元素

    ```java
    /**
    这段程序可以在控制台运行，但是放在IDE中会报空指针异常
    */
    import java.io.Console;
    public class Test {
        public static void main(String[] args) {
            test4();
        }
    
        private static void test4() {
            Console console = System.console();
    
            String username = console.readLine();
            char[] password = console.readPassword();
    
            System.out.println(username + "---" + password);
            for (int i = 0; i < password.length; i++) {
                System.out.println(password[i]);
            }
        }
    }
    ```