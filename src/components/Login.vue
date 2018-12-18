
<template>
  <div class="sign">
    <div class="g-signin2" id="google-signin-button"></div>
  </div>  
</template>

<script>
export default {
  name: "Login",
   mounted() {
    gapi.signin2.render('google-signin-button', {
      onsuccess: this.onSignIn
    })
  },
  methods: {
    onSignIn: function(googleUser) {
      var profile = googleUser.getBasicProfile();      
      console.log("ID: " + profile.getId()); // Do not send to your backend! Use an ID token instead.
      console.log("Name: " + profile.getName());
      console.log("Image URL: " + profile.getImageUrl());
      console.log("Email: " + profile.getEmail()); // This is null if the 'email' scope is not present.
      this.$emit("userdetails",profile.getName(),profile.getImageUrl(),true);
      
    },
    
  }
};
</script>

<style scoped>
      .sign{
        margin-top: 4%;
    margin-left: 45%;
    
      }
</style>
