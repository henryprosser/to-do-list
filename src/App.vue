<script setup>
// Imports
import { ref, onMounted, computed, watch } from "vue";

// Initialise variables
const todos = ref([]);
const input_content = ref("");
const input_priority = ref(1);
const sortBy = ref("priority"); // Default sort by priority

// Computed property to sort todos based on sortBy criteria
const sortedTodos = computed(() => {
  if (sortBy.value === "priority") {
    return [...todos.value].sort((a, b) => {
      if (a.priority === b.priority) {
        return b.createdAt - a.createdAt;
      }
      return b.priority - a.priority;
    });
  } else if (sortBy.value === "createdAt") {
    return [...todos.value].sort((a, b) => b.createdAt - a.createdAt);
  }
  return todos.value;
});

// Method to add a new todo
const addTodo = () => {
  if (
    input_content.value.trim() === "" ||
    input_priority.value < 1 ||
    input_priority.value > 10
  ) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    priority: parseInt(input_priority.value),
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = ""; // Clear input after adding
  input_priority.value = 1; // Reset priority to default
};

// Method to remove a todo
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

// Method to set sorting criteria
const setSortBy = (criteria) => {
  sortBy.value = criteria;
};

// Watcher to save todos to localStorage on change
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

// Load todos from localStorage on mount
onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <div>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
  </div>
  <main class="app">
    <section class="greeting">
      <h2 class="title">Welcome to your To-Do list!</h2>
    </section>

    <section class="create-todo">
      <form @submit.prevent="addTodo">
        <h4>Add a task</h4>
        <div class="add-task-group">
          <input
            type="text"
            placeholder="e.g. Buy lunch"
            v-model="input_content"
          />
        </div>
        <div class="form-group">
          <label for="priority"
            ><h4>
              Select a Priority <br />
              1 (least urgent) - 10 (most urgent)
            </h4>
          </label>
          <input
            type="number"
            id="priority"
            min="1"
            max="10"
            v-model="input_priority"
          />
        </div>
        <div class="form-group">
          <input type="submit" value="Add" />
        </div>
      </form>
    </section>

    <div class="todo-group">
      <section class="todo-list">
        <h3>To-Do List</h3>
        <div class="list">
          <div class="sort-dropdown">
            <label for="sort">Sort by:</label>
            <select id="sort" v-model="sortBy">
              <option value="priority">Priority</option>
              <option value="createdAt">Time Added</option>
            </select>
          </div>
          <div
            v-for="todo in sortedTodos"
            :key="todo.createdAt"
            :class="`todo-item ${todo.done ? 'done' : ''}`"
          >
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span>{{ todo.content }}</span>
              <span class="priority"> (Priority: {{ todo.priority }})</span>
            </label>
            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </div>
      </section>
    </div>
  </main>
</template>

<style scoped>
.logo {
  height: 6em;
  will-change: filter;
  transition: filter 300ms;
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}

.app {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

.greeting {
  text-align: center;
}

.create-todo {
  margin: 20px 0;
}

.add-task-group {
  margin-top: 0em;
}

.form-group {
  margin-top: 1em;
  margin-bottom: 1em;
}

.todo-group {
  margin-top: 5em;
}

.todo-list .list {
  margin-top: 20px;
}

.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

.todo-item.done span,
.todo-item.done .priority {
  text-decoration: line-through;
}

.actions {
  margin-left: 10px;
}

.sort-dropdown {
  margin-bottom: 10px;
}

.sort-dropdown label {
  margin-right: 10px;
}

.sort-dropdown select {
  padding: 5px;
  font-size: 16px;
}

button.delete {
  background-color: red;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
}
</style>
