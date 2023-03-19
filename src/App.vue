<template>
  <div id="app">
    <div v-color="color">el-tree
      <input type="text"  v-focus>
    </div>
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
    return{
      color:'red'
    }
  },
  directives:{
    color:{
      bind(el,binding){
        el.style.color=binding.value
         
      }
    },
    focus:{
      inserted(el){
        el.focus()
      }
    }
  }
}
</script>

<style>
#app {

}
</style>
