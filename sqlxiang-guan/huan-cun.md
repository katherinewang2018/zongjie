可以参考：[https://www.cnblogs.com/woshimrf/p/4957369.html](https://www.cnblogs.com/woshimrf/p/4957369.html)

项目中一般是使用二级缓存:开源中国的j2cache 和final框架集成的ehcache

jfinal项目中配置缓存：\(增加缓存时，取时，先从缓存中取值，取不到时才去数据库查；编辑成功时也要重新刷新缓存\)

1.主配置类中：

```
@Override
    public void configPlugin(Plugins plugins) {

        //添加缓存插件
        plugins.add(new J2CachePlugin());
```

2.J2CachePlugin

```
public class J2CachePlugin implements IPlugin {

    private static CacheChannel cache;

    @Override
    public boolean start() {
        try {
            //禁止ipv6
            System.setProperty("java.net.preferIPv4Stack", "true");
            cache = J2Cache.getChannel();
            J2CacheKit.init(cache);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    @Override
    public boolean stop() {
        try {
            cache.close();
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }
}
```

3.在res文件夹中添加j2cache.properties 配置文件

    #J2Cache configuration


    #########################################
    # Cache Broadcast Method
    # values:
    # jgroups -> use jgroups's multicast
    # redis -> use redis publish/subscribe mechanism (using jedis)
    # lettuce -> use redis publish/subscribe mechanism (using lettuce, Recommend)
    # rabbitmq -> use RabbitMQ publisher/consumer mechanism
    # rocketmq -> use RocketMQ publisher/consumer mechanism
    # none -> don't notify the other nodes in cluster
    # xx.xxxx.xxxx.Xxxxx your own cache broadcast policy classname that implement net.oschina.j2cache.cluster.ClusterPolicy
    #########################################

    j2cache.broadcast = redis

    #########################################
    # Level 1&2 provider
    # values:
    # none -> disable this level cache
    # ehcache -> use ehcache2 as level 1 cache
    # ehcache3 -> use ehcache3 as level 1 cache
    # caffeine -> use caffeine as level 1 cache(only in memory)
    # redis -> use redis as level 2 cache (using jedis)
    # lettuce -> use redis as level 2 cache (using lettuce)
    # readonly-redis -> use redis as level 2 cache ,but never write data to it. if use this provider, you must uncomment `j2cache.L2.config_section` to make the redis configurations available.
    # memcached -> use memcached as level 2 cache (xmemcached),
    # [classname] -> use custom provider
    #########################################

    j2cache.L1.provider_class = ehcache
    j2cache.L2.provider_class = redis

    # When L2 provider isn't `redis`, using `L2.config_section = redis` to read redis configurations
    # j2cache.L2.config_section = redis

    # Enable/Disable ttl in redis cache data (if disabled, the object in redis will never expire, default:true)
    # NOTICE: redis hash mode (redis.storage = hash) do not support this feature)
    j2cache.sync_ttl_to_redis = true

    # Whether to cache null objects by default (default false)
    j2cache.default_cache_null_object = true

    #########################################
    # Cache Serialization Provider
    # values:
    # fst -> using fast-serialization (recommend)
    # kyro -> using kyro serialization
    # json -> using fst's json serialization (testing)
    # fastjson -> using fastjson serialization (embed non-static class not support)
    # java -> java standard
    # [classname implements Serializer]
    #########################################

    j2cache.serialization = fst
    #json.map.person = net.oschina.j2cache.demo.Person

    #########################################
    # Ehcache configuration
    #########################################

    # ehcache.configXml = /ehcache.xml

    # ehcache3.configXml = /ehcache3.xml
    # ehcache3.defaultHeapSize = 1000

    #########################################
    # Caffeine configuration
    # caffeine.region.[name] = size, xxxx[s|m|h|d]
    #
    #########################################
    caffeine.properties = /caffeine.properties

    #########################################
    # Redis connection configuration
    #########################################

    #########################################
    # Redis Cluster Mode
    #
    # single -> single redis server
    # sentinel -> master-slaves servers
    # cluster -> cluster servers (数据库配置无效，使用 database = 0）
    # sharded -> sharded servers  (密码、数据库必须在 hosts 中指定，且连接池配置无效 ; redis://user:password@127.0.0.1:6379/0）
    #
    #########################################

    redis.mode = single

    #redis storage mode (generic|hash)
    redis.storage = generic

    ## redis pub/sub channel name
    redis.channel = j2cache
    ## redis pub/sub server (using redis.hosts when empty)
    redis.channel.host =

    #cluster name just for sharded
    redis.cluster_name = j2cache

    ## redis cache namespace optional, default[empty]
    redis.namespace = j2cache

    ## connection
    # Separate multiple redis nodes with commas, such as 192.168.0.10:6379,192.168.0.11:6379,192.168.0.12:6379

    redis.hosts = 127.0.0.1:6379
    redis.timeout = 2000
    redis.password = wwf12345
    redis.database = 1

    ## redis pool properties
    redis.maxTotal = 100
    redis.maxIdle = 10
    redis.maxWaitMillis = 5000
    redis.minEvictableIdleTimeMillis = 60000
    redis.minIdle = 1
    redis.numTestsPerEvictionRun = 10
    redis.lifo = false
    redis.softMinEvictableIdleTimeMillis = 10
    redis.testOnBorrow = true
    redis.testOnReturn = false
    redis.testWhileIdle = true
    redis.timeBetweenEvictionRunsMillis = 300000
    redis.blockWhenExhausted = false
    redis.jmxEnabled = false

4.在res文件夹中添加ehcache.xml配置文件

```
<ehcache updateCheck="false" dynamicConfig="false"  >

    <diskStore path="java.io.tmpdir"/>

    <cacheManagerEventListenerFactory class="" properties=""/>

    <!--Default Cache configuration. These will applied to caches programmatically created through
        the CacheManager.

        The following attributes are required for defaultCache:

        maxInMemory       - Sets the maximum number of objects that will be created in memory
        eternal           - Sets whether elements are eternal. If eternal,  timeouts are ignored and the element
                            is never expired.
        timeToIdleSeconds - Sets the time to idle for an element before it expires. Is only used
                            if the element is not eternal. Idle time is now - last accessed time
        timeToLiveSeconds - Sets the time to live for an element before it expires. Is only used
                            if the element is not eternal. TTL is now - creation time
        overflowToDisk    - Sets whether elements can overflow to disk when the in-memory cache
                            has reached the maxInMemory limit.

        -->
    <defaultCache
            maxElementsInMemory="500"
            eternal="false"
            timeToIdleSeconds="60"
            timeToLiveSeconds="120"
            overflowToDisk="true"
            memoryStoreEvictionPolicy="LFU">
        <!--  cacheEventListenerFactory class="net.oschina.j2cache.ehcache.EhCacheEventListenerFactory"/-->
    </defaultCache>

    <!--Predefined caches.  Add your cache configuration settings here.
        If you do not have a configuration for your cache a WARNING will be issued when the
        CacheManager starts

        The following attributes are required for defaultCache:

        name              - Sets the name of the cache. This is used to identify the cache. It must be unique.
        maxInMemory       - Sets the maximum number of objects that will be created in memory
        eternal           - Sets whether elements are eternal. If eternal,  timeouts are ignored and the element
                            is never expired.
        timeToIdleSeconds - Sets the time to idle for an element before it expires. Is only used
                            if the element is not eternal. Idle time is now - last accessed time
        timeToLiveSeconds - Sets the time to live for an element before it expires. Is only used
                            if the element is not eternal. TTL is now - creation time
        overflowToDisk    - Sets whether elements can overflow to disk when the in-memory cache
                            has reached the maxInMemory limit.

        -->

    <!--币价，最长存活30min，空闲间隔15min-->
    <cache name="myCache1"
           maxElementsInMemory="500"
           eternal="false"
           timeToIdleSeconds="900"
           timeToLiveSeconds="1800"
           overflowToDisk="true"
           >
    </cache>

    <!--chain，最长存活10min，空闲间隔5min-->
    <cache name="myCache2"
           maxElementsInMemory="500"
           eternal="false"
           timeToIdleSeconds="300"
           timeToLiveSeconds="600"
           overflowToDisk="true"
           >
    </cache>
</ehcache>
```

5.pom文件

```
       		 <dependency>
			<groupId>net.oschina.j2cache</groupId>
			<artifactId>j2cache-core</artifactId>
			<version>2.7.6-release</version>
		</dependency>
		<dependency>
			<groupId>net.sf.ehcache</groupId>
			<artifactId>ehcache</artifactId>
			<version>2.9.1</version>
		</dependency>

		<!--redis-->
		<dependency>
			<groupId>redis.clients</groupId>
			<artifactId>jedis</artifactId>
			<version>2.9.0</version>
		</dependency>
```

6.在进行缓存操作时，使用工具类

```
package com.eshanren.ext.cache;

import com.eshanren.util.StringUtil;
import net.oschina.j2cache.CacheChannel;


/**
 * J2cache 工具
 *
 * @author WWF
 */
public class J2CacheKit {
    /**
     * 缓存通道
     */
    private static CacheChannel cache;

    public static void init(CacheChannel cache) {
        J2CacheKit.cache = cache;
    }

    public static void set(String key, Object value) {
        set("cache", key, value);
    }

    public static <T> T set(String cacheName, String key, Object value) {
        if (null == value) {
            return null;
        }
        try {
            cache.set(cacheName, key, value);
        } catch (Exception e) {
        }
        return (T) value;
    }

    public static <T> T setByKeys(String cacheName, Object value, String... params) {
        if (null == value) {
            return null;
        }
        set(cacheName, StringUtil.contractAndTrim(params), value);
        return (T) value;
    }

    public static <T> T get(String key) {
        return get("cache", key);
    }

    public static <T> T get(String cacheName, String key) {
        try {
            return (T) cache.get(cacheName, key).getValue();
        } catch (Exception e) {
            return null;
        }
    }

    public static <T> T getByKeys(String cacheName, String... params) {
        return (T) cache.get(cacheName, StringUtil.contractAndTrim(params)).getValue();
    }

    public static void remove(String key) {
        remove("cache", key);
    }

    public static void remove(String cacheName, String key) {
        try {
            if (null == key) {
                removeAll(cacheName);
            } else {
                cache.evict(cacheName, key);
            }
        } catch (Exception e) {
        }
    }

    public static void removeAll(String cacheName) {
        try {
            cache.clear(cacheName);
        } catch (Exception e) {
        }
    }
}
```

8.配置的模板

```
这些配置文件的模板可以从 https://gitee.com/ld/J2Cache/tree/master/core/resources 这里获取。
```

另一种可以使用jfinal自己集成的ehcache缓存

1.配置类中：

```
@Override
    public void configPlugin(Plugins plugins) {

        //添加缓存插件
        plugins.add(new EhCachePlugin());
```

2.跟上面一样也要配置ehcache.xml

3.使用 例子

```
CacheKit.put(DataConfigService.givenCacheName, "givenEos", givenConfig.getValue());

CacheKit.put(cacheName, key, value);

//从缓存中取数据
CacheKit.get(cacheName, key);

//这个直接使用就是加入缓存了
Db.findByCache(cacheName, key, sql);
```

## **ps：最后是版本问题：**









