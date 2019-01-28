<template>
  <div class="flex justify-center">
    <div class="w-full max-w-sm">
        <div class="flex items-center border-b border-b-2 border-teal py-3">
            <input v-model="todoInput" @keyup.enter="addTodo"
                class="appearance-none bg-transparent border-none w-full text-grey-darker mr-3 py-1 px-2 leading-tight focus:outline-none"
                type="text" placeholder="To Do">

            <button @click="addTodo"
                class="flex-no-shrink bg-teal hover:bg-teal-dark border-teal hover:border-teal-dark text-sm border-4 text-white py-1 px-2 rounded" type="button">
                Add
            </button>

        </div>

        <div class="items-center border-b border-b-2 border-teal py-2">
            <!-- <ul class="list-reset">
                <div v-for="todo in todos" :key="todo.id" class="flex flex-col">
                    <div class="text-center">
                        <input v-model="todo.done" class="mr-2 leading-tight min-w-4 min-h-4" type="checkbox">
                    </div>
                    <div class="text-start">
                        <li class="py-2 flex ">
                            {{ todo.todo }}
                        </li>
                    </div>
                    <div class="">
                        <div class="">
                            &times;
                        </div>
                    </div>
                </div>
            </ul> -->
            <div v-if="todos.length === 0" >
                <div class="bg-blue-lightest border border-blue-light text-blue-dark px-4 py-3 rounded relative" role="alert">
                    <span class="block sm:inline">You dont have anything to do!</span>
                </div>
            </div>
            <div v-else v-for="todo in todos" :key="todo.id" class="flex p-3">
                <div class="w-1/5 flex justify-start items-center justify-start">
                    <div class="done-checkbox">
                        <input v-model="todo.done" class="mr-2 leading-tight min-w-4 min-h-4 rounded-full" type="checkbox">
                    </div>
                </div>
                <div v-bind:class="{ 'line-through' : todo.done }" class="w-3/5 justify-start items-center">
                    {{ todo.todo }}
                </div>
                <div class="w-1/5 flex justify-end items-center">
                    <button @click="deleteTodo(todo)" class="bg-transparent hover:bg-blue text-blue-dark font-semibold hover:text-white w-8 h-8 border border-blue hover:border-transparent rounded-full">
                        <i class="fas fa-trash-alt"></i>
                    </button>
                </div>
            </div>


        </div>
    </div>
  </div>
</template>

<script>
var hash = require('md5');
export default {
    

  name: 'todo',
  props: {
    //
  },
  data () {
    return {
      todoInput : '',
      currentId : 3,
      todos : []
    }
  },
  methods: {
      addTodo() {
          if(this.todoInput.length === 0) return;
          this.currentId++;
          this.todos.push({
              id: hash(Date.now()),
              todo: this.todoInput,
              done: false
          });

          this.todoInput = '';
      },
      deleteTodo(todo){
          this.todos.splice(this.todos.indexOf(todo), 1);
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

