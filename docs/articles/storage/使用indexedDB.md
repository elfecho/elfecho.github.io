# 浏览器数据库 IndexedDB

> IndexedDB 就是浏览器提供的本地数据库，它可以被网页脚本创建和操作。

## 背景

随着浏览器的功能不断增强，越来越多的网站开始考虑，将大量数据储存在客户端，这样可以减少从服务器获取数据，直接从本地获取数据。

现有的浏览器数据储存方案，都不适合储存大量数据：Cookie 的大小不超过4KB，且每次请求都会发送回服务器；LocalStorage 在 2.5MB 到 10MB 之间（各家浏览器不同），而且不提供搜索功能，不能建立自定义的索引。所以，需要一种新的解决方案，这就是 IndexedDB 诞生的背景。

我们可以通过开发者工具查看 IndexedDB 中的存储数据：

## 特点

通俗地说，IndexedDB 就是浏览器提供的本地数据库，它可以被网页脚本创建和操作。IndexedDB 允许储存大量数据，提供查找接口，还能建立索引。这些都是 LocalStorage 所不具备的。就数据库类型而言，IndexedDB 不属于关系型数据库（不支持 SQL 查询语句），更接近 NoSQL 数据库。

IndexedDB 具有以下特点：

**（1）键值对储存。** IndexedDB 内部采用对象仓库（object store）存放数据。所有类型的数据都可以直接存入，包括 JavaScript 对象。对象仓库中，数据以"键值对"的形式保存，每一个数据记录都有对应的主键，主键是独一无二的，不能有重复，否则会抛出一个错误。

**（2）异步。** IndexedDB 操作时不会锁死浏览器，用户依然可以进行其他操作，这与 LocalStorage 形成对比，后者的操作是同步的。异步设计是为了防止大量数据的读写，拖慢网页的表现。

**（3）支持事务。** IndexedDB 支持事务（transaction），这意味着一系列操作步骤之中，只要有一步失败，整个事务就都取消，数据库回滚到事务发生之前的状态，不存在只改写一部分数据的情况。

**（4）同源限制** IndexedDB 受到同源限制，每一个数据库对应创建它的域名。网页只能访问自身域名下的数据库，而不能访问跨域的数据库。

**（5）储存空间大** IndexedDB 的储存空间比 LocalStorage 大得多，一般来说不少于 250MB，甚至没有上限。

**（6）支持二进制储存。** IndexedDB 不仅可以储存字符串，还可以储存二进制数据（ArrayBuffer 对象和 Blob 对象）。

## 作用

IndexedDB 数据库的使用目前可以直接在 HTTP 协议下使用，这个和 `cacheStorage` 缓存存储必须使用 HTTPS 协议不一样。所以就应用场景来讲，IndexedDB 数据库还是挺广的，考虑到 IE10 也支持，所以基本可以确定在实际项目中应用是绝对不成问题的。

例如，页面中一些不常变动的结构化数据，我们就可以使用 IndexedDB 数据库存储在本地，有助于增强页面的交互性能。


## 使用indexedDB浏览器数据库

### 打开aribnb数据库

```javascript
// 打开数据库
class DB {
  private dbName: string // 数据库名称
  private db: any // 数据库对象
  constructor(dbName: string) {
    this.dbName = dbName
  }
  openStore(storeName: string, keyPath: string, indexs?: Array<string>) {
    const request = window.indexedDB.open(this.dbName, 2)
    return new Promise((resolve: any, reject: any) => {
      request.onsuccess = (event: any) => {
        console.log('数据库打开成功')
        console.log('onsuccess', event)
        this.db = event.target.result
        resolve()
      }
      request.onerror = (event: any) => {
        console.log('数据库打开失败')
        console.log('onerror', event)
        reject(event)
      }
      request.onupgradeneeded = (event) => {
        console.log('数据库更新成功')
        const { result }: any = event.target
        const store = result.createObjectStore(storeName, { autoIncrement: true, keyPath })
        if (indexs && indexs.length > 0) {
          indexs?.map((v: string, index) => {
            store.createIndex(v, v, { unique: false }) // 三个对象分别对应 索引名称、 索引属性、 配置对象
          })
        }
        store.transaction.oncomplete = (event: any) => {
          console.log('创建对象仓库成功')
        }
        console.log('onupgradeneeded', event)
      }
    })
  }
}

```

![upgit_20220627_1656325926.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/06/upgit_20220627_1656325926.png)

**创建数据库表**

```javascript
import IndexedDB from '@/utils/indexedDB'

const airbnbDB = new IndexedDB('aribnb')

airbnbDB.openStore('elephant', 'id', ['nose', 'ear'])
```

![upgit_20220627_1656327886.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/06/upgit_20220627_1656327886.png)

### 对数据库进行增删改查

- 新增 add()
- 删除 delete()
- 修改 put()
- 查所有 getAll()
- 查单个 get()

```javascript
// 新增/修改数据库数据
class DB {
  updateItem(storeName: string, data: any) {
    const store = this.db.transaction([storeName], 'readwrite').objectStore(storeName) // 创建事务
    const request = store.put({ // 这里为了适配新增与删除，故使用put
      ...data,
      updateTime: new Date().getTime()
    })

    request.onsuccess = (event: any) => {
      console.log('数据写入成功')
      console.log('onsuccess', event)
    }

    request.onerror = (event: any) => {
      console.log('数据写入失败')
      console.log('onerror', event)
    }
  }

  

  // 删除数据库数据

  deleteItem(storeName: string, key: number | string) {
    const store = this.db.transaction([storeName], 'readwrite').objectStore(storeName)
    const request = store.delete(key)
    request.onsuccess = (event: any) => {
      console.log('数据删除成功')
      console.log('onsuccess', event)
    }

    request.onerror = (event: any) => {
      console.log('数据删除失败')
      console.log('onerror', event)
    }

  }

  

  // 查询所有数据

  getList(storeName: string) {
    const store = this.db.transaction(storeName).objectStore(storeName)
    const request = store.getAll()
    return new Promise((resolve, reject) => {
      request.onsuccess = (event: any) => {
        console.log('查询所有数据成功')
        console.log('onsuccess', event.target.result)
        resolve(event.target.result)
      }

      request.onerror = (event: any) => {
        console.log('查询所有数据失败')
        console.log('onerror', event)
        reject(event)
      }

    })

  }

  

  // 查询某一条数据

  getItem(storeName: string, key: number | string) {
    const store = this.db.transaction(storeName).objectStore(storeName)
    const request = store.get(key)
    request.onsuccess = (event: any) => {
      console.log('查询某一条数据成功')
      console.log('onsuccess', event.target.result)
    }

    request.onerror = (event: any) => {
      console.log('查询某一条数据失败')
      console.log('onerror', event)
    }

  }
}
```