---
layout: post
title: "设计一个动态平衡的哈希表"
date: 2014-06-09 01:20:02 +0800
comments: true
categories: 
---

最近周六在上 July 组织的算法课，学习了很多当年大学错过的课程，感觉收获很多，尤其是Ben的课程，很大的鼓励，很丰富的内容，以下内容是一个作业题：如何设计一个动态平衡的哈希表。

##什么是动态平衡的哈希表
1. 哈希表是一种数据结构；
2. 动态平衡：随着哈希表中数据的增加，负载率保持不变；

什么是哈希表（hashtable），什么是负载率（load factor），请大家来参加 July 举行的算法课，这里的老师都是大神，或者推荐你看一下 MIT 的“算法导论”课程中的第七课“哈希表”，你可以在网易云课堂中找到。
<!--more-->


##哈希表的实现

我主要说一下我做这个题的思路；

###哈希表的实现方法
我们知道哈希表有开放地址法（Open addressing）和链表法（Chaining），因为开放地址法的最大负载率是1，所以开放地址法常常更需要实现动态平衡，所以我们选择使用开放地址法来实现；

####开放地址法
开放地址法的实现也可以有很多种，但是我们选取最简单的一种，就是如果一个 slot 被暂用，就向后一个地址查询，知道找到空的 slot，然后把对应 {key:value} 放进去；

###数据结构

需要有两个数据结构，一个是Hashtable，Hashtable是一个数组；另一个是Entry，就是放在这个数组上的一个一个的entry；整体思路是：我们先创建一个空的Hashtable，然后把一个一个的Entry放到这个数组里面；

###确定数据结构原型

大家可以通过我的注释来了解我代码的意思；
```cpp
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

```cpp
class Hashtable {
private:
  int size;
  Entry **table;
  //已占用的位置个数
  int occupiedSize;
  //负载率临界值
  float minLoadFactorThreshold;
  float maxLoadFactorThreshold;
  int maxTableSize;
  int minTableSize;
  static Entry * deleteEntry;
  void resize(double factor);

public:
  Hashtable (int size);
  int get (int key);
  void put (int key, int value);
  int hash (int key);
  int getSize ();
  int getOccupiedSize ();
  void remove (int key);
  ~Hashtable ();
};

```

###写单元测试

有了原型之后，我们就可以来书写单元测试代码了，通过单元测试，我们很快的就可以定位到自己的问题；我们先写好最简单的测试；

```cpp
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


  //测试当负载率大于maxLoadFactorThreshold，要扩大 size * 2
  Hashtable * table2 = new Hashtable(2);
  table2->put(1, 100);
  table2->put(2, 200);
  table2->put(3, 300);
  assert(table2->get(1) == 100);
  assert(table2->get(2) == 200);
  assert(table2->get(3) == 300);
  assert(table2->getSize() == 8);
  assert(table2->getOccupiedSize() == 3);


  //测试删除和最小删除值
  Hashtable * table11 = new Hashtable(11);
  table11->put(1, 100);
  table11->put(2, 200);
  table11->put(3, 300);
  table11->put(4, 400);
  table11->put(5, 500);
  table11->put(6, 600);
  table11->put(7, 700);
  table11->put(8, 800);
  table11->put(9, 900);
  table11->put(10, 1000);
  table11->put(11, 1100);
  table11->put(12, 1200);
  assert(table11->get(8) == 800);
  assert(table11->getSize() == 44);
  assert(table11->getOccupiedSize() == 12);
  table11->remove(8);
  assert(table11->get(8) == -1);
  table11->remove(7);
  // 10 / 44 = 0.2 0.2 < 0.25 所以要压缩
  assert(table11->getSize() == 22);
  assert(table11->getOccupiedSize() == 10);
  table11->remove(6);
  table11->remove(5);
  table11->remove(4);
  table11->remove(3);
  table11->remove(2);
  // 5 / 22 = 0.2 0.2 < 0.25 所以要压缩
  assert(table11->getSize() == 11);
  assert(table11->getOccupiedSize() == 5);
  table11->remove(1);
  table11->remove(9);
  table11->remove(10);
  // 2 / 11 = 0.18 0.18 < 0.25 但是 11/2 < 10 ，所以不压缩
  assert(table11->getSize() == 11);
  assert(table11->getOccupiedSize() == 2);

  cout << "The tests run successfully." << endl;

}
```

###实现方法

实现方法的时候，可以边实现边测试，一会会你就完成了;

```cpp

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


Entry * Hashtable::deleteEntry = new Entry(-1, -1);

Hashtable::Hashtable (int size) {
  assert(size > 0);
  this->size = size;
  occupiedSize = 0;
  minTableSize = MIN_TABLE_SIZE;
  maxTableSize = MAX_TABLE_SIZE;
  minLoadFactorThreshold = MIN_LOAD_FACTOR_THREHOLD;
  maxLoadFactorThreshold = MAX_LOAD_FACTOR_THREHOLD;
  table = new Entry* [size];
  for (int i = 0; i < size; i++) {
    table[i] = NULL;
  }
}

void Hashtable::put (int key, int value) {
  int hash = this->hash(key);
  int count = 0;
  while(table[hash] != NULL) {
    //全部位置遍历后，如果仍然没有空位，返回；
    if (count >= size) {
      cout << "The hashtable is full, failed to add {key:" << key << " value:" << value << "}" << endl;
      return;
    }
    hash = (hash + 1) % size;
    count ++;
  }
  table[hash] = new Entry(key, value);
  occupiedSize ++;
  float currentLoadFactor = (float)occupiedSize / (float)size;
  if( currentLoadFactor > maxLoadFactorThreshold && size * 2 < maxTableSize) {
    resize(2);
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
    if (count >= size) {
      return -1;
    }
    hash = (hash + 1) % size;
    count ++;
  }

  return -1;
}

int Hashtable::hash (int key) {
  return key % size;
}
int Hashtable::getSize () {
  return size;
}
int Hashtable::getOccupiedSize () {
  return occupiedSize;
}

void Hashtable::resize (double factor) {
  int oldSize = size;
  Entry ** oldTable = table;
  size = (int) oldSize * factor;
  table = new Entry* [size];
  occupiedSize = 0;

  for (int i = 0; i < size; i++) {
    table[i] = NULL;
  }


  for (int i = 0; i < oldSize; i++) {
    if (NULL != oldTable[i] &&  oldTable[i] != deleteEntry) {
      put(oldTable[i]->getKey(), oldTable[i]->getValue());
    }
  }

  delete[] oldTable;

}

void Hashtable::remove (int key) {
  int hash = this->hash(key);
  int count = 0;
  while(table[hash] != NULL) {
    if(table[hash]->getKey() == key) {
      delete table[hash];
      table[hash] = deleteEntry;
      occupiedSize --;
      // cout << "remove and occupiedSize: " << occupiedSize <<  endl;
      float currentLoadFactor = (float)occupiedSize / (float)size;
      if( currentLoadFactor < minLoadFactorThreshold && size / 2 > minTableSize) {
        resize(0.5);
      }

    }
    //全部位置遍历后，仍然没有找到对应的key
    if (count >= size) {
      cout << "没有找到你要删除的 key: " << key << endl;
      return;
    }
    hash = (hash + 1) % size;
    count ++;
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

###在线测试

<script src="http://ideone.com/e.js/QWKgw8" type="text/javascript" ></script>


我没有做过C++的项目，一直写JavaScript，所以以上代码任何写的不正确的、烂的地方，还请大家多多指教，非常感谢；