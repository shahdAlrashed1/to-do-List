<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)
const selectedCategory = ref('all')


//فلترة للتصنيف
//ترتيب المصفوفة تصاعديا
const filteredTodos = computed(() => {
  let result = [...todos.value]

  if (selectedCategory.value !== 'all') {
    result = result.filter(todo => {
      return todo.category === selectedCategory.value
    })
  }

  return result.sort((a, b) => a.createdAt - b.createdAt)
})

// إضافة المهام إلى القائمة وافراغ حقل التعبئة بعد إضافة المهمة
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

// زر حذف المهمة
const removeTodo = (todo) => {
  const confirmed = confirm(`هل أنت متأكد من حذف: "${todo.content}"؟`)

  if (!confirmed) {
    return
  }

  todos.value = todos.value.filter(t => t !== todo)
}

// مراقبة قائمة المهام عند حدوث تغيرات وحفظها بعد التحديث
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

// عرض نتيجة التغيرات على الصفحة وحفظها بعد التحديث
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

        <!-- يمنع إعادة تحميل الصفحة -->
        <form @submit.prevent="addTodo">
          <h4>What's on your todo list?</h4>
          <input type="text" placeholder="Add a new task..." v-model="input_content">
          <div>{{ input_content }}</div>
          <h4>pick a category</h4>
          <div class="filters">
              <button type="button" @click="selectedCategory = 'all'" class="filterBtn">All</button>
              <button type="button" @click="selectedCategory = 'Business'" class="filterBtn bus">Business</button>
              <button type="button" @click="selectedCategory = 'Personal'" class="filterBtn per">Personal</button>
         </div>
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
        <div v-for="todo in filteredTodos" :key="todo.createdAt" :class="['todo-item', { done: todo.done }]">
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
