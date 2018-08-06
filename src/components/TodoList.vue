<template lang="pug">
section
  .container
    .user-info
      h1 To-Do List
      span.user-score Score: {{ score }}
    .todo-task
      input.task-input(type="text" placeholder="Type your task here" v-model="newTodoTitle" @keyup.enter="newTodoItem")
      .category-list
        select.category-select(v-model="category" placeholder='Source Type')
          option(disabled value="") Category
          option(value='Other') Other
          option(value='Work') Work
          option(value='Home') Home
          option(value='Travel') Travel
      .priority-list
        .priority-item
          label.priority-label.priority-label--grey(for='grey' :class="{ active: priority == 'grey' }" @click=" priority = 'grey' " )
          input.priority-input(type="radio" id="grey" checked="checked" )
        .priority-item
          label.priority-label.priority-label--yellow(for='yellow' :class="{ active: priority == 'yellow' }" @click=" priority = 'yellow' " )
          input.priority-input(type="radio" id="yellow")
        .priority-item
          label.priority-label.priority-label--red(for='red' :class="{ active: priority == 'red' }" @click=" priority = 'red' " )
          input.priority-input(type="radio" id="red")
      button.button-main.active(@click="newTodoItem") Create


    
      

    .todo-list
      <transition-group name="listAnim">
      .todo-item(v-for="(todo, index) in todosFilter" :key="todo.id" :class="{ grey: todo.priority == 'grey', yellow: todo.priority == 'yellow', red: todo.priority == 'red' }")
        .todo-item-header
          input(type="checkbox" v-model="todo.completed" @click.once="scoreUpdate")
          .todo-item-title(v-if="!todo.editing" :class="{ completed: todo.completed }" @click="editTodoItem(todo)") {{ todo.title }}
          input.todo-item-title.todo-item-title--edit(type="text" v-else="todo.editing" v-model="todo.title" @blur="finishEditTodoItem(todo)" @keyup.enter="finishEditTodoItem(todo)" @keyup.esc="cancelEditTodoItem(todo)")
          .todo-item-category {{ todo.category }}
        .todo-item-buttons
          span.delete-button(@click="deleteTodoItem(index)")
          span.edit-button(@click="todo.editing = true") Edit
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
      priority: 'grey',
      category: '',
      score: 0,
      todos: [
        {
          'id': 1,
          'title': 'Learn Vue.js',
          'completed': false,
          'editing': false,
          'priority': 'grey',
          'category': 'Work'
        },
        {
          'id': 2,
          'title': 'Finish App',
          'completed': false,
          'editing': false,
          'priority': 'yellow',
          'category': 'Work'
        } 
      ]
    }
  },
  methods: {
    newTodoItem(){
      if(this.newTodoTitle.trim() == ''){
        return;
      }
       if(this.category.trim() == ''){
        this.category = 'Other';
      }

      this.todos.push({
        id: this.newTodoId,
        title: this.newTodoTitle,
        completed: false,
        editing: false,
        priority: this.priority,
        category: this.category
      })

      this.newTodoId += 1;
      this.score += 5;

      //Reset
      this.newTodoTitle = '';
      this.priority = 'grey';
      this.category = '';
    },
    deleteTodoItem(index){
      this.todos.splice(index, 1);
      this.score += 10;
    },

    //Edit Todo
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
    //Edit Todo end

    doneAllTodo(){
      this.todos.forEach((todo)=>todo.completed = event.target.checked);
    },
    deleteCompletedTodo(){
      this.score += this.allTodoCompleted;
      this.todos = this.todos.filter(todo => !todo.completed);
    },
    scoreUpdate(){
       this.score += 10;
    }
  },
  computed: {
    allTodoInWork(){
      return this.todos.filter(todo => !todo.completed).length;
    },
    anyTodoInWork(){
      return this.allTodoInWork != 0;
    },
    allTodoCompleted(){
      return this.todos.filter(todo => todo.completed).length;
    },
    //Todo filter for todos
    todosFilter(){
      if(this.filter == 'all'){
        return this.todos;
      }else if(this.filter == 'active'){
        return this.todos.filter(todo => !todo.completed);
      }else if(this.filter == 'completed'){
        return this.todos.filter(todo => todo.completed);
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

.user-info
  display flex
  align-items center
  justify-content space-between
  margin-bottom 20px

.todo-task
  display flex
  justify-content space-between
  position relative
  align-items center
  margin-bottom 20px
  width 100%

.priority-list
  display flex
  justify-content space-between
  margin-right 14px


.priority-item
  width: 33%;
  margin-right 8px

  &:last-child 
    margin-right 0

.priority-input
  display none

.priority-label
  display: flex;
  position: relative;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  cursor: pointer;
  transition: all .25s cubic-bezier(.02,.01,.47,1);
  
  &.active
    border 3px solid #ccc
  &:hover
    transform translateY(-2px)
    transition-delay 0s!important

.priority-label--grey
  background-color #ECECEC
.priority-label--yellow
  background-color #F5D76E
.priority-label--red
  background-color #f17c67

.task-input
  width 70%
  padding 8px 16px
  margin-right 14px
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
  padding 0 10px
  border-radius 8px


  &.grey
    background-color #ECECEC
  &.yellow
    background-color #F5D76E
  &.red
    background-color #f17c67

  &:last-child
    margin-bottom 0

.todo-item-header
  display flex
  align-items center

.todo-item-title
  padding 6px
  font-size 20px
  cursor pointer
  
  &.todo-item-title--edit
    border 1px solid #ccc
    cursor initial
    
  
  &.completed
    text-decoration line-through
    color #ccc

.todo-item-category
  font-size 16px
  padding-left 20px
  color #777


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

select
  -webkit-appearance: none;
  -moz-appearance: none;
  -ms-appearance: none;
  appearance: none;
  outline: 0;
  box-shadow: none;
  border: 0;
  background-image: none;

.category-list
  position: relative;
  display: block;
  width: 120px;
  height: 39px;
  line-height: 3;
  border 1px solid #ccc
  border-radius 8px
  overflow: hidden;
  transition: .25s all ease;

  &:after
    content: '\2193';
    position: absolute;
    color: #7e7e7e;
    top: -8px;
    right: 3px;
    padding: 0 10px;
    pointer-events: none;
    transition: all .25s cubic-bezier(.02,.01,.47,1);

  &:hover
    .category-select
      color #333
    &:after
      color: #333;
      top: -6px;


.category-select
  position absolute
  background-color #fff
  width 100%
  height 100%
  margin 0
  padding: 0 10px;  
  color: #7e7e7e;
  font-size 18px
  cursor: pointer;
  transition: all .25s cubic-bezier(.02,.01,.47,1);


</style>


