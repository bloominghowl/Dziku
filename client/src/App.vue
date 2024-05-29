<script setup>
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

// Lyfecycle hook yo fetch data on app mount
onMounted(() => {
  //Load user name from local storage or set to empty string
	name.value = localStorage.getItem('name') || '';
  // Load todos from local storage or set to empt array
	todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
 <h1>Hello world</h1>
</template>

<style>

</style>
