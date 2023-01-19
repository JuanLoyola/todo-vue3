<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">Bienvenido de vuelta, <input type="text" placeholder="Tu nombre aqui" v-model="name"></h2>
		</section>

		<section class="create-todo">

			<form @submit.prevent="addTodo">
				<h4>Que hay en tu lista?</h4>
				<input
					type="text"
					placeholder="Ej: Estudiar Node.js"
					v-model="input_content"
					required
				/>

				<h4>Elegir una categoria</h4>

				<div class="options">
					<label>
					<input
						type="radio"
						name="category"
						value="negocios"
						v-model="input_category"
					/>
					<span class="bubble business"></span>
					<div>Negocios</div>
					</label>

					<label>
					<input
						type="radio"
						name="category"
						value="personal"
						v-model="input_category"
					/>
					<span class="bubble personal"></span>
					<div>Personal</div>
					</label>
				</div>

				<div class="category-alert" v-if="required_alert">
					Elegir un nombre para el item y seleccionar una categoria
				</div>

				<br v-if="required_alert" />

				<input type="submit" value="Agregar a la lista" />
			</form>
		</section>

		<section class="todo-list">
			<h3 v-if="todos_asc.length">Tu lista pendiente:</h3>
			<div class="list">
				<div v-for="todo in todos_asc" :key="todo" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${todo.category}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" disabled v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Borrar</button>
					</div>
				</div>
			</div>
		</section>
  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

let required_alert = ref(false)

const todos_asc = computed (() => todos.value.sort((a,b) => {
	return b.createdAt - a.createdAt
}))

const addTodo = () => {
  const content = input_content.value.trim()
  const category = input_category.value

  if(!content) {
    return
  }

  if(!category) {
    resetInputTodo()
    required_alert.value = true
		
    setTimeout(() => {
      required_alert.value = false
    }, 3000)

    return
  }

  todos.value.push({
    content,
    category,
    done: false,
    createdAt: new Date().getTime()
  })

  resetInputTodo()
}

const resetInputTodo = () => {
  input_content.value = ""
  input_category.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})
</script>
