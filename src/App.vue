<template>
  <div id="app" class="font-sans font-medium">

    <div class="flex justify-center">
      <div class="w-32 py-16">
        <img  alt="Vue logo" src="./assets/logo.png" class="">
      </div>
    </div>
    
    <Todo/>
  </div>
</template>

<script>
import Todo from './components/Todo.vue';

export default {
  name: 'app',
  components: {
    Todo
  }
}
</script>