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

				<ul v-auto-animate>
					<div class="category-alert" v-if="required_alert">
						Elegir un nombre para el ITEM y seleccionar una CATEGORIA
					</div>

					<br v-if="required_alert" />


					<input type="submit" value="Agregar a la lista" />
				</ul>

			</form>
		</section>

		<section class="todo-list">
			<h4 v-if="todos_asc.length">Tu lista pendiente:</h4>
			<ul v-auto-animate class="list">
				<li v-for="todo in todos_asc" :key="todo" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${todo.category}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" disabled v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">
							<svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
								<path stroke-linecap="round" stroke-linejoin="round" d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0" />
							</svg>
						</button>
					</div>
				</li>
			</ul>
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
