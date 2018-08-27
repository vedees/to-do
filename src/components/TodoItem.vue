<template lang="pug">
  .todo-item( :class="{ grey: todo.priority == 'grey', yellow: todo.priority == 'yellow', red: todo.priority == 'red' }" )
    .todo-item-header
      input(type="checkbox" v-model="completed" @click.once="scoreUpdate" @change="finishEditTodoItem")
      .todo-item-title(v-if="!editing" :class="{ completed: completed }" @click="editTodoItem") {{ title }}
      input.todo-item-title.todo-item-title--edit(type="text" v-else="editing" v-model="title" @blur="finishEditTodoItem" @keyup.enter="finishEditTodoItem" @keyup.esc="cancelEditTodoItem")
      .todo-item-category {{ category }}
    .todo-item-buttons
      span.delete-button(@click="deleteTodoItem(index)")
      span.edit-button(v-if="editing == false" @click="editTodoItem") Edit
      span.edit-button.edit-button--save(v-if="editing == true" @click="finishEditTodoItem") Save
</template>

<script>
export default {
  name: 'todo-item',
  props: {
    todo: {
      tupe: Object,
      required: true,
    },
    index: {
      type: Number,
      required: true, 
    },
    checked: {
      type: Boolean,
      required: true, 
    }
  },
  data(){
    return {
      'id': this.todo.id,
      'title': this.todo.title,
      'titleBeforeEdit': '',
      'category': this.todo.category,
      'priority': this.todo.priority,
      'completed': this.todo.completed,
      'editing': this.todo.editing,
    }
  },
  watch:{
    checked(){
      if(this.checked) {
        this.completed = true
      }else{
        this.completed = this.todo.completed
      }
    }
  },
  methods: {
    deleteTodoItem(index) {
      this.$emit('deleteTodoItem', index)
    },
    editTodoItem(){
      this.titleBeforeEdit = this.title;
      this.editing = true;
    },
    cancelEditTodoItem(){
      this.title = this.titleBeforeEdit;
      this.editing = false;
    },
    finishEditTodoItem(){
      if(this.title.trim() == ''){
        this.title = this.titleBeforeEdit;
      }
      this.editing = false;

      this.$emit('doneFinishedEditTodoItem', {
        'index': this.index,
        'todo': {
          'id': this.id,
          'title': this.title,
          'completed': this.completed,
          'editing': this.editing,
          'priority': this.priority,
          'category': this.category,
        }
      })
    },

  }
}
</script>

<style lamg="stylus">

</style>
