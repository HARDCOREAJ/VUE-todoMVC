<template>
  <div id="app">
  <todo></todo>
  </div>
</template>

<script>
import todo from'./components/todoProject'
export default {
  name: 'App',
  components:{todo}
}
</script>

<style>
#app {
        width: 800px;
        height: 900px;
        margin: 0 auto;
        text-align: center;
        background-color: rgb(245, 245, 245);
        padding: 24px 0;
}
</style>
