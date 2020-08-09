<template>
  <div id="app">
    <choose-user v-if="$root.me==null" :userlist="userlist"></choose-user>
    <user-list
      v-if="$root.me!=null"
      :chooseUser="chooseUser"
      :islogin="islogin"
      :users="users"
      :unreadlist="unreadlist"
    ></user-list>
    <chat-user v-if="ischat" :touser="touser" :closeChat="closeChat" :newsMsg="newsMsg"></chat-user>
  </div>
</template>

<script>
import chooseUser from "./components/chooseUser.vue";
import userList from "./components/userList.vue";
import chatUser from "./components/chatUser.vue";
import axios from "axios";
import io from "socket.io-client";
import socket from "./socket";

export default {
  name: "App",
  data() {
    return {
      userlist: [],
      me: null,
      islogin: false,
      users: [],
      ischat: false,
      touser: null,
      unreadlist: [],
      newsMsg: null,
    };
  },
  components: {
    chooseUser,
    userList,
    chatUser,
  },
  async beforeMount() {
    let res = await axios.get("http://localhost:3000/api/userlist");
    this.userlist = res.data;
  },
  mounted() {
    //监听登录成功，设置为true
    socket.on("login", (data) => {
      if (data.state == "ok") {
        this.islogin = true;
        socket.emit("users");
      }
    });

    //监听是否未登录时有信息待读
    socket.on("unreadMsg", (data) => {
      //console.log(data);
      data.forEach((item, index) => {
        //设置未读红点
        //将聊天内容分别添加到本地的存储
        //将from/to改造成有头像的对象
        item.from = this.usersObj[item.from];
        item.to = this.usersObj[item.to];
        this.unreadlist.push(item.from);
        let strKey3 =
          "chat-user-" + this.$root.me.username + "-" + item.from.username;
        //先解析本地存储，再添加
        localStorage[strKey3] = localStorage[strKey3]
          ? localStorage[strKey3]
          : "[]";
        let newArr = JSON.parse(localStorage[strKey3]);
        newArr.push(item);
        localStorage[strKey3] = JSON.stringify(newArr);
      });
      console.log(this);
    });

    //发送者发送消息时，接收者即用户在线，聊天框实时显示消息
    socket.on("accept", (msg) => {
      if (
        (this.ischat == true && msg.from.username == this.touser.username) ||
        (msg.to.username == this.touser.username && msg.to.isgroup == "true")
      ) {
        this.newsMsg = msg;
      } else {
        let strKey4 =
          "chat-user-" + this.$root.me.username + "-" + msg.from.username;
        //先解析本地存储，再添加
        localStorage[strKey4] = localStorage[strKey4]
          ? localStorage[strKey4]
          : "[]";
        let newArr = JSON.parse(localStorage[strKey4]);
        newArr.push(msg);
        localStorage[strKey4] = JSON.stringify(newArr);
        this.unreadlist.push(msg.from.username);
      }
    });

    //监听登出事件
    socket.on("logout", (data) => {
      console.log(data);
      this.islogin = false;
      //断开连接
      socket.disconnect();
    });

    //监听断开事件
    socket.on("disconnect", (data) => {
      console.log("断开连接");
    });

    //监听用户列表事件
    socket.on("users", (data) => {
      this.users = data;
    });
  },
  methods: {
    chooseUser: function (user) {
      this.touser = user;
      this.ischat = true;
      let index = this.userlist.indexOf(user.username);
      this.unreadlist.splice(index, 1);
    },
    closeChat: function () {
      this.ischat = false;
    },
  },
  computed: {
    usersObj: function (users) {
      let obj = {};
      this.users.forEach((item, index) => {
        obj[item.username] = item;
      });
      console.log(obj);
      return obj;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
</style>
