<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" style="display:block;margin:auto;margin-bottom:40px;">
    <h1 style="text-align:center;">Vue Scout</h1>
    <div class="vue-scout-container">
          <Scout msg="Welcome to Your Vue.js App"/>
    </div>
  </div>
</template>

<script>
import Scout from './components/Scout.vue'

export default {
  name: 'app',
  components: {
    Scout
  },
  data(){
    return{
      selected:[],
      options:[]
    }
    
  },
  mounted(){
    var xhr = new XMLHttpRequest();
xhr.open("GET", "https://reqres.in/api/products/3", true);
xhr.onload = function(){
    var res = JSON.parse(xhr.responseText);
    console.log(res.data)
};
xhr.send();
  }
}
</script>

<style>
.vue-scout-container{
  width:500px;
  margin:auto;
}
</style>
