<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import "./radioStyle.css";
import { ref ,  onMounted , computed ,  watch } from "vue"

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const todo_acs = computed(()=> todos.value.sort((a,b)=> {
  return b.createdAt - a.createdAt;
}));


const AddTodo = ()=> {
  if (input_content.value.trim() == '' || input_category.value == null) {
    return console.log("there is no input");
  }

  todos.value.push({
    content : input_content.value,
    category : input_category.value,
    done : false,
    createdAt : new Date().getTime(),
  })

  input_content.value = '';
  input_category.value = null;
}

const removeTodo = (todo)=> {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos , (newVal) => {
  localStorage.setItem("todos" , JSON.stringify(newVal));
} ,{ deep : true})

watch(name , (newVal)=>{
  localStorage.setItem(name , newVal);
});

onMounted(()=> {
  const storedName = localStorage.getItem('name');
  if (!storedName) {
    const askName = prompt('Please enter your name:');
    if (askName) {
      name.value = askName;
      localStorage.setItem('name', askName);
    }
  } else {
    name.value = storedName;
  }
  todo_acs.value = JSON.parse(localStorage.getItem(todos)) || [];
})


</script>

<template>
  <main class="app p-6 bg-gray-100 min-h-screen">
    <section class="greeting mb-8">
      <h2 class="title text-3xl font-bold mb-4">
        What's up
        <input
          type="text"
          placeholder="Name Here"
          v-model="name"
          class="bg-gray-100"
        />
      </h2>
    </section>

    <section class="create_todo mb-8 p-6 bg-white rounded-lg shadow">
      <h2 class="text-2xl font-bold mb-4">CREATE A TODO</h2>
      <form @submit.prevent="AddTodo">
        <h4 class="text-lg font-semibold mb-2">What's on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g make a video"
          v-model="input_content"
          class="w-full mb-4 p-4 border border-gray-300 rounded-md focus:outline-none focus:border-blue-500"
        />

        <h4 class="text-lg font-semibold mb-2">Pick a category</h4>
        <div class="options mb-4 flex gap-4">
          <label class="flex items-center">
            <input
              type="radio"
              name="category"
              value="business"
              id="radio-1"
              v-model="input_category"
              class="radio mr-2 w-4 h-4 bg-blue-500 rounded-full inline-block"
            />
            <span class="bubble business w-4 h-4 bg-blue-500 rounded-full inline-block"></span>
            <div for="radio-1" class="ml-2">business</div>
          </label>
          <label class="flex items-center">
            <input
              type="radio"
              name="category"
              value="personal"
              id="radio-2"
              v-model="input_category"
              class="mr-2"
            />
            <span class="bubble personal w-4 h-4 bg-green-500 rounded-full inline-block"></span>
            <div for="radio-2" class="ml-2">personal</div>
          </label>
        </div>
        <input
          type="submit"
          value="Add Todo"
          class="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 cursor-pointer"
        />
      </form>
    </section>

    <section class="todo-list p-6 bg-white rounded-lg shadow">
      <h3 class="text-2xl font-bold mb-4">TODO LIST</h3>
      <div class="list">
        <div
          v-for="todo in todo_acs"
          :key="todo.id"
          :class="`todo_item p-4 mb-4 ${todo.category === 'business' ? 'bg-blue-100' : 'bg-green-100'} rounded-lg flex items-center justify-between ${todo.done ? 'opacity-50' : ''}`"
          :style="{ border: `1px solid ${todo.category === 'business' ? 'blue' : 'green'}` }"
        >
          <label class="flex items-center">
            <input
              type="checkbox"
              v-model="todo.done"
              class="mr-2"
            />
          </label>

          <div class="todo_content flex-grow">
            <input
              type="text"
              v-model="todo.content"
              class="w-full p-2 border border-gray-300 rounded-md focus:outline-none focus:border-blue-500"
            />
          </div>

          <div class="actions ml-4">
            <button
              class="delete px-4 py-2 bg-red-500 text-white rounded-md hover:bg-red-600"
              @click="removeTodo(todo)"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>


<style scoped>
.title {
  font-size: 2em;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 10px; /* Add space between text and input */
}

input[type="text"] {
  padding: 0px;
  font-size: 1em;
  border-radius: 5px;
  outline: none;
  transition: border-color 0.3s;
}

</style>