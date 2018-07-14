<template lang="pug">
.wrapper
  section
    .container
      h1 To-Do List
			
  <todo-list></todo-list>

</template>

<script>
import TodoList from './components/TodoList'

export default {
  name: 'App',
  components: {
    TodoList
  }
}
</script>

<style lang="stylus">
@import "./assets/stylus/main.styl"


</style>
