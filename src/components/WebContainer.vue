<template>
  <div id='container'>
    <Header ref="header"></Header>
    <router-view @userdetails="userdetails"></router-view>
    <Footer></Footer>
  </div>
</template>

<script>
import Header from "./Header";
import Footer from "./Footer";
export default {
  name: "WebContainer",
  components: {
    Header,
    Footer
  },
  methods:{
    userdetails:function(username,profileURL,loginstatus){
      this.$refs.header.username=username;
      this.$refs.header.profileURL=profileURL;
      this.$refs.header.loginstatus=loginstatus;
      this.$router.push('/home');
    }
  }
};
</script>

<style>
#container{
  
}
</style>