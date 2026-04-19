<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)

const todo_asc = computed(() => todos.value.sort((a, b) =>  {
  return a.createdAt - b.createdAt
}))

const addTodo = () => {
  if(input_content.value.trim() === '' || input_content.value === null){
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false, 
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
})


onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        what's up, <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>
      
      <section class="create-todo">
        <h3>CREATE A TODO</h3>
        <form @submit.prevent="addTodo">
          <h4>What's on your todo list?</h4>
          <input type="text" placeholder="your todo" v-model="input_content">
          <div>{{ input_content }}</div>
          <h4>pick a category</h4>
          <div class="options">
            <label>
              <input type="radio" name="category" value="Business" v-model="input_category">
              <span class="bubble business"></span>
              <div>Business</div>
            </label>
            <label>
              <input type="radio" name="category" value="Personal" v-model="input_category">
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>
          <input type="submit" value="Add todo">
        </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todo_asc" :key="todo.createdAt" :class="['todo-item', { done: todo.done }]">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="action">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
