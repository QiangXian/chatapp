# **聊天室ChatRoom前瞻**
# 后端地址：https://github.com/QiangXian/chatapp-back-end

<img src="C:\Users\lenovo\Desktop\wolf.JPG" alt="wolf" style="height:100px;display:inline" />

## 需求

​	聊天APP

## 功能

​	查看聊天列表、实现单聊功能、实现群（选做：个人中心、好友添加、群创建、登录、注册）

# 前端：wxchat

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

## 功能实现

#### 1.创建一张用户表

字段：username、~~passwod、mail~~,headerimg、socketid、isonline

#### 1.5在页面选择一个聊天角色



#### 2.设置前端内容

（1）socket-client客户端与服务器的socket相连接，（选择当前的用户），并建立连接

（2）客户端向服务器发送一个获取用户列表的事件

```javascript--
socket.emit('getUserlist')
```

监听服务器返回的数据

```javascript
socket.on('getUserlist')
```

#### 3.设置后端的内容：chatapp（Express框架)

（1）服务器监听客户端的连接，获取当前连接用户的用户名和socket.id，更新数据库内容

（2）服务器监听获取用户列表事件

```javascript
socket.on('getUserlist',function(){
    数据库的查询user表语句;
    //将结果返回到给前段
    socket.emit('getUserlist'，data);
})
```

