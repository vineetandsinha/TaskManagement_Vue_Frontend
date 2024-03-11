<template>
  <HelloWorld msg="Welcome to Vue-Task Management App"/>
  <Task msg="Welcome to Task Management App"/>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import Task from './components/Task.vue'
// import Echo from 'laravel-echo';

export default {
  name: 'App',
  components: {
    HelloWorld,
    Task
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
