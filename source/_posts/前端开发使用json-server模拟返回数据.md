---
title: 前端开发使用json-server模拟返回数据
copyright: true
date: 2019-03-18 20:06:09
categories: Angular
tags:
	- 模拟数据
	- JSON
	- json-server
---
## 前端开发使用json-server模拟返回数据

### 前言

在刚开始的Angular开发中，并没有完全做到前后端分离式开发，这样一来导致前后端的开发进度互相牵绊，影响项目顺利进行，使用Postman工具的mock功能，感觉配置起来相对有点麻烦，后来发现了json-server，配置起来简单一些，功能上也基本满足需求，就简单的了解了一下，并将其应用到项目中。

### 安装

```shell
npm i -g json-server
```

### 创建json数据库

```json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment1", "author": { "name": "typicode1", "age": 10 },"postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

### 启动json-server服务

1. 启动本地服务

   ```shell
   $ json-server --w db.json

     \{^_^}/ hi!

     Loading db.json
     Done

     Resources
     http://localhost:3000/todos

     Home
     http://localhost:3000

     Type s + enter at any time to create a snapshot of the database
     Watching...

   ```

2. 启动远程服务

   ```shell
   $ json-server http://example.com/file.json
   $ json-server http://jsonplaceholder.typicode.com/db
   ```

3. 在package.json 中添加指令

   ```json
     "scripts": {
       "json-server": "json-server --watch db.json",
         ...
     }
   ```

4. 设置服务端口

   ```shell
   json-server --watch db.json --port 3004
   ```



### 配置environment.ts

```
src/environments/environment.ts      # 开发环境
src/environments/environment.prod.ts.  # 上线环境
```

apiUrl设置为json-server的服务地址

```typescript
export const environment = {
  production: false,
  apiUrl: 'http://localhost:3000'
};
```

也可直接使用json-server服务的URL

### 获取数据

```typescript
const API_URL = environment.apiUrl;
...
http.get(API_URL + "posts")
...

```

### json-server提供的请求方式

GET、POST、PUT、DELETE、PATCH、OPTIONS

注：POST\PUT\PATCH请求需要设置请求头Content-type: application/json

1. 基础请求方式

   ```
   GET    /posts
   GET    /posts/:id
   POST   /posts
   PUT    /posts/:id
   PATCH  /posts/:id
   DELETE /posts/:id

   ```

2. 获取json数据库中所有数据

   ```
   GET /db

   ```

3. 带参数查找请求方式

   ```
   GET /posts?title=json-server&author=typicode
   GET /posts?id=1&postid=2
   GET /comments?author.name=typicode1  * 使用.访问下级属性值
   ```

4. 页面分页链接请求（first、last、prev、next）

   ```
   GET /posts?_page=7
   GET /posts?_page=7&_limit=20
   ```

5. 升降序排列请求

   ```
   GET /users?_sort=id&_order=asc
   GET /users?_sort=name&_order=asc
   多条件升降序： GET /posts?_sort=user,views&_order=desc,asc
   ```

6. 范围查询数据

   ```
   GET /users?_start=2&_end=20 （类似于substring）
   GET /users?_start=2&_limit=3    （类似于substr）
   ```

7. 根据文字模糊匹配查询数据

   ```
   GET /users?q=情感
   ```

另外json-server支持自定义路由等其他功能，详情请参照[官方文档](https://www.npmjs.com/package/json-server)

