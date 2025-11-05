---
tags:
  - 前端
---
## 概念

运行在浏览器上的无关系数据库

## 打开数据库

```JavaScript
import {openDB} from 'idb';

async function useDB () {
  const db = await openDB('example-database', version);
  
  return db
}
```

如果数据库不存在会自动创建

## 创建 object stores

```JavaScript
import {openDB} from 'idb';

async function useDB () {
  const db = await openDB('example-database', version, {
      upgrade(db) {
          const exampleStore = db.createObjectStore("example-store", {
              keyPath: "id",
              autoIncrement: true,
          });
      }
  });
  
  return db
}
```

object store: 类似于关系型数据库的表, 存储某种数据对象, 且不限制数据对象的字段类型

keyPath: 指定主键名称

autoIncreament: 为主键自动创建为唯一值

在 upgrade 中执行是因为需要管理版本号, 没版本号则创建 object store, 版本号有变化则更新 object store 结构, 版本号没有变化则不再重复执行创建 object store

## 创建索引

```JavaScript
import {openDB} from 'idb';

async function useDB () {
  const db = await openDB('example-database', version, {
      upgrade(db) {
          const exampleStore = db.createObjectStore("example-store", {
              keyPath: "id",
              autoIncrement: true,
          });
          
          exampleStore.createIndex("byName", "name", { unique: false });
      }
  });
  
  return db
}

async function search () {
    const db = await useDB()
    const value = await db.getFromeIndex('example-store', 'byName', 'value')
    
    return value
}

search()
```

Index: 针对 object store 的某个字段创建索引, 在索引基础上查询该字段某个值对应的数据会很快, 而不需要遍历整个 object store

## 参考文献

- [Work with IndexedDB](https://web.dev/articles/indexeddb#create)