项目简介：
项目是整合JDBC和Cache的web开发spring-boot-starter
数据库操作语句自由，无需封装绑定实体类，完全自由增删查改。
导入依赖快速搭建
使用Cache 缓存技术实现解决web开发数较多加载慢的问题
com.rdweb.starter伴你开发web
项目demo:
   首先pom.xm导入rdweb:
   
    <dependency>
        <groupId>com.rdweb.starter</groupId>
        <artifactId>rdweb-spring-boot-starter</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>

   Web 连接数据库
   Pom.xml 导入数据库驱动依赖
   例：mysql驱动
   
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <scope>runtime</scope>
    </dependency>

application.properties配置连接数据库四大元素：

    rd.web.jdbc.driverClass=com.mysql.cj.jdbc.Driver
    rd.web.jdbc.url=jdbc:mysql:///mybatis
    rd.web.jdbc.userName=root
    rd.web.jdbc.password=123456


如若使用cache缓存需要在主程序添加注解@EnableCaching

    @EnableCaching
    @SpringBootApplication
    public class Test1Application {
        public static void main(String[] args) {
            SpringApplication.run(Test1Application.class, args);
        }
    }

不使用cache 缓存可以不用添加

编写Controller测试
    
    @RestController
    public class appController {

    @Autowired
    JDBC jdbc;//连接数据库管理器
    @Autowired
    CacheManager cacheManager;//缓存管理器
    @RequestMapping("/selAll")
    public String view(){
        //自定义数据库操作语句
        String sql="select * from userinfo";

        //使用缓存（目前CacheUtils包含selectCachetoJson1(),selectCachetoJson(),selectCachetoString(),updateCache(),readerCachetoString()。5个方法。
        //selectCachetoJson1()与JDBC类中数据库查询方法tableTojson1()对应
        //selectCachetoJson()与JDBC类中数据库查询方法tableTojson()对应
        //selectCachetoString()与JDBC类中数据库查询方法ajaxreturn1()对应
        //updateCache()清除缓存，在更新数据或删除数据时使用
        //readerCachetoString()读取静态文件加入缓存使用
        //方法参数cacheManager 缓存管理器，”userinfo”数据库表名创建缓存区块，null默认参数”select”可以自定义
        //“1”为自定义缓存key_id,”all”多条记录查询,jdbc 数据库管理器,sql 数据库语句。
        String re= CacheUtils.selectCachetoJson(cacheManager,"userinfo",null,1,"all",jdbc,sql);
            return re;
    }
    @RequestMapping("/deAll")
    public String view1(){
        String sql="delete * from userinfo";
        jdbc.noquery(sql);
        CacheUtils.updateCache(cacheManager,"userinfo",null,1,"all");
        return "删除成功";
    }
    @RequestMapping("/sel")
    public String view2(){
        String sql="select * from userinfo where userName=’1100001’";
        String re= CacheUtils.selectCachetoJson1(cacheManager,"userinfo",null,1,"1100001",jdbc,sql);
        return re;
    }

    @RequestMapping("/de")
    public String view3(){
        String sql="delete * from userinfo where userName=’1100001’"; 
        jdbc.noquery(sql);
        CacheUtils.updateCache(cacheManager,"userinfo",null,1,"1100001");
        return "删除成功";
    }
    @RequestMapping("/up")
    public String view4(){
        String sql="update  userinfo set password =’sasasa’ where userName=’1100001’";
        jdbc.noquery(sql);
        CacheUtils.updateCache(cacheManager,"userinfo",null,1,"1100001");
        return "更新成功";
    }
    @RequestMapping("/in")
    public String view5(){
        String sql="insert  userinfo values(‘’,’’,’’,’’,’’,.....) ";
        jdbc.noquery(sql);
        CacheUtils.updateCache(cacheManager,"userinfo",null,1,"all");
        return "录入成功";
    }
    //不使用缓存插入语句
    @RequestMapping("/in1")
    public String view6(){
        String sql="insert  userinfo values(‘’,’’,’’,’’,’’,.....) ";
        jdbc.noquery(sql);
        return "录入成功";
    }
    //不使用缓存查询语句
    @RequestMapping("/sel1")
    public String view7(){
        String sql="insert  userinfo values(‘’,’’,’’,’’,’’,.....) ";
        //String re=jdbc.tabletoJson(sql);//json格式
        //String re=jdbc.tabletoJson1(sql);//json格式

        //String re=jdbc.ajaxretuen1(sql);//字符串
        String re=jdbc.ajaxretuen(sql);//字符串

        return re;
    }
    //不使用缓存删除语句

    @RequestMapping("/de1")
    public String view8(){
        String sql="delete * from userinfo where userName=’1100001’"; 
        jdbc.noquery(sql);
        return "删除成功";

    }
    //不使用缓存更新语句

    @RequestMapping("/up1")
    public String view9(){
        String sql="update  userinfo set password =’sasasa’ where userName=’1100001’";
        jdbc.noquery(sql);
        return "更新成功"; 
    }
    }
