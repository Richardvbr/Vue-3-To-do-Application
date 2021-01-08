<template>
	<h1>Vue 3 | To-do Application</h1>
	<h4 class="time">{{ getDate() }}</h4>
	<!-- Prevent the page from reloading after submitting, run addTodo() after submit -->
	<form @submit.prevent="addTodo()">
		<label>New To-do item</label>
		<input class="input-field"
			v-model="newTodo"
			name="newTodo"
			autocomplete="off"
			placeholder="Add an item..."
		>
		<button class="button-submit">Add To Your List</button>
	</form>

	<h2>Your to-do list</h2>

	<!-- Generate li element for every item in todos array -->
	<ul>
		<li
			v-for="(todo, index) in todos"
			:key="index"
		>
			<span 
				:class="{ done: todo.done }"
				@click="doneTodo(todo)"
			>{{ todo.content }}</span>
			<i class="fas fa-times button-remove" @click="removeTodo(index)"></i>
		</li>
	</ul>
	
	<!-- Renders if todos array is empty -->
	<h4 v-if="todos.length === 0">No items in list.</h4>


	<!-- Renders when there are >= 1 items in list -->
	<p class="clear-list" v-if="todos.length >= 1" @click="clearTodos">Clear list</p>
</template>

<script>
	// Import 'ref' to enable reactive data
	import { ref } from 'vue';
	export default {
		name: 'App',
		setup () {
			const newTodo = ref('');

			// Create default/example list
			const defaultData = [{
				done: false,
				content: 'Example item | Click to toggle status'
			}]

			// Parse local storage
			const todosList = JSON.parse(localStorage.getItem('todos')) || defaultData;
			const todos = ref(todosList);

			// Adds new to-do item
			const addTodo = () => {
				if (newTodo.value) {
					todos.value.push({
						done: false,
						content: newTodo.value
					});
					newTodo.value = '';
				}
				
				// Updates array after each entry	
				saveData();
			}

			// Toggles done/not done status
			const doneTodo = (todo) => {
				todo.done = !todo.done
				saveData();
			}

			// Removes to-do item from array
			const removeTodo = (index) => {
				todos.value.splice(index, 1);
				saveData();
			}

			// Clears entire list
			const clearTodos = () => {
				todos.value.length = 0;
				saveData();
			}

			// Saves to-do items in the browser's local storage (array)
			const saveData = () => {
				const storageData = JSON.stringify(todos.value);
				localStorage.setItem('todos', storageData);
			}

			// Get current time
			const getDate = () => {
				const currentDate = ref(new Date());
				const dateOptions = ref({ weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
				let showDate = ref(currentDate.value.toLocaleDateString('en-us', dateOptions.value));
				return showDate.value;
			}

			return {
				todos,
				newTodo,
				addTodo,
				doneTodo,
				removeTodo,
				clearTodos,
				saveData,
				getDate
			}
		}
	}
</script>

<style lang="scss">
$border: 2px solid #777;
$backgroundColor: #27292d;
$textColor: white;
$primaryColor: #5ac07a;
$hoverColor: #61a879;
$secondTextColor: #1f2023;
$removeColor: #df6d2b;
$removeColorHover: #eb3616;

body {
	margin: 0;
	padding: 0;
	font-family: Roboto, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	background-color: $backgroundColor;
	color: $textColor;

	#app {
		max-width: 30vw;
		margin: 0 auto;
		padding: 20px;

		h1 {
			font-weight: bold;
			font-size: 28px;
			text-align: center;
			margin: 50px 0 0 0;
		}

		.time {
			margin: 0.25rem 0 3.25rem 0;
			font-weight: 500;
		}

		form {
			display: flex;
			flex-direction: column;
			width: 100%;

			label {
				font-size: 14px;
				font-weight: 600;
			}

			input,
			button {
				height: 3rem;
				box-shadow: none;
				outline: none;
				padding-left: 1rem;
				padding-right: 1rem;
				border-radius: 10px;
				font-size: 18px;
				margin-top: 0.35rem;
			}

			.input-field {
				background-color: transparent;
				border: $border;
				color: inherit;

				&:focus {
					border: 2px solid #7e8a7f;
				}
			}
		}

		.button-submit {
			cursor: pointer;
			background-color: $primaryColor;
			border: 1px solid $primaryColor;
			color: $secondTextColor;
			font-weight: bold;
			outline: none;
			border-radius: 10px;
			margin-top: 1rem;
			margin-bottom: 2.5rem;
			&:hover {
				background-color: $hoverColor;
			}
		}

		.button-remove {
			color: $removeColor;
			font-size: 23px;
			cursor: pointer;
			&:hover {
				color: $removeColorHover;
			}
		}

		.clear-list {
			cursor: pointer;
			display: inline-block;
			color: $removeColor;
			font-family: Roboto, sans-serif;
			font-weight: 500;
			font-size: 16px;
			margin: 0;
			&:hover {
				color: $removeColorHover;
			}
		}

		h2 {
			font-size: 22px;
			border-bottom: $border;
			padding-bottom: 1rem;
		}

		ul {
			padding: 10px;

			li {
				display: flex;
				justify-content: space-between;
				align-items: center;
				border: $border;
				padding: 0.75rem 2rem 0.75rem 2.75rem;
				border-radius: 10px;
				margin-bottom: 1rem;
				position: relative;
				span {
					cursor: pointer;
				}

				.done {
					text-decoration: line-through;
					color: #5ac07a;
					&::before {
						content: '\f00c';
						font-family: "Font Awesome 5 Free";
						font-weight: 900;
						display: inline-block;
						padding-left: -10px;
						position: absolute;
						left: 18px;
					}
				}
			}
		}

		h4 {
			text-align: center;
			opacity: 0.5;
			margin: 0;
		}
	}
}
</style>
