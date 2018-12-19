<template>
  <div class="header" style="display:flex">
    <div class="title">E-Library</div>
    <div class="profileInfo">
      <div v-if="loginstatus" class="userinfo">
        <div style="margin-top:5px">
          <img :src="profileURL">
        </div>
        <div style="margin-top: 10px;">Hello, {{username}}</div>
      </div>
      <div v-if="loginstatus" style="padding-top:30px">
        <button @click="signout" class="logoff">SignOut</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "header",
  data() {
    return {
      username: "",
      profileURL: "",
      loginstatus: false
    };
  },

  methods: {
    signout: function() {
      var that = this;
      var auth2 = gapi.auth2.getAuthInstance();
      auth2.signOut().then(function() {
        
        console.log("User signed out.");        
        that.loginstatus = false;      
        that.$router.push("/login");
      });
      auth2.disconnect();
    },
  }
};
</script>

<style>
.header {
  background-color: black;
  height: 100px;
  text-align: center;
  color: white;
}
.userinfo {
  display: inline-block;
}
.title {
  font-size: 65px;
}
img {
  border-radius: 50px;

  height: 60px;
}
.logoff {
  background-color: cadetblue;
  border: 1px solid cadetblue;
  color: white;
  cursor: pointer;
}
.profileInfo{
  right: 0;
    position: absolute;
    display: flex;
}
</style>
