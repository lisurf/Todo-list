<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '') {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})

	console.log(todos)

	input_content.value = ''
	input_category.value = null
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

const groupedTodos = computed(() => {
  const groups = {}

  todos.value.forEach((todo) => {
    if (!groups[todo.category]) {
      groups[todo.category] = []
    }
    groups[todo.category].push(todo)
  })
  return groups
})

const groupByCategory = () => {
	todos_asc.value = groupedTodos.value
	console.log(todos_asc)
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
				Hello, <input type="text" id="name" placeholder="Place your name here" v-model="name">
			</h2>
		</section>

		<section>
			<div style="display:flex;">
				<h3 style="padding-left:10px; padding-right: 341px;">What's on your todo list?</h3>
				<h3>Pick a category</h3>
			</div>
		</section>

		<section class="create-todo">
			<form id="new-todo-form" @submit.prevent="addTodo" style="display:flex;">
				<div style="padding-right: 30px;">
					<input
						type="text"
						name="content"
						id="content"
						placeholder="e.g. grocery list"
						v-model="input_content" />
				</div>
				<div class="options">
					<label>
						<input
							type="radio"
							name="category"
							id="category1"
							value="work"
							v-model="input_category" />
						<span class="bubble"></span>
						<div>Work</div>
					</label>

					<label>
						<input
							type="radio"
							name="category"
							id="category2"
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Important</div>
					</label>

					<label>
						<input
							type="radio"
							name="category"
							id="category3"
							value="house"
							v-model="input_category" />
						<span class="bubble house"></span>
						<div>House</div>
					</label>
				</div>

				<button @click="groupByCategory" >Group by Category</button>
				<input type="submit" value="Add" />
			</form>
		</section>

		<section class="todo-list">
    		<div class="list" id="todo-list">

        	<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
            	<label>
					<input type="checkbox" v-model="todo.done" />
					<span :class="`bubble ${
						todo.category == 'work'
							? 'work'
							: todo.category == 'personal'
							? 'personal'
							: todo.category == 'house'
							? 'house'
							: 'none'
					}`"></span>
            	</label>

            <div class="todo-content">
                <input type="text" v-model="todo.content" />
            </div>

            <div class="actions">
                <button class="delete" @click="removeTodo(todo)"> X </button>
            </div>
        	</div>

			<div v-if="flag" v-for="(group, category) in groupedTodos" :key="category">
				<div v-for="todo in group" :class="`todo-item ${todo.done && 'done'}`"></div>
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'work'
								? 'work'
								: todo.category == 'personal'
								? 'personal'
								: todo.category == 'house'
								? 'house'
								: 'none'
						}`"></span>
					</label>

				<div class="todo-content">
					<input type="text" v-model="todo.content" />
				</div>

				<div class="actions">
					<button class="delete" @click="removeTodo(todo)"> X </button>
				</div>
			</div>

    </div>
</section>

	</main>
</template>

