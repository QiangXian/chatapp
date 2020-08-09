<template>
  <div class="userlist">
    <div class="nav">
      <div class="headerimg" :class="{online:!islogin}">
        <img v-if="$root.me != null" :src="$root.me.headerimg" alt />
      </div>
      <div class="title">消息</div>
      <div class="headerimg"></div>
    </div>
    <div class="users">
      <div @click="chooseUser(item)" class="useritem" v-for="(item,index) in friends" :key="index">
        <div
          class="left"
          :class="{online:item.isonline=='true',unread:unreadlist.indexOf(item.username)!= -1}"
        >
          <img :src="item.headerimg" alt />
        </div>

        <div class="right">
          <span class="username">{{item.username}}</span>
          <span class="msg"></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["islogin", "users", "chooseUser", "unreadlist"],
  computed: {
    friends: function () {
      return this.users.filter((item, index) => {
        return item.username != this.$root.me.username;
      });
    },
  },
};
</script>

<style scoped>
.useritem {
  display: flex;
  height: 80px;
  border-bottom: 1px solid #ccc;
  align-items: center;
  padding: 0 10px;
}
.unread {
  position: relative;
}
.unread::before {
  position: absolute;
  content: "";
  display: block;
  width: 15px;
  height: 15px;
  border-radius: 50%;
  background: red;
  bottom: 3px;
  right: 0;
}
.useritem .left {
  filter: grayscale(1);
}
.headerimg {
  height: 50px;
  width: 50px;
  margin: 0 10px;
}
.online {
  filter: grayscale(0) !important;
}
.nav {
  height: 80px;
  width: 100vw;
  background-color: skyblue;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.nav .title {
  font-weight: 900;
  font-size: 18px;
}
.headerimg img {
  height: 50px;
  width: 50px;
  border-radius: 50%;
}
.useritem .left img {
  height: 60px;
  width: 60px;
  border-radius: 50%;
}
.useritem .right {
  padding: 0 10px;
}
</style>>