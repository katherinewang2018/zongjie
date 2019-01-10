查询语句：

```
SELECT method FROM dapp_method WHERE dapp_id = #para(dappId)
```

DAO层：

```
        Kv param = Kv.by("dappId", dappId);
        SqlPara sqlPara = Db.getSqlPara("dappMethod.method", param);
        return Db.query(sqlPara.getSql(), sqlPara.getPara()); //返回list集合
```

表关系为一对多，一个dappId可以对应多个dapp\_method

使用 @Inject 注解可以向 Controller 以及 Interceptor 中注入依赖对象。

所以只有controller和interceptor中可以这样用。并且确认配置里面是否

me.setInjectDependency\(true\);

如果在其他情况中使用是这样用：

Service service = Aop.get\(Service.class\);

### 测试service层

```
public class ActiveRecordTest {
    public static void main(String[] args) {
        Prop p = PropKit.use("数据库连接配置文件.txt");


        DruidPlugin dp = new DruidPlugin(p.get("jdbcUrl"), p.get("user"), p.get("password").trim());
        ActiveRecordPlugin arp = new ActiveRecordPlugin(dp);
        arp.addMapping("project", Project.class);

        // 与 jfinal web 环境唯一的不同是要手动调用一次相关插件的start()方法
        dp.start();
        arp.start();

        // 通过上面简单的几行代码，即可立即开始使用
        public static final ProjectAdminService me = new ProjectAdminService();//引入测试的service层


    }
}
```



