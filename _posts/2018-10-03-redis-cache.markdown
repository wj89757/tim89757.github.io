---
layout:     post
title:      "Redis缓存实践"
subtitle:   ""
date:       2018-10-03 06:30:00
author:     "Tim"
header-img: "img/post-bg-2015.jpg"
tags:
    - Redis
    - Cache
    - Redis缓存
---

> Redis缓存实践

### redis 简介

简单来说 redis 就是一个数据库，不过与传统数据库不同的是 redis 的数据是存在内存中的，所以存写速度非常快，因此 redis 被广泛应用于缓存方向。另外，redis 也经常用来做分布式锁。redis 提供了多种数据类型来支持不同的业务场景。除此之外，redis 支持事务 、持久化、LUA脚本、LRU驱动事件、多种集群方案。 

### 为什么要用 redis /为什么要用缓存

主要从“高性能”和“高并发”这两点来看待这个问题。

**高性能：**

假如用户第一次访问数据库中的某些数据。这个过程会比较慢，因为是从硬盘上读取的。将该用户访问的数据存在数缓存中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可！

![](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-24/54316596.jpg)


**高并发：**

直接操作缓存能够承受的请求是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存这里而不用经过数据库。


![](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-24/85146760.jpg)


### 为什么要用 redis 而不用 map/guava 做缓存?


>下面的内容来自 segmentfault 一位网友的提问，地址：https://segmentfault.com/q/1010000009106416

缓存分为本地缓存和分布式缓存。以 Java 为例，使用自带的 map 或者 guava 实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着 jvm 的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，缓存不具有一致性。

使用 redis 或 memcached 之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持 redis 或  memcached服务的高可用，整个程序架构上较为复杂。


###  redis 和 memcached 的区别

对于 redis 和 memcached 我总结了下面四点。现在公司一般都是用 redis 来实现缓存，而且 redis 自身也越来越强大了！

1. **redis支持更丰富的数据类型（支持更复杂的应用场景）**：Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，zset，hash等数据结构的存储。memcache支持简单的数据类型，String。
2. **Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用,而Memecache把数据全部存在内存之中。**
3. **集群模式**：memcached没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；但是 redis 目前是原生支持 cluster 模式的.
4. **Memcached是多线程，非阻塞IO复用的网络模型；Redis使用单线程的多路 IO 复用模型。**


> 来自网络上的一张图，这里分享给大家！

![redis 和 memcached 的区别](http://my-blog-to-use.oss-cn-beijing.aliyuncs.com/18-9-24/61603179.jpg)


### redis使用

> RedisTemplate介绍

​	spring 封装了 RedisTemplate 对象来进行对redis的各种操作，它支持所有的 redis 原生的 api。

​	RedisTemplate中定义了对5种数据结构操作

- redisTemplate.opsForValue();//操作字符串

- redisTemplate.opsForHash();//操作hash

- redisTemplate.opsForList();//操作list

- redisTemplate.opsForSet();//操作set

- redisTemplate.opsForZSet();//操作有序set

  SDR默认采用的序列化策略有两种，一种是String的序列化策略，一种是JDK的序列化策略。StringRedisTemplate默认采用的是String的序列化策略，保存的key和value都是采用此策略序列化保存的。RedisTemplate默认采用的是JDK的序列化策略，保存的key和value都是采用此策略序列化保存的。**但是最好先将对象序列化进行字符串保存，这样序列化的逻辑就由我们自己保管了**

  > 对RedisTemplate进一步封装

  ​	**1.首先是spring配置**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd"
       default-lazy-init="false">

    <!-- jedis 连接池配置参数：  -->
    <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <property name="maxActive" value="1000"/>
        <property name="maxIdle" value="50"/>
        <property name="maxWait" value="1500"/>
        <property name="testOnBorrow" value="true"/>
        <property name="testOnReturn" value="true"/>
        <property name="testWhileIdle" value="true"/>
    </bean>

    <!--redis连接工厂 -->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory" destroy-method="destroy">
        <property name="poolConfig" ref="jedisPoolConfig" />
        <!--IP地址 -->
        <property name="hostName" value="${cache.redis.host}" />
        <!--端口号  -->
        <property name="port" value="${cache.redis.port}" />
        <!--如果Redis设置有密码  -->
        <property name="password" value="${cache.redis.password}" />
        <!--客户端超时时间单位是毫秒  -->
        <property name="timeout" value="${cache.redis.timeout}" />
    </bean>

    <!--redis操作模版,使用该对象可以操作redis  -->
    <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate" >
        <property name="connectionFactory" ref="jedisConnectionFactory" />
        <!--如果不配置Serializer，那么存储的时候缺省使用String，如果用User类型存储，那么会提示错误User can't cast to String！！  -->
        <property name="keySerializer" >
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer" />
        </property>
        <property name="valueSerializer" >
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer" />
        </property>
        <property name="hashKeySerializer">
            <bean class="org.springframework.data.redis.serializer.StringRedisSerializer"/>
        </property>
        <property name="hashValueSerializer">
            <bean class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>
        </property>
    </bean>

    <!--自定义redis工具类,在需要缓存的地方注入此类  -->
    <bean id="cacheServiceImpl" class="<指定类>">
        <property name="redisTemplate" ref="redisTemplate" />
    </bean>

    <context:annotation-config/>
    <context:component-scan base-package="<指定包>"/>
</beans>
```

​	**2.自定义redis的key**

​	为了让key更好的使用，并且支持更多的类型，对key就行封装。

```java
/**
 * @author wangjun
 * @Date 2018/3/28
 */
public class CacheKey implements Serializable {
    private String key;
    private String category;

    public static CacheKey getInstance(String key, String category) {
        CacheKey cacheKey = new CacheKey();
        cacheKey.setKey(key);
        cacheKey.setCategory(category);
        return cacheKey;
    }

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }

    public String getCategory() {
        return category;
    }

    public void setCategory(String category) {
        this.category = category;
    }

    public String getKeyName() {
        return String.format("%s-%s", category, key);
    }

    public boolean isInvalid() {
        return StringUtils.isEmpty(key) || StringUtils.isEmpty(category);
    }
}
```

​	**3.自定义redis工具类**

```java
/**
 * @author wangjun
 * @Date 2018/2/26
 */
public class CacheServiceImpl {
    private Logger logger = Logger.getLogger(getClass());
    private RedisTemplate<String, Object> redisTemplate;
    private static final int EXPIRE_TIME = 60 * 60 * 24;

    public void setRedisTemplate(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    public Object get(CacheKey key) {
        if (key.isInvalid()) {
            return null;
        }
        try {
            Object cValue = redisTemplate.opsForValue().get(key.getKeyName());
            return cValue;
        } catch (Exception e) {
            return null;
        } 
    }

    public boolean set(CacheKey key, Object value) {
        if (key.isInvalid()) {
            return false;
        }
        try {
            set(key, value, EXPIRE_TIME);
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    public boolean set(CacheKey key, Object value, long time) {
        if (key.isInvalid()) {
            return false;
        }
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key.getKeyName(), value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    public boolean delete(CacheKey key) {
        if (key.isInvalid()) {
            return false;
        }
        try {
            redisTemplate.delete(key.getKeyName());
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    public boolean hasKey(CacheKey key) {
        try {
            return redisTemplate.hasKey(key.getKeyName());
        } catch (Exception e) {
            logger.error("has key ex", e);
            return false;
        }
    }

    public long getExpire(CacheKey key){
        return redisTemplate.getExpire(key.getKeyName());
    }
}
```

​	

