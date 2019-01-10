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

Service service = Aop.get\(Service.class\); //jfinal 3.5 版本以上

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
        //final ProjectAdminService me = new ProjectAdminService();//引入测试的service层


    }
}
```

### template engine如果后台返回的数据是json格式，如何在页面获取：

\#for 指令可以对 List、数组、Map、Set 等类型迭代，不能对 String 内部的数据进行迭代

将其转换成 Map 类型再迭代即可

### jfinal查询数据，jfinal框架有意思的地方

sql：

```
#sql("all")
  SELECT e.*, ei.name, ei.cover, ei.intro, ei.lang //注意这里的查询结果 这里是两个表
  FROM event e
  INNER JOIN event_info ei USING (event_id)
  WHERE e.status = 1
  #if(lang) AND ei.lang = #para(lang) #end
  #if(type) AND e.type = #para(type) #end
  ORDER BY e.edit_time DESC
  LIMIT #para(limitNum)
#end
```

service层

```
public List<Event> findAll(int limitNum, String lang, String type) { //返回了event对象
        Kv param = Kv.by("limitNum", limitNum)
                .set("lang", EventConst.LANG_EN.equals(lang) ? EventConst.LANG_EN : EventConst.LANG_CH).set("type", type);
        return Event.dao.find(Db.getSqlPara("event.all", param));
    }
```

在controller层中 获取数据时：

```
Record data = new Record();
data.set("openMode", event.getOpenMode());
data.set("openTag", event.getOpenTag());
data.set("link", event.getLink()); //是event的属性
data.set("name", event.get("name"));//不是event的属性
data.set("cover", cover);
data.set("intro", event.get("intro"));
data.set("lang", event.get("lang"));
data.set("type", event.getType());
```



