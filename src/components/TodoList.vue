<template lang="pug">
section
  .container
    input(type="text" placeholder="Type your task here" v-model="newTodoTitle" @keyup.enter="newTodoItem").task-input
    .todo-list
      .todo-item(v-for="(todo, index) in todos" :key="todo.id")
        .texx
          .todo-item-title(v-if="!todo.editing" @click="editTodoItem(todo)") {{ todo.title }}
          input(type="text" v-else="todo.editing" v-model="todo.title" @blur="finishEditTodoItem(todo)" @keyup.enter="finishEditTodoItem(todo)" @keyup.esc="cancelEditTodoItem(todo)").todo-item-title.todo-item-title--edit
        span.delete-button(@click="deleteTodoItem(index)")
</template>

<script>
export default {
  name: 'todo-list',
  data() {
    return{
      newTodoTitle: '',
      titleBeforeEdit: '',
      newTodoId: 3,
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
    }
  }
}
</script>

<style lang="stylus">

.task-input
  width 100%
  padding 8px 16px
  font-size 18px
  margin-bottom 20px

  &:focus
    outline 0

.todo-item
  display flex
  justify-content space-between
  align-items center
  position relative
  margin-bottom 6px

  &:last-child
    margin-bottom 0

.todo-item-title
  padding 6px
  font-size 20px
  
  &.todo-item-title--edit
    border 1px solid #ccc

.delete-button
  position absolute
  top 8px
  right 0
  width 18px
  height @width
  cursor pointer
  border-radius: 50%;

.delete-button:before,
.delete-button:after
  content ""
  position absolute
  top 50%
  right 0
  width 18px
  height 1px
  background-color #333333

  &:hover
    background-color #e62f57

.delete-button:before
  transform rotate(45deg)

.delete-button:after
  transform rotate(-45deg)


</style>


