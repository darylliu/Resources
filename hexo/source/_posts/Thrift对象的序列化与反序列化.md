---
title: Thrift对象的序列化与反序列化
tags: Thrift
categories: 笔记
keywords:
  - Thrift
  - 序列化
  - 反序列化
  - protobuf
description: 对象的序列化与序列化，可能大家更多接触的是谷歌的protobuf。Thrift作为一个跨语言的RPC代码生成引擎，也具备此功能。本文要说的是如何使用Thrift实现对象的序列化与反序列化，其实就是，如何以protobuf的方式使用Thrift。
abbrlink: ada59da0
date: 2019-05-27 22:37:21
---
## 正文

对象的序列化与反序列化，可能大家更多接触的是谷歌的protobuf。
Thrift作为一个跨语言的RPC代码生成引擎，也具备此功能。
本文要说的是如何使用Thrift实现对象的序列化与反序列化，其实就是，如何以protobuf的方式使用Thrift。

Thrift描述文件：

```
# filename: demo.thrift
struct Node {
    1: string host
    2: i32 port
}
```

以生成的Python代码为例，Thrift生成的类型提供了两个关键方法：

```
class Node:
  """
  Attributes:
   - host
   - port
  """
  thrift_spec = (
    None, # 0
    (1, TType.STRING, 'host', None, None, ), # 1
    (2, TType.I32, 'port', None, None, ), # 2
  )
  
  def __init__(self, host=None, port=None,):
    self.host = host
    self.port = port

  def read(self, iprot):
    ...

  def write(self, oprot):
    ...
```

read/write方法按照指定协议传输对象，所以需要一个TProtocol对象。

TProtocol对象构造时需要传入一个TTransport对象，即传输层，所以还需要一个TTransport对象。

由于数据已经准备完毕，要做的只是反序列化。

好，TMemoryBuffer满足需求。

```
class TMemoryBuffer(TTransportBase, CReadableTransport):
  """Wraps a cStringIO object as a TTransport.

  NOTE: Unlike the C++ version of this class, you cannot write to it
        then immediately read from it.  If you want to read from a
        TMemoryBuffer, you must either pass a string to the constructor.
  TODO(dreiss): Make this work like the C++ version.
  """

  def __init__(self, value=None):
    """value -- a value to read from for stringio

    If value is set, this will be a transport for reading,
    otherwise, it is for writing"""
    if value is not None:
      self._buffer = StringIO(value)
    else:
      self._buffer = StringIO()

```

TMemoryBuffer继承TTransportBase，也属于一种TTransport，内部封装了一个StringIO对象。

利用目标数据构造一个TMemoryBuffer对象，然后调用read/write方法实现反序列化和序列化。

需要注意的是，Python在初始化TMemoryBuffer对象时必须指定value。

序列化/反序列化的示例代码：

```
#! /usr/bin/env python
# -*- coding: utf-8 -*-

import sys
sys.path.append('gen-py')

from thrift.transport import TTransport
from thrift.protocol import TBinaryProtocol
from demo.ttypes import *

def serialize(th_obj):
    """ Serialize. 
    """
    tmembuf = TTransport.TMemoryBuffer()
    tbinprot = TBinaryProtocol.TBinaryProtocol(tmembuf)
    th_obj.write(tbinprot)
    return tmembuf.getvalue()

def deserialize(val, th_obj_type):
    """ Deserialize. 
    """
    th_obj = th_obj_type()
    tmembuf = TTransport.TMemoryBuffer(val)
    tbinprot = TBinaryProtocol.TBinaryProtocol(tmembuf)
    th_obj.read(tbinprot)
    return th_obj

if __name__ == '__main__':
    node1 = Node('localhost', 8000)
    print 'node1:', node1

    # modified
    node1.host = '127.0.0.1'
    node1.port = 9000

    val = serialize(node1)
    node2 = deserialize(val, Node)
    print 'node2:', node2
```

输出结果：

```
node1: Node(host='localhost', port=8000)
node2: Node(host='127.0.0.1', port=9000)
```

## 特别鸣谢
[参考链接](http://caosiyang.github.io/2016/11/25/thrfit-serialize-and-deserialize/)