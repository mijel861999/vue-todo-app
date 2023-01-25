<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref("");
const todo_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === "") {
    console.log("Sus parámetros están vacíos");
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">What's up</h2>
      <input
        class="input-name"
        type="text"
        placeholder="Name here"
        v-model="name"
      />
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          class="todo-input-content"
          placeholder="e.g make a video"
          v-model="input_content"
        />

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input class="add-todo-button" type="submit" value="Add todo!" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div
          v-for="todo in todo_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
.title input {
  width: 150px;
  border: none;
  font-size: 20px;
  font-weight: bold;
}

.options {
  display: flex;
}

.options label {
  border: 1px solid grey;
  margin: 10px 7px;
  width: 200px;
  height: 70px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 7px;
}

.add-todo-button {
  background: black;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 10px;
  font-size: 17px;
  cursor: pointer;
}

.list {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}

.todo-item {
  border: 0.5px solid grey;
  width: 300px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-radius: 6px;
  margin: 5px 0px;
}

.todo-item input {
  border: none;
}

.todo-item .actions {
  background-color: tomato;
  justify-self: flex-end;
}

.todo-item .actions button {
  background: tomato;
}

.todo-input-content {
  border: 0.5px solid grey;
  padding: 10px 10px;
  border-radius: 7px;
}

.done {
  opacity: 0.5;
}

.input-name {
  border: none;
  text-align: center;
  font-size: 30px;
  font-weight: bold;
}

textarea:focus,
input:focus {
  outline: none;
}
</style>
