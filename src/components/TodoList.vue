<template lang="pug">
section
	.container
		input(type="text" placeholder="Type your task here" v-model="newTodoTitle" @keyup.enter="newTodoItem").task-input
		.todo-list(v-for="todo in todos" :key="todo.id" class="todo-item")
			span {{ todo.title }}
</template>

<script>
export default {
	name: 'todo-list',
	data() {
		return{
			newTodoTitle: '',
			newTodoId: 3,
			todos: [
				{
					'id': 1,
					'title': 'Learn Vue.js',
					'completed': false
				},
				{
					'id': 2,
					'title': 'Finish App',
					'completed': false
				}
			]
		}
	},
	methods: {
		newTodoItem: function(){
			this.todos.push({
				id: this.newTodoId,
				title: this.newTodoTitle,
				completed: false
			})
			this.newTodoTitle = '';
			this.newTodoId += 1;

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

</style>


