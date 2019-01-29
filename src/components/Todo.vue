<template>
  <div class="flex justify-center">
    <div class="w-full max-w-sm">
        <div class="flex items-center border-b border-b-2 border-teal py-3">
            <input v-model="todoInput" @keyup.enter="addTodo" @keyup.esc="cancelAddTodo()"
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
            <div v-if="filtringTodos().length === 0 && todos.length !== 0" class="text-center p-3 text-orange">
                    <p text-center>Nothing for {{ filterTodos === 'uncompleted' ? 'Uncompleted' : 'Completed' }}</p>
            </div>
            <div v-else v-for="todo in filtringTodos()" :key="todo.id" class="flex p-3">
                <div class="w-1/5 flex justify-start items-center justify-start">
                    <div class="done-checkbox">
                        <input @click="complited(todo)" :checked="todo.done" class="mr-2 leading-tight min-w-4 min-h-4 rounded-full" type="checkbox">
                    </div>
                </div>
                <div v-bind:class="{ 'line-through' : todo.done }" @dblclick="editTodo(todo)" class="w-3/5 justify-start items-center">
                    <div v-if="!todo.onEdit" class="p-1">
                        {{ todo.todo }}
                    </div>
                    <div v-else>
                        <input v-model="editInput.value" @keyup.enter="saveTodoChange()" @keyup.esc="cancelEdit(todo)" class="shadow appearance-none border rounded w-full py-2 px-3 text-grey-darker leading-tight focus:outline-none focus:shadow-outline" type="text">
                    </div>
                </div>
                <div class="w-1/5 flex justify-end items-center">
                    <button @click="deleteTodo(todo)" class="bg-transparent hover:bg-blue text-blue-dark font-semibold hover:text-white w-8 h-8 border border-blue hover:border-transparent rounded-full">
                        <i class="fas fa-trash-alt"></i>
                    </button>
                </div>
            </div>

        </div>
        
        <div class="px-2 pt-3 border-b border-b-2 border-teal py-2">
            <div class="flex -mx-2">
                <div class="w-1/3 px-3">
                    <div class="h-12">
                        <input @click="checkAll()" v-model="isAllChecked" :checked="calculisAllChecked()" class="mr-2 leading-tight min-w-4 min-h-4 rounded-full" type="checkbox">
                        Check all
                    </div>
                </div>
                <div class="w-1/3 px-3">
                    <div class="h-12"></div>
                </div>
                <div class="w-1/3 px-3">
                    <div class="h-12 text-right">
                        {{ tasksRemaiming() }}
                    </div>
                </div>
            </div>
        </div>

        <div class="px-2 pt-2">
            <div class="flex -mx-2">
                <div class="w-1/3 px-3">
                    <div class="h-12">
                        <a href="#" @click="filterTodos = 'all'" :class="filterTodos === 'all' ? 'text-blue-dark' : 'text-grey-darkest'" class="no-underline hover:underline hover:text-grey">
                            All
                        </a>
                    </div>
                </div>
                <div class="w-1/3 px-3">
                    <div class="h-12 text-right">
                        <a href="#" @click="filterTodos = 'uncompleted'" :class="filterTodos === 'uncompleted' ? 'text-blue-dark' : 'text-grey-darkest'" class="no-underline hover:underline hover:text-grey">
                            Uncompleted
                        </a>
                    </div>
                </div>
                <div class="w-1/3 px-3">
                    <div class="h-12 text-right">
                        <a href="#" @click="filterTodos = 'completed'" :class="filterTodos === 'completed' ? 'text-blue-dark' : 'text-grey-darkest'" class="no-underline hover:underline hover:text-grey">
                            Completed
                        </a>
                    </div>
                </div>
            </div>
        </div>

    </div>
  </div>
</template>

<script>
/* eslint-disable */
let hash = require('md5');

export default {

    name: 'todo',
    props: {
    //
    },
    mounted(){
        // Check if local storage is empty, if empty fill it with empty array
        if(!localStorage.getItem('todos')){
            localStorage.setItem('todos', JSON.stringify([]));
        }
        // Get todos from local storage.
        this.todos = JSON.parse(localStorage.getItem('todos'));

        this.calculisAllChecked();

    },
    data () {
        return {
            todoInput : '',
            todos : [],
            editInput : {
                value: '',
                index: ''
            },
            isAllChecked: '',
            filterTodos: 'all'
        }
    },
    methods: {
        addTodo() {
            if(this.todoInput.length === 0) return;
    
            this.todos.push({
                id: hash(Date.now()),
                todo: this.todoInput,
                done: false,
                onEdit: false
            });
            localStorage.setItem('todos', JSON.stringify(this.todos));
            this.todoInput = '';
            this.filterTodos = 'all'
        },
        deleteTodo(todo){
            this.todos.splice(this.todos.indexOf(todo), 1);
            localStorage.setItem('todos', JSON.stringify(this.todos));
        },
        complited(todo) {
            this.todos[this.todos.indexOf(todo)].done = !this.todos[this.todos.indexOf(todo)].done;
            localStorage.setItem('todos', JSON.stringify(this.todos));
        },
        editTodo(todo) {
            // check if others todos item is on edit
            this.todos.forEach(todo => {
                if (todo.onEdit) todo.onEdit = false;
                this.editInput.value = '';
            });
            this.todos[this.todos.indexOf(todo)].onEdit = true;
            this.editInput.value = this.todos[this.todos.indexOf(todo)].todo;
            this.editInput.index = this.todos.indexOf(todo);
        },
        saveTodoChange(){
            let index = this.editInput.index;
            this.todos[index].todo = this.editInput.value;
            this.todos[index].onEdit = false;

            localStorage.setItem('todos', JSON.stringify(this.todos));
        },
        cancelEdit(){
            this.todos[this.editInput.index].onEdit = false;
            this.editInput.index = null;
            this.editInput.value = '';
        },
        cancelAddTodo(){
            this.todoInput = '';
        },
        checkAll(){
            if(this.isAllChecked) {
                this.isAllChecked = false;
                this.todos.forEach(todo => {
                    if (todo.done) todo.done = false;
                });
            } else {
                this.todos.forEach(todo => {
                    if (!todo.done) todo.done = true;
                });
            }
            this.isAllChecked = true;
            localStorage.setItem('todos', JSON.stringify(this.todos));
        },
        calculisAllChecked(){
            // check if all cheked or unchecked
            console.log('here');
            
            return this.isAllChecked = this.todos.filter(todo => !todo.done).length === 0 ? true : false;
        },
        filtringTodos(){
            switch (this.filterTodos) {
                case 'all':
                    return this.todos;
                    break;
            
                case 'uncompleted':
                    return this.todos.filter(todo => !todo.done);
                    break;
            
                case 'completed':
                    return this.todos.filter(todo => todo.done);
                    break;
            
                default:
                    break;
            }

        },
        tasksRemaiming(){
            let remaining = this.todos.filter(todo => !todo.done).length;

            switch (remaining) {
                case 0:
                    return 'All done';
                    break;

                case 1:
                    return 'One task left';
                    break;

                default:
                    return `${remaining} tasks left`;
                    break;
            }

        }

    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

