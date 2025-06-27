<template>
  <div class="todo-app">
    <h1>Todo List dengan Axios & JSON Server (CRUD lengkap)</h1>

    <form @submit.prevent="addTodo">
      <input v-model="newTodo" placeholder="Tambah todo baru..." required />
      <button type="submit" :disabled="loading">Tambah</button>
    </form>

    <p v-if="loading">Memuat data...</p>
    <p v-if="error" style="color: yellow;">{{ error }}</p>

    <ul v-if="todos.length">
      <li v-for="todo in todos" :key="todo.id">
        <input
          type="checkbox"
          :checked="todo.completed"
          @change="toggleCompleted(todo)"
        />

        <template v-if="editId !== todo.id">
          <span :style="{ textDecoration: todo.completed ? 'line-through' : 'none' }">
            {{ todo.title }}
          </span>
          <button @click="startEdit(todo)">Edit</button>
        </template>

        <template v-else>
          <input v-model="editTitle" />
          <button @click="updateTodo(todo)">Simpan</button>
          <button @click="cancelEdit">Batal</button>
        </template>

        <button @click="deleteTodo(todo.id)">Hapus</button>
      </li>
    </ul>

    <p v-else>Tidak ada todo.</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const todos = ref([])
const newTodo = ref('')
const loading = ref(false)
const error = ref('')
const editId = ref(null)
const editTitle = ref('')
const API_URL = 'http://localhost:3000/todos'

const loadTodos = async () => {
  loading.value = true
  error.value = ''
  try {
    const response = await axios.get(API_URL)
    todos.value = response.data
  } catch (err) {
    error.value = 'Gagal memuat data todo.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

const addTodo = async () => {
  if (!newTodo.value.trim()) return
  loading.value = true
  error.value = ''
  try {
    const newData = {
      title: newTodo.value,
      completed: false,
    }
    const response = await axios.post(API_URL, newData)
    todos.value.push(response.data)
    newTodo.value = ''
  } catch (err) {
    error.value = 'Gagal menambah todo.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

const toggleCompleted = async (todo) => {
  loading.value = true
  error.value = ''
  try {
    const response = await axios.put(`${API_URL}/${todo.id}`, {
      ...todo,
      completed: !todo.completed,
    })
    const idx = todos.value.findIndex((t) => t.id === todo.id)
    if (idx !== -1) todos.value[idx] = response.data
  } catch (err) {
    error.value = 'Gagal memperbarui todo.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

const deleteTodo = async (id) => {
  loading.value = true
  error.value = ''
  try {
    await axios.delete(`${API_URL}/${id}`)
    todos.value = todos.value.filter((todo) => todo.id !== id)
  } catch (err) {
    error.value = 'Gagal menghapus todo.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

const startEdit = (todo) => {
  editId.value = todo.id
  editTitle.value = todo.title
}

const cancelEdit = () => {
  editId.value = null
  editTitle.value = ''
}

const updateTodo = async (todo) => {
  if (!editTitle.value.trim()) return
  loading.value = true
  error.value = ''
  try {
    const response = await axios.put(`${API_URL}/${todo.id}`, {
      ...todo,
      title: editTitle.value,
    })
    const idx = todos.value.findIndex((t) => t.id === todo.id)
    if (idx !== -1) todos.value[idx] = response.data
    editId.value = null
    editTitle.value = ''
  } catch (err) {
    error.value = 'Gagal memperbarui todo.'
    console.error(err)
  } finally {
    loading.value = false
  }
}

onMounted(loadTodos)
</script>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 0 auto;
  background: #B22222;
  border-radius: 16px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.08);
  padding: 30px;
  text-align: center;
  font-family: 'Segoe UI', sans-serif;
  color: #fff;
}

h1 {
  font-size: 26px;
  color: #fff;
  margin-bottom: 20px;
}

form {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin-bottom: 24px;
}

form input[type="text"] {
  flex: 1 1 250px;
  padding: 10px 14px;
  border: 1px solid #d1d5db;
  border-radius: 999px;
  font-size: 16px;
  transition: border-color 0.2s;
  background: #FFA07A;
  color: #000;
}

form input[type="text"]:focus {
  border-color: #ff0000;
  outline: none;
  background: #FFA07A;
}

button {
  padding: 10px 16px;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.2s;
  border: none;
}

button[type="submit"] {
  background-color: #ff0000;
  color: white;
}

button[type="submit"]:hover {
  background-color: #cc0000;
}

button:disabled {
  background-color: #ffcccc;
  cursor: not-allowed;
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

li {
  display: flex;
  align-items: center;
  background: #fff;
  color: #111;
  border-radius: 10px;
  padding: 10px 14px;
  margin-bottom: 12px;
  transition: background-color 0.2s;
}

li:hover {
  background: #f3f4f6;
}

input[type="checkbox"] {
  appearance: none;
  width: 20px;
  height: 20px;
  border: 2px solid #ff0000;
  border-radius: 6px;
  position: relative;
  cursor: pointer;
  background: white;
  transition: background-color 0.2s, border-color 0.2s;
  margin-right: 12px;
}

input[type="checkbox"]:checked {
  background-color: #ff0000;
  border-color: #cc0000;
}

input[type="checkbox"]::after {
  content: "";
  position: absolute;
  top: 3px;
  left: 6px;
  width: 4px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
  display: none;
}

input[type="checkbox"]:checked::after {
  display: block;
}

li span {
  flex: 1;
  font-size: 16px;
  text-align: left;
  color: #111;
}

li input[type="text"] {
  flex: 1;
  padding: 6px 10px;
  font-size: 16px;
  border-radius: 8px;
}

li button {
  margin-left: 8px;
  background-color: #ff0000;
  color: white;
  padding: 6px 12px;
  border-radius: 6px;
}

li button:hover {
  background-color: #cc0000;
}

p {
  font-size: 14px;
  color: #f8f8f8;
}
</style>