<template lang="pug">
section
  .container
    input(type="text" placeholder="Type your task here" v-model="newTodoTitle" @keyup.enter="newTodoItem").task-input
    .todo-list
      <transition-group name="fade">
      .todo-item(v-for="(todo, index) in todosFilter" :key="todo.id")
        .todo-item-header
          input(type="checkbox" v-model="todo.completed")
          .todo-item-title(v-if="!todo.editing" :class="{ completed: todo.completed }" @click="editTodoItem(todo)") {{ todo.title }}
          input(type="text" v-else="todo.editing" v-model="todo.title" @blur="finishEditTodoItem(todo)" @keyup.enter="finishEditTodoItem(todo)" @keyup.esc="cancelEditTodoItem(todo)").todo-item-title.todo-item-title--edit
        span.delete-button(@click="deleteTodoItem(index)")
      </transition-group>
    
    .todo-info
      .todo-menu-check
        input(type="checkbox" :checked="!anyTodoInWork" @change="doneAllTodo")
        |  Check All
      p Items left: {{ allTodoInWork }}

    .todo-menu
      .main-navigaton
        button(:class="{ active: filter == 'all' }" @click="filter = 'all' ").button-main All
        button(:class="{ active: filter == 'active' }" @click="filter = 'active' ").button-main Active
        button(:class="{ active: filter == 'completed' }" @click="filter = 'completed' ").button-main Completed
      <transition name="fade">
      button(v-if="showDeleteCompletedTodo" @click="deleteCompletedTodo").button-main.button--second Clear Completed
      </transition>
</template>

<script>
export default {
  name: 'todo-list',
  data() {
    return{
      newTodoTitle: '',
      titleBeforeEdit: '',
      newTodoId: 3,
      filter: 'all',
      todos: [
        {
          'id': 1,
          'title': 'Learn Vue.js',
          'completed': false,
          'editing': false
        },
        {
          'id': 2,
          'title': 'Finish App',
          'completed': false,
          'editing': false
        } 
      ]
    }
  },
  methods: {
    newTodoItem(){
      if(this.newTodoTitle.trim() == ''){
        return
      }

      this.todos.push({
        id: this.newTodoId,
        title: this.newTodoTitle,
        completed: false
      })

      this.newTodoTitle = '';
      this.newTodoId += 1;
    },
    deleteTodoItem(index){
      this.todos.splice(index, 1);
    },
    editTodoItem(todo){

      //title before edit
      this.titleBeforeEdit = todo.title;
      todo.editing = true;
    },
    finishEditTodoItem(todo){
      todo.editing = false;
      
      if(todo.title.trim() == ''){
        todo.title = this.titleBeforeEdit;
      }
    },
    cancelEditTodoItem(todo){
      todo.title = this.titleBeforeEdit;
      todo.editing = false;
    },
    doneAllTodo(){
      this.todos.forEach((todo)=>todo.completed = event.target.checked);
    },
    deleteCompletedTodo(){
      this.todos = this.todos.filter(todo => !todo.completed);
    }
  },
  computed: {
    allTodoInWork(){
      return this.todos.filter(todo => !todo.completed).length;
    },
    anyTodoInWork(){
      return this.allTodoInWork != 0;
    },
    //Todo filter for todos
    todosFilter(){
      if(this.filter == 'all'){
        return this.todos;
      }else if(this.filter == 'active'){
        return this.todos.filter(todo => !todo.completed)
      }else if(this.filter == 'completed'){
        return this.todos.filter(todo => todo.completed)
      }

      return this.todos;
    },
    showDeleteCompletedTodo(){
      return this.todos.filter(todo => todo.completed).length > 0;
    }

  }
}
</script>

<style lang="stylus">

.task-input
  width 100%
  margin-bottom 20px
  padding 8px 16px
  font-size 18px
  border-radius 8px
  border 1px solid #ccc

  &:focus
    outline 0

.todo-list
  padding-bottom 20px
  margin-bottom 20px
  border-bottom 1px solid #ccc

.todo-item
  display flex
  justify-content space-between
  align-items center
  position relative
  margin-bottom 6px

  &:last-child
    margin-bottom 0

.todo-item-header
  display flex
  align-items center

.todo-item-title
  padding 6px
  font-size 20px
  
  &.todo-item-title--edit
    border 1px solid #ccc
  
  &.completed
    text-decoration line-through
    color #ccc

.todo-info
  display flex
  justify-content space-between
  align-items center
  padding-bottom 20px
  margin-bottom 20px
  border-bottom 1px solid #ccc

.todo-menu,
.todo-menu-check
  display flex
  justify-content space-between
  align-items center

</style>


