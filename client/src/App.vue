<script setup lang="ts">
// Import neccessary functions from Vue
import { ref, onMounted, computed, watch } from 'vue';

// state variables using refs
const todos = ref([]);  // Array to store todo items
const name = ref('');   // Optional ref to store user's name

// User input refs
const input_content = ref(''); // Text entered for a new todo
const input_category = ref(null); // Category for a new todo

// Computed property for sorted todos
const todos_asc = computed(() => todos.value.sort((a,b) =>{
  // Sort todos by the creation of dates in ascending order
	return a.createdAt - b.createdAt;
}));


// Watch effects for local storage persistence
watch(name, (newVal) => {
  // Update local storage with new user name
	localStorage.setItem('name', newVal);
});

watch(todos, (newVal) => {
  // Update local storage with the entire todos array as JSON
	localStorage.setItem('todos', JSON.stringify(newVal));
}, {
	deep: true
});

// Function to add new todo item
const addTodo = () => {
  // Validate user input 
	if (input_content.value.trim() === '' || input_category.value === null) {
		return;
	}

  // Create a new todo object with relevant properties
	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	});
  // Clear user input after adding a todo
  input_content.value = '';
  input_category.value = null;
};

// Function to remove a todo item
const removeTodo = (todo) => {
  // Filter out selected todo from the todos array
	todos.value = todos.value.filter((t) => t !== todo)
}

// Lyfecycle hook to fetch data on app mount
onMounted(() => {
  //Load user name from local storage or set to empty string
	name.value = localStorage.getItem('name') || '';
  // Load todos from local storage or set to empt array
	todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
 	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. learn vue.js"
					v-model="input_content" />
				
				<h4>Select a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Business</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category3" 
							value="School"
							v-model="input_category" />
						<span class="bubble school"></span>
						<div>School</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category4" 
							value="Errands"
							v-model="input_category" />
						<span class="bubble errands"></span>
						<div>Errands</div>
					</label>
				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>
	</main>
</template>
