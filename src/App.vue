<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})

	// Reset input fields
	input_content.value = ''
	input_category.value = null
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

const editTodo = (todo) => {
	todo.editable = true
	todo.originalContent = todo.content
}

const saveTodo = (todo) => {
	todo.editable = false
	delete todo.originalContent
}

const cancelEdit = (todo) => {
	todo.content = todo.originalContent
	todo.editable = false
	delete todo.originalContent
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				HAII!!! <input type="text" id="name" placeholder="Najwa Lutfia Nisya" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>CRUD</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>Apa yang ingin anda list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. make a video"
					v-model="input_content" />
				
				<h4>Pilih Kategori</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Bisnis</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Pribadi</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`" :key="todo.createdAt">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input 
							type="text" 
							v-if="todo.editable"
							v-model="todo.content" />
						<span v-else>{{ todo.content }}</span>
					</div>
				
					<div class="actions">
						<button v-if="!todo.editable" class="edit" @click="editTodo(todo)">Edit</button>
						<button v-if="todo.editable" class="save" @click="saveTodo(todo)">Save</button>
						<button v-if="todo.editable" class="cancel" @click="cancelEdit(todo)">Cancel</button>
						<button class="delete" @click="removeTodo(todo)">Hapus</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>

<style scoped>
.todo-item {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 10px;
	border: 1px solid #ddd;
	border-radius: 5px;
	margin-bottom: 10px;
	background-color: #fff;
}
.todo-item.done .todo-content span {
	text-decoration: line-through;
	color: #aaa;
}
.todo-content input[type="text"] {
	width: 100%;
	padding: 5px;
	border: 1px solid #ccc;
	border-radius: 5px;
}
.actions button {
	margin-left: 5px;
	padding: 5px 10px;
	border: none;
	border-radius: 5px;
	cursor: pointer;
}
.actions .edit {
	background-color: #4caf50;
	color: #fff;
}
.actions .save {
	background-color: #2196f3;
	color: #fff;
}
.actions .cancel {
	background-color: #f44336;
	color: #fff;
}
.actions .delete {
	background-color: #e91e63;
	color: #fff;
}
</style>


