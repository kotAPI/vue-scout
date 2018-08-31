<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" style="display:block;margin:auto;margin-bottom:40px;">
    <h1 style="text-align:center;">Vue Scout</h1>
    <div class="vue-scout-container">
          <Scout msg="Welcome to Your Vue.js App" :options="options" v-model="selected" label="first_name" :multiple="true" :taggable="true" @tag="newTagHandler" />
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
  methods:{
    newTagHandler(a){
      console.log(a)
    }
  },
  mounted(){
    var xhr = new XMLHttpRequest();
    xhr.open("GET", "https://reqres.in/api/users?page=2", true);
    xhr.onload = ()=>{
        var res = JSON.parse(xhr.responseText);
        console.log(res.data)
        this.options = res.data
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
