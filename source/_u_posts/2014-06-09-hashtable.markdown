---
layout: post
title: "设计一个动态平衡的哈希表"
date: 2014-06-09 01:20:02 +0800
comments: true
categories: 
---

最近周六在上 July 组织的算法课，学习了很多当年大学错过的课程，感觉收获很多，尤其是Ben的课程，很大的鼓励，很丰富的内容，以下内容是一个作业题：如何设计一个动态平衡的哈希表。

#什么是动态平衡的哈希表
1. 哈希表是一种数据结构；
2. 动态平衡：随着哈希表中数据的增加，负载率保持不变；

什么是哈希表（hashtable），什么是负载率（load factor），请大家来参加 July举行的算法课，或者推荐你看一下 MIT 的“算法导论”课程中的第七课“哈希表”，你可以在网易云课堂中找到。
<!--more-->

#哈希表的实现

我主要说一下我做这个题的思路；

####哈希表的实现方法
我们知道哈希表有开发地址法（Open addressing）和链表法（Chaining），因为开发地址法的最大负载率是1，所以开发地址法常常更需要实现动态平衡，所以我们选择使用开发地址法来实现；

####开发地址法
开发地址法的实现也可以有很多种，但是我们选取最简单的一种，就是如果一个 slot 被暂用，就向后一个地址查询，知道找到空的 slot，然后把对应 {key:value} 放进去；

####数据结构

需要有两个数据结构，一个是Hashtable，Hashtable是一个数组；另一个是Entry，就是放在这个数组上的一个一个的entry；整体思路是：我们先创建一个空的Hashtable，然后把一个一个的Entry放到这个数组里面；

####确定数据结构原型

大家可以通过我的注释来了解我代码的意思；
```c++
class Entry {
private:
  int key;
  int value;
public:
  Entry (int key, int value);
  int getKey ();
  int getValue ();
};
```

```c++
class Hashtable {
private:
  int size;
  //存放一个一个entry的数组
  Entry **table;
  //已占用的位置个数
  int occupiedSize;
  //负载率临界值
  float loadFactorThreshold;
  //负载率超过临界值后，需要动态修改 table 的大小
  void resize();

public:
  Hashtable (int size);
  //拿数据
  int get (int key);
  //放数据
  void put (int key, int value);  
  //hash key后，进行存放
  int hash (int key);
  int getSize ();
  int getOccupiedSize ();
  ~Hashtable ();
};

```

####写单元测试

有了原型之后，我们就可以来书写单元测试代码了，通过单元测试，我们很快的就可以定位到自己的问题；我们先写好最简单的测试；

```c++
#include <iostream>
#include <assert.h>
using namespace std;

int main () {
  Hashtable * table10 = new Hashtable(10);
  table10->put(1, 100);
  table10->put(2, 200);
  table10->put(3, 300);
  assert(table10->get(1) == 100);
  assert(table10->get(2) == 200);
  assert(table10->get(3) == 300);
  assert(table10->getSize() == 10);
  assert(table10->getOccupiedSize() == 3);


  Hashtable * table2 = new Hashtable(2);
  table2->put(1, 100);
  table2->put(2, 200);
  table2->put(3, 300);
  assert(table2->get(1) == 100);
  assert(table2->get(2) == 200);
  assert(table2->get(3) == 300);
  assert(table2->getSize() == 8);
  assert(table2->getOccupiedSize() == 3);

  cout << "The tests run successfully." << endl;

}
```

####实现方法

实现方法的时候，可以边实现边测试，一会会你就完成了;

```c++
Entry::Entry (int key, int value) {
  this->key = key;
  this->value = value;
}

int Entry::getKey () {
  return this->key;
}

int Entry::getValue () {
  return this->value;
}

Hashtable::Hashtable (int size) {
  assert(size > 0);
  this->size = size;
  this->occupiedSize = 0;
  this->loadFactorThreshold = 0.5;
  table = new Entry* [size];
  for (int i = 0; i < size; i++) {
    table[i] = NULL;
  }
}

void Hashtable::put (int key, int value) {
  int hash = this->hash(key);
  int count = 0;
  while(table[hash] != NULL) {
    //全部位置遍历后，如果仍然没有空位，返回；这个在动态平衡的哈希表中不会出现
    if (count >= this->size) {
      cout << "The hashtable is full, failed to add {key:" << key << " value:" << value << "}" << endl;
      return;
    }
    hash = (hash + 1) % this->size;
    count ++;
  }
  table[hash] = new Entry(key, value);
  this->occupiedSize ++;
  float currentLoadFactor = (float)this->occupiedSize / (float)this->size;
  if( currentLoadFactor > this->loadFactorThreshold) {
    resize();
  }
}

int Hashtable::get (int key) {
  int hash = this->hash(key);
  int count = 0;
  while(table[hash] != NULL) {
    if(table[hash]->getKey() == key) {
      return table[hash]->getValue();
    }
    //全部位置遍历后，仍然没有找到对应的key，返回-1
    if (count >= this->size) {
      return -1;
    }
    hash = (hash + 1) % this->size;
    count ++;
  }

  return -1;
}

int Hashtable::hash (int key) {
  return key % this->size;
}
int Hashtable::getSize () {
  return this->size;
}
int Hashtable::getOccupiedSize () {
  return this->occupiedSize;
}

void Hashtable::resize () {
  int oldSize = this->size;
  Entry ** oldTable = this->table;
  size = oldSize * 2;
  table = new Entry* [size];
  occupiedSize = 0;

  for (int i = 0; i < size; i++) {
    table[i] = NULL;
  }


  for (int i = 0; i < oldSize; i++) {
    if (NULL != oldTable[i]) {
      put(oldTable[i]->getKey(), oldTable[i]->getValue());
    }
  }

}

Hashtable::~Hashtable () {
  for (int i = 0; i < size; i++) {
    if(table[i]) {
      delete table[i];
    }
  }
  delete [] table;
}

```

####在线测试

<script src="http://ideone.com/e.js/guXhB4" type="text/javascript" ></script>


我没有做过C++的项目，一直写JavaScript，所以以上代码任何写的不正确的、烂的地方，还请大家多多指教，非常感谢；