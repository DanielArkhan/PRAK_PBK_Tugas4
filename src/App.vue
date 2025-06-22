<script setup>
import TodoList from './components/TodoList.vue'
</script>

<template>
  <div id="app">
    <TodoList />
  </div>
</template>

<style>
body {
  background: linear-gradient(135deg, #f8fafc 0%, #dbeafe 100%);
  min-height: 100vh;
  margin: 0;
  font-family: 'Segoe UI', Arial, sans-serif;
}
#app {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>