---
title: Go json反序列化interface{}对int64处理
tags: Go
categories: 笔记
keywords:
  - Go
  - json
  - 反序列化
  - 精度丢失
  - int64
  - float64
  - interface
description: 最近在项目中遇到一个坑，Go语言在json反序列化时，如果未指定类型，则数字（比如int64）会默认是 float64，这样再次序列化的时候造成了精度丢失。具体可以看文章中的代码示例
date: 2019-08-21 20:22:11
---
## 问题
最近在项目中遇到一个坑，Go语言在json反序列化时，如果未指定类型，则数字（比如int64）会默认是 float64，这样再次序列化的时候造成了精度丢失。
具体可以看如下代码


```
package main

import (
	"fmt"

	jsoniter "github.com/json-iterator/go"
)

func main() {
	s := "{\"a\":6673221165400540161}"

	d := make(map[string]interface{})
	err := jsoniter.Unmarshal([]byte(s), &d)
	if err != nil {
		panic(err)
	}

	s2, err := jsoniter.Marshal(d)
	if err != nil {
		panic(err)
	}

	fmt.Println(string(s2))
}
```

代码执行结果是： {"a":6673221165400540000}

原始数据是：
{"a":6673221165400540161}

产生了精度丢失。

## 解决办法


```
package main

import (
	"fmt"
	jsoniter "github.com/json-iterator/go"
	"strings"
)

func main() {
	s := "{\"a\":6673221165400540161}"
	decoder := jsoniter.NewDecoder(strings.NewReader(s))
	decoder.UseNumber()
	d := make(map[string]interface{})
	err := decoder.Decode(&d)
	if err != nil {
		panic(err)
	}

	s2, err := jsoniter.Marshal(d)
	if err != nil {
		panic(err)
	}

	fmt.Println(string(s2))
}
```

上面的程序，使用了 func (*Decoder) UseNumber 方法告诉反序列化 json 的数字类型的时候，不要直接转换成 float64，而是转换成 json.Number 类型。

json.Number 内部实现机制：


```
// A Number represents a JSON number literal.
type Number string

// String returns the literal text of the number.
func (n Number) String() string { return string(n) }

// Float64 returns the number as a float64.
func (n Number) Float64() (float64, error) {
    return strconv.ParseFloat(string(n), 64)
}

// Int64 returns the number as an int64.
func (n Number) Int64() (int64, error) {
    return strconv.ParseInt(string(n), 10, 64)
}
```

json.Number 本质是字符串，反序列化的时候将 json 的数值先转成 json.Number，其实是一种延迟处理的手段，待后续逻辑需要时候，再把 json.Number 转成 float64 或者 int64。

## 特别鸣谢
[参考链接](http://70data.net/1876.html)
