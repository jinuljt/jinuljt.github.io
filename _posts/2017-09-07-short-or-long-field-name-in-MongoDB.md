---
layout: post
title: "short or long field name in MongoDB"
---

# MongoDB 中使用长字段名还是短字段名的研究


起因是发现公司使用[MongoDB](https://www.mongodb.com/)的项目中使用的字段名都是很简短的缩写，对于我这样习惯使用长字段名的很不习惯。例如created_at, 项目中使用的crtAt。

对于长字段名，它的好处很明显：看字段名就能知道他的含义。而短字段名就不行了，必须要有外部资源来翻译。

遇到这种情况我考虑的是，之前的设计者为什么要这么做。

- MongoDB是一个文档型数据库，每个field都会存在document中，那么会不会因为这样节省存储空间呢？这个可能性很大，但是我觉得MongoDB肯定会做压缩不会这么傻傻的。

- 短字段名会检索更快？好像没什么道理。


针对上面两个想法使用数据生成脚本生成20w条数据，加上索引，之后测试find_one。

## 测试环境与脚本

MongoDB 运行在 Macbook 的docker中，使用的是默认的docker镜像，没做任何优化。

```
MacBook Pro(2013 Late)/2.4G i5/8GB
Docker Version 17.06.1-ce-mac24 (18950)
MongoDB server version: 3.4.7
```

### 数据生成脚本

```
import random
import datetime

from pymongo import MongoClient



columns = {
    "waagenophyllidae_id": {"short_name": "wid", "type": int},
    "placanticline _id": {"short_name": "pid", "type": int},
    "placanticline_station_id": {"short_name": 'psid', "type": int},
    "aliantista_id": {"short_name": 'aid', "type": int},
    "graafian_id": {"short_name": 'gid', "type": int},
    "coabitazione_id": {"short_name": 'cid', "type": int},
    "inbattibile_at": {"short_name": 'iat', "type": datetime.datetime},
    "ouagadougou_at": {"short_name": 'oat', "type": datetime.datetime},
    "demagnetise_amount": {"short_name": "da", "type": int},
    "caatinga_amount": {"short_name": "ca", "type": int},
    "status": {"short_name": "st", "type": int},
    "last_opacifiedglass_code": {"short_name": "loc", "type": int},
    "tranformational_in": {"short_name": "ti", "type": int},
    "tranformational_out": {"short_name": "to", "type": int},
    "created_at": {"short_name": "cat", "type": datetime.datetime},
    "modified_at": {"short_name": "mat", "type": datetime.datetime},
}


if __name__ == '__main__':
    client = MongoClient()
    db = client['test']
    long_field_collection = db['long_field_collection']
    short_field_collection = db['short_field_collection']


    long_field_data = {}
    short_field_data = {}

    for i in range(200000):
        if "_id" in long_field_data:
            long_field_data.pop("_id")
        if "_id" in short_field_data:
            short_field_data.pop("_id")

        if i and i % 20000 == 0:
            print("inserted {} document".format(i))

        for field, detail in columns.items():
            if detail['type'] is datetime.datetime:
                value = datetime.datetime(2017, random.randint(1,12), random.randint(1,28), random.randint(0,23), random.randint(0,59), random.randint(0,59))
            else:
                value = random.randint(1,100)
            long_field_data[field] = value
            short_field_data[detail['short_name']] = value
        long_field_collection.insert(long_field_data)
        short_field_collection.insert(short_field_data)

```

### 测试find_one 脚本

```
import timeit
import random
import time

from pymongo import MongoClient


mongo = MongoClient()


def repeat(collection, field, number=10000):
    s = time.time()
    for i in range(number):
        collection.find_one({field: random.randint(1,100)})
    return time.time() - s




if __name__ == '__main__':
    long_field_collection = mongo['test']['long_field_collection']
    short_field_collection = mongo['test']['short_field_collection']

    print("long field find_one test:", [repeat(long_field_collection, 'waagenophyllidae_id') for i in range(5)])
    print("short field find_one test:", [repeat(short_field_collection, 'wid') for i in range(5)])

```


## short field name 是否更省空间


### 不影响 index 大小
```
> db.short_field_collection.totalIndexSize()
2990080
> db.long_field_collection.totalIndexSize()
2957312
```

### 影响存储空间大小
```
> db.short_field_collection.storageSize()
21504000
> db.long_field_collection.storageSize()
33783808
```

### 非常影响数据大小
```
> db.short_field_collection.dataSize()
35600000
> db.long_field_collection.dataSize()
76400000
```

### 影响 totalSize

```
> db.short_field_collection.totalSize()
24494080
> db.long_field_collection.totalSize()
36741120
```


## short field name 是否影响检索(find_one)

### 不影响

```
long field find_one test: [17.11999201774597, 12.968364953994751, 11.35543704032898, 16.556156873703003, 13.168859958648682]
short field find_one test: [11.312017917633057, 12.013270854949951, 15.31855320930481, 11.15831995010376, 11.852614879608154]
```

## 结论

不推荐使用 short field name(短字段名)

- 对存储空间有影响，在我这个几乎都是int数值的document中，长字段名多使用了 50%多的空间，但是由于这样的document占用空间很小，我觉得使用短字段名导致的可读性损失非常不划算。

- 对index大小以及检索速度完全没有任何影响。