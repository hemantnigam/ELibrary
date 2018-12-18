<template>
  <div id="app">
    <WebContainer></WebContainer>
  </div>
</template>

<script>
import WebContainer from './components/WebContainer';
export default {
  name: 'App',
  components:{
    WebContainer
  },
  created(){
    this.$router.replace('/');
  }
}
</script>
<style>
</style>
