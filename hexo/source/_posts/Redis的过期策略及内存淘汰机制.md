---
title: Redis的过期策略及内存淘汰机制
tags: Redis
categories: 专题
keywords:
  - Redis
  - 过期策略
  - 淘汰机制
description: >-
  对于一个缓存型redis，如果本身只有10G的容量，那么流量增大，写入了15G的数据，那么对于原本的10G数据是按照怎样的策略进行删除呢？在实际使用过程中，redis里数据已经设置了过期时间，但是时间到了，内存占用率还是比较高，数据没有删除的原因是什么呢？今天一起来看下。
abbrlink: 6bdb61fa
date: 2019-04-28 22:35:27
---

对于一个缓存型redis，如果本身只有10G的容量，那么流量增大，写入了15G的数据，那么对于原本的10G数据是按照怎样的策略进行删除呢？在实际使用过程中，redis里数据已经设置了过期时间，但是时间到了，内存占用率还是比较高，数据没有删除的原因是什么呢？今天一起来看下。

## 问题:redis的过期删除策略以及内存淘汰机制

### 定时删除

用一个定时器对某个key进行监视，过期则自动删除。这种方法的好处是内存会及时释放，但是由于使用到了定时器，比较消耗CPU资源，当流量比较大的时候，并不适合，所以redis并没有采用这种方案

### 定期删除

redis默认每过100ms检查一次，当前是否有过期的key,有过期key则进行删除操作。需要说明的是，redis不是每个100ms将所有的key检查一次，而是随机抽取进行检查(如果每隔100ms,全部key进行检查，redis不用做别的事情了。。。)。因此，如果只采用定期删除策略，会导致很多key到时间没有删除。

### 惰性删除

类似懒加载，当请求获取某个key的时候，redis会检查一下，这个key如果设置了过期时间，那么会判断该key是否过期？如果过期了会执行删除操作。

### 内存淘汰机制

按照上面的方法，对于某些key，如果定期删除一直没有选中（运气比较差）。即时去请求key的时候，也没有请求到（比较冷门的key）,惰性删除没生效。这样，redis的内存会同样会越来越高。那么就应该采用内存淘汰机制。

在redis的conf文件中，可以对内存淘汰策略进行配置。

1. noeviction：当内存不足以容纳新写入数据时，新写入操作会报错。应该没人用吧。
2. allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key。推荐使用，目前项目在用这种。
3. allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个key。应该也没人用吧，你不删最少使用Key,去随机删。。。
4. volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的key。这种情况一般是把redis既当缓存，又做持久化存储的时候才用。不推荐
5. volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个key。依然不推荐
6. volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的key优先移除。不推荐

ps：如果没有设置 expire 的key, 不满足先决条件(prerequisites); 那么 volatile-lru, volatile-random 和 volatile-ttl 策略的行为, 和 noeviction(不删除) 基本上一致。

所以总的来说，redis是通过**定期删除+惰性删除+配置的内存淘汰机制**来保证内存可用的。

## 特别鸣谢

[参考链接](https://note.youdao.com/)