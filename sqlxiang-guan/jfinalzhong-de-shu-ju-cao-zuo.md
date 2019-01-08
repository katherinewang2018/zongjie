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

