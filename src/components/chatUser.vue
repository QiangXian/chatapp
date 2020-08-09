<template>
  <div class="chatuser">
    <div class="header">
      <span class="back" @click="closeChat">&lt;</span>
      <div class="senduser">{{touser.username}}</div>
    </div>
    <div class="chatlist" ref="chatbox">
      <div
        class="chatItem"
        v-for="(item,index) in chatlist"
        :key="index"
        :class="{self:$root.me.username == item.from.username}"
      >
        <div class="headers">
          <img :src="item.from.headerimg" alt />
        </div>
        <div class="chatContent">{{item.content}}</div>
      </div>
    </div>
    <div class="inputcom">
      <input type="text" v-model="inputData" @keydown.enter="sendEvent" />
      <button @click="sendEvent">发送</button>
    </div>
  </div>
</template>

<script>
import socket from "../socket";
export default {
  props: ["touser", "closeChat", "newsMsg"],
  data() {
    return {
      chatlist: [],
      inputData: "",
    };
  },
  methods: {
    sendEvent: function () {
      let msg = {
        from: this.$root.me,
        to: this.touser,
        content: this.inputData,
        time: new Date().getTime(),
      };
      //发送到服务端
      socket.emit("sendMsg", msg);

      this.chatlist.push(msg);
      //保存聊天记录到本地
      this.saveStorage();
      this.inputData = "";
    },
    toBottom: function () {
      let chatbox = this.$refs.chatbox;
      chatbox.scrollTop = chatbox.scrollHeight - chatbox.clientHeight;
    },
    saveStorage: function () {
      let strKey =
        "chat-user-" + this.$root.me.username + "-" + this.touser.username;
      localStorage[strKey] = JSON.stringify(this.chatlist);
    },
    getStorage: function () {
      let strKey1 =
        "chat-user-" + this.$root.me.username + "-" + this.touser.username;
      localStorage[strKey1] = localStorage[strKey1]
        ? localStorage[strKey1]
        : "[]";
      let result = JSON.parse(localStorage[strKey1]);
      this.chatlist = result;
      console.log(this.chatlist);
    },
  },
  beforeMount() {
    //挂载前解析本地是否有聊天记录的存储，若无则抛出异常
    this.getStorage();
    socket.emit("readMsg", {
      self: this.$root.me.username,
      username: this.touser.username,
    });
  },
  mounted() {
    //数据挂载完成后，视图自动滚动至最新的聊天信息
    this.toBottom();
  },
  watch: {
    newsMsg: function (val) {
      this.chatlist.push(val);
      this.saveStorage();
    },
  },
  updated() {
    this.toBottom();
  },
};
</script>

<style scoped>
.chatItem {
  display: flex;
  margin: 0 10px;
}
.chatItem.self {
  flex-direction: row-reverse;
  justify-content: flex-start;
}
.chatItem .headers img {
  width: 60px;
  height: 60px;
  border-radius: 50%;
}
.chatItem .chatContent {
  background: #bbb;
  border-radius: 5px;
  padding: 5px;
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 10px 0 10px;
  line-height: 32px;
}
.chatuser {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
  background-color: #fff6f6;
}
.chatuser .header {
  font-size: 18px;
  font-weight: 900;
  background: skyblue;
  height: 50px;
  width: 100%;
  text-align: center;
  line-height: 40px;
  position: relative;
}
.header .senduser {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
}
.chatuser .back {
  font-size: 30px;
  display: block;
  width: 60px;
  height: 40px;
  line-height: 40px;
  text-align: center;
  position: absolute;
  left: 0;
  top: 3px;
}
.chatlist {
  flex: 1;
  overflow: scroll;
}
.inputcom {
  height: 50px;
  display: flex;
  background: #ffdfdf;
  justify-content: space-between;
}
.inputcom input {
  width: 270px;
  height: 40px;
  border-radius: 5px;
  outline: none;
  border: 1px solid #fff6f6;
  margin: auto 5px;
}
.inputcom button {
  width: 80px;
  height: 40px;
  border-radius: 5px;
  outline: none;
  border: 1px solid #fff6f6;
  margin: auto 5px;
}
</style>