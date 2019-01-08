查询语句：

```
SELECT method FROM dapp_method WHERE dapp_id = #para(dappId)
```

DAO层：

```
        Kv param = Kv.by("dappId", dappId);
        SqlPara sqlPara = Db.getSqlPara("dappMethod.method", param);
        return Db.query(sqlPara.getSql(), sqlPara.getPara());
```



