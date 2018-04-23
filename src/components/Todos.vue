<template>
  <div class="todos-container">
    <section class="todoapp">
      <header class="header">
        <h1>todos</h1>
        <input class="new-todo" type="text" v-model="newTodo" v-on:keyup.enter="addTodo" placeholder="What needs to be done?" />
      </header>
      <section class="main">
        <input type="checkbox" class="toggle-all" id="toggle-all">
        <label for="toggle-all">Mark all as complete</label>
        <ul class="todo-list">
          <li v-for="todo in filteredTodos" :class="{completed: todo.completed, editing: todo==editedTodo}">
            <div class="view">
              <input type="checkbox" class="toggle" v-model="todo.completed"/> 
              <label @dblclick="editTodo(todo)">{{todo.title}}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
            <input type="text" class="edit"
            v-model="todo.title"
            @blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)" />
          </li>
        </ul>
      </section>

      <footer class="footer">
        <ul class="filters">
          <li><a href="#" @click.prevent="visibility='all'" :class="{selected: visibility=='all'}">All</a></li>
          <li><a href="#" @click.prevent="visibility='active'" :class="{selected: visibility=='active'}">Active</a></li>
          <li><a href="#" @click.prevent="visibility='completed'" :class="{selected: visibility=='completed'}">Completed</a></li>
        </ul>
      </footer>
    </section>

    <footer class="info">
      <p>Double-click to edit a todo</p>
    </footer>
  </div>
</template>


<script>
  const STORAGE_KEY = 'todo-storage'

  export default {
   name: "Todos",
   data() {
     return {
       newTodo: "",
       todos: [],
       editedTodo: null,
       visibility: 'all'
     }
   },
   created() {
     this.todos = this.fetch()
   },
   methods: {
     fetch() {
       var todos = JSON.parse(localStorage.getItem(STORAGE_KEY))
       return todos
     },
     save(todos) {
       localStorage.setItem(STORAGE_KEY,JSON.stringify(todos))
     },
     addTodo() {
       this.todos.push({
         id: this.todos.length,
         title: this.newTodo,
         completed: false
       })
       this.newTodo=""
      //  localStorage.setItem(STORAGE_KEY,JSON.stringify(this.todos))
     },
     removeTodo(todo) {
       this.todos.splice(this.todos.indexOf(todo),1)
      //  localStorage.setItem(STORAGE_KEY,JSON.stringify(this.todos))
     },
     editTodo(todo) {
       this.editedTodo=todo
     },
     doneEdit(todo) {
       if(this.editedTodo) {
         this.editedTodo=null
         todo.title=todo.title.trim()
         if(!todo.title)
          this.removeTodo(todo)
        
        //  localStorage.setItem(STORAGE_KEY,JSON.stringify(this.todos))
       }
     }
   },
   watch: {
     todos: {
       handler(todos) {
         this.save(todos)
       },
       deep: true
     }
   },
   computed: {
     filteredTodos() {
      if(this.visibility==='all')
        return this.todos
      else if(this.visibility==='active') {
        return this.todos.filter(function(todo) {
          return !todo.completed
        })
      }
      else {
        return this.todos.filter(function(todo) {
          return todo.completed
        })
      }
    }
   }
  }
</script>
