<template>

  <div class="page-container app">

    <div class="sidebar-container">
      <div class="widget-container">
        <div class="plus-button">
          <button class="plus-button-primary" >
            <span role="img" aria-label="plus" class="anticon anticon-plus plus" @click="toggleColors" v-if="!displayColors">
                <svg viewBox="64 64 896 896" focusable="false" data-icon="plus" width="1em" 
                height="1em" fill="currentColor" aria-hidden="true">
                <path d="M482 152h60q8 0 8 8v704q0 8-8 8h-60q-8 0-8-8V160q0-8 8-8z"></path>
                <path d="M176 474h672q8 0 8 8v60q0 8-8 8H176q-8 0-8-8v-60q0-8 8-8z"></path>
                </svg>
            </span>
            <span role="img" aria-label="close" class="anticon anticon-close close" @click="toggleColors" v-if="displayColors" >
                <svg viewBox="64 64 896 896" focusable="false" data-icon="close" width="1em" 
                height="1em" fill="currentColor" aria-hidden="true">
                <path d="M563.8 512l262.5-312.9c4.4-5.2.7-13.1-6.1-13.1h-79.8c-4.7 0-9.2 2.1-12.3 5.7L511.6 449.8 295.1 191.7c-3-3.6-7.5-5.7-12.3-5.7H203c-6.8 0-10.5 7.9-6.1 13.1L459.4 512 196.9 824.9A7.95 7.95 0 00203 838h79.8c4.7 0 9.2-2.1 12.3-5.7l216.5-258.1 216.5 258.1c3 3.6 7.5 5.7 12.3 5.7h79.8c6.8 0 10.5-7.9 6.1-13.1L563.8 512z"></path>
                </svg>
            </span>
          
          </button>

          <div class="color-picker" v-if="displayColors">
            <div class="item">
              <button class="circle lightOrange" @click="toggleAdd"></button>
            </div>
            <div class="item">
              <button class="circle orange" @click="toggleAdd"></button>
            </div>
            <div class="item">
              <button class="circle yellowGreen" @click="toggleAdd"></button>
            </div>
            <div class="item">
              <button class="circle purple" @click="toggleAdd"></button>
            </div>
            <div class="item">
              <button class="circle lightGreen" @click="toggleAdd"></button>
            </div>
            <div class="item">
              <button class="circle lightBlue" @click="toggleAdd"></button>
            </div>
          </div>       
        </div>
        <div class="toggle-button">
          <!-- <button></button>
          <span>Light</span> -->
        </div>
      </div>
    </div>

    <div class="todo-container">
      <div class="greeting">
        <h1 class="title">What's up, <input class="user-name" type="text" placeholder="Name here" v-model="name"></h1>
      </div>

      <div class="create-todo">
        <h2>NOTES</h2>
        <!-- <span class="noSaved" v-if="!displayForm">No saved notes yet.</span>
        <span class="noSaved" v-if="displayForm">Add more notes as you like.</span> -->
        <form @submit.prevent="addTodo">
          <h4>What's on your todo list?</h4>
          
          <div class="notes-container">
            <textarea class="input-content" type="text" placeholder="e.g. make a video" v-model="input_content"></textarea>
          </div>

          <h4>Pick a category</h4>
          <div class="options">
            <label>
              <input type="radio" name="category" value="business" v-model="input_category"/>
              <span class="bubble business"></span>
              <div>Business</div>
            </label>
          </div>
          <div class="options">
            <label>
              <input type="radio" name="category" value="personal" v-model="input_category"/>
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>
          <input type="submit" value="Add todo"/>
        </form> 
      </div>

      <div class="todo-list">
        <div class="list">
          <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
            <!-- <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </label> -->
            <div class="todo-content">
              <textarea class="note-content" type="text" v-model="todo.content"></textarea>
              <div class="actions">
                <button class="delete" @click="removeTodo(todo)">
                  <span role="img" aria-label="delete" class="anticon anticon-delete">
                    <svg viewBox="64 64 896 896" focusable="false" data-icon="delete" width="1em" 
                    height="1em" fill="currentColor" aria-hidden="true">
                    <path d="M360 184h-8c4.4 0 8-3.6 8-8v8h304v-8c0 4.4 3.6 8 8 8h-8v72h72v-80c0-35.3-28.7-64-64-64H352c-35.3 0-64 28.7-64 64v80h72v-72zm504 72H160c-17.7 0-32 14.3-32 32v32c0 4.4 3.6 8 8 8h60.4l24.7 523c1.6 34.1 29.8 61 63.9 61h454c34.2 0 62.3-26.8 63.9-61l24.7-523H888c4.4 0 8-3.6 8-8v-32c0-17.7-14.3-32-32-32zM731.3 840H292.7l-24.2-512h487l-24.2 512z">
                    </path>
                    </svg>
                  </span>
                </button>
              </div>
            </div>         
          </div>
        </div>
      </div>

    </div>

  </div>
</template>









<script setup>
import { ref, onMounted, computed, watch } from 'vue'

// declaration of variables
const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)
const displayForm = ref(false)
const displayColors = ref(false)
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt // returns an ascending list by date
}))

// display color picker
const toggleColors = () => {
  displayColors.value = !displayColors.value
}
// trial and error
const toggleAdd = () => {
  displayForm.value = true
}

// adding todo
const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null){
    return 
  }
  
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_category.value = null
  input_content.value = ''
}

// remove todo
const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>


<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: whitesmoke;
}

.page-container {
  min-height: 100vh;
  width: 100%;
  height: 100vh;
  display: flex;
}

.page-container .sidebar-container {
  background: rgb(231, 228, 231);
  transition: none 0s ease 0s;
  flex: 0 0 80px;
  max-width: 80px;
  min-width: 80px;
  width: 80px;
  display: flex;
  justify-content: center;
  padding: 3rem 0;
}

.page-container .sidebar-container .widget-container {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.page-container .sidebar-container .widget-container .plus-button-primary {
  background: transparent;
  border: none;
  color: white;
  font-size: 18px;
  line-height: 0;
  height: 60px;
  width: 60px;
  transition: none 0s ease 0s;
  background: rgb(0, 0, 0);
  cursor: pointer;
  border-radius: 50%;
  min-width: 40px;
}

.page-container .sidebar-container .widget-container .plus-button .color-picker {
  margin-top: 3rem;
  display: flex;
  flex-direction: column;
  gap: 24px;
}
.page-container .sidebar-container .widget-container .plus-button .color-picker  .item .circle{
  cursor: pointer;
    border-radius: 50%;
    height: 25px;
    width: 25px;
    border: none;
}

.lightOrange {
  background: rgb(248, 208, 136);
}
.orange {
  background: rgb(242, 169, 134);
}
.yellowGreen {
  background: rgb(233, 240, 163);
}
.purple {
  background: rgb(225, 186, 255);
}
.lightGreen {
  background: rgb(189, 241, 164);
}
.lightBlue {
  background: rgb(130, 208, 242);
}


.page-container .todo-container {
  width: 100%;
  padding: 30px 24px;
  text-align: left;
}

.page-container .todo-container .greeting h1.title {
  font-size: 38px;
  font-weight: 600;
  line-height: 1.23;
}

.page-container .todo-container .greeting .user-name{
  font-size: 38px;
  font-weight: 600;
  line-height: 1.23;
  border: none;
  color: #2c3e50;
  width: 100%;
  max-width: 50%;
  background: transparent;
}

.page-container .todo-container .create-todo h2 {
  margin-bottom: 5px;
}

.page-container .todo-container .create-todo span.noSaved {
  color: rgba(0,0,0,.45);
  display: block;
}

.page-container .todo-container .create-todo form .notes-container {
  display: flex;
  gap: 30px;
  flex-wrap: wrap;
  margin-top: 2rem;
}

.page-container .todo-container .create-todo form .notes-container .input-content {
  border-radius: 20px;
  width: 100%;
  max-width: 800px;
  min-width: auto;
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border: none;
}

.todo-list .list {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.todo-list .list .todo-item {
  background: rgb(242, 169, 134);
  border-radius: 20px;
  width: 100%;
  max-width: 25%;
  min-height: 260px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.todo-list .list .todo-content {
  
}

.todo-list .list .todo-content .actions {
  text-align: end;
}

.todo-list .list .todo-content .note-content {
  background: transparent;
  border: none;
  resize: none;
  height: 100%;
  margin-bottom: 10px;
}

input, textarea:focus {
  outline: none;
  border: none;
}

button.delete {
  background: #000;
  border: #000;
  cursor: pointer;
  border-radius: 50%;
  height:30px;
  width: 30px;
  color: #FFF;
}

</style>



