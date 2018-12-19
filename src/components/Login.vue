
<template>
  <div class="sign">
    <g-signin-button
      :params="googleSignInParams"
      @success="onSignInSuccess"
      @error="onSignInError"
    >Sign in with Google</g-signin-button>
  </div>
</template>

<script>
import Vue from "vue";
import GSignInButton from "vue-google-signin-button";

Vue.use(GSignInButton);

export default {
  name: "Login",
  data() {
    return {
      IsSignedIn: false,
      googleSignInParams: {
        client_id:
          "343052652845-6guo4uqflprlo0ta7f10jf449ueehfph.apps.googleusercontent.com"
      }
    };
  },

  methods: {
    onSignInSuccess(googleUser) {
      var profile = googleUser.getBasicProfile();
      console.log("ID: " + profile.getId());
      console.log("Name: " + profile.getName());
      console.log("Image URL: " + profile.getImageUrl());
      console.log("Email: " + profile.getEmail());
      this.$emit("userdetails", profile.getName(), profile.getImageUrl(), true);
    },
    onSignInError(error) {
      console.log("Error: ", error);
    }
  }
};
</script>

<style scoped>
.sign {
  text-align: center;
  margin-top: 4%;
}
.g-signin-button {
  /* This is where you control how the button looks. Be creative! */
  display: inline-block;
  padding: 4px 8px;
  border-radius: 3px;
  background-color: cadetblue;
  color: #fff;
  width: 400px;
  height: 65px;
  font-size: 40px;
  background-color: cadetblue;
  cursor: pointer;
}
</style>
