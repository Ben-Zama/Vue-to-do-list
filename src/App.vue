<template>
  <div class="main">
    <!-- Greetings -->
    <section class="greeting">
      <h2>Welcome back,</h2>
      <input type="text" placeholder="Input name here" v-model="name" />
    </section>
    <!-- Create -->
    <section class="create">
      <h3>Create a task</h3>
      <form @submit.prevent="createTodo">
        <input
          type="text"
          placeholder="Add a task e.g. go shopping"
          v-model="input_content"
        />
        <div class="options">
          <p>Pick an option</p>
          <div>
            <label class="important-div">
              <input
                type="radio"
                name="category"
                value="important"
                v-model="input_category"
              />
              <span class="bubble important"></span>
              <p>Important</p>
            </label>
            <label class="optional-div">
              <input
                type="radio"
                name="category"
                value="optional"
                v-model="input_category"
              />
              <span class="bubble optional"></span>
              <p>Optional</p>
            </label>
          </div>
        </div>
        <button type="submit">Add to list</button>
      </form>
    </section>
    <!-- List -->
    <section v-if="todos_asc.length > 0" class="list-container">
      <h3>List</h3>
      <div class="list">
        <div
          v-for="(item, index) in todos_asc"
          :key="index"
          :class="['todo-item', { done: item.done }]"
        >
          <label>
            <input type="checkbox" v-model="item.done" />
            <span
              :class="`bubble ${
                item.category == 'important' ? 'important' : 'optional'
              }`"
            ></span>
          </label>
          <div>
            <input type="text" v-model="item.content" />
          </div>
          <button class="delete" @click="openModal(index, 'delete')">
            <i class="bi bi-trash3-fill"></i>
          </button>
        </div>
      </div>
      <button class="clear-all" @click="openModal(null, 'clearAll')">
        Clear All
      </button>
    </section>
    <!-- Modal -->
    <Modal
      :visible="modalVisible"
      :title="modalTitle"
      :message="modalMessage"
      @confirm="handleConfirm"
      @cancel="closeModal"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, watch, computed } from "vue";
import Modal from "./components/modal.vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const modalVisible = ref(false);
const modalTitle = ref("");
const modalMessage = ref("");
const modalAction = ref(null);
const modalTarget = ref(null);

const todos_asc = computed(() =>
  [...todos.value].sort((a, b) => b.createdAt - a.createdAt)
);

const createTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime,
  });
  input_content.value = "";
  input_category.value = null;
};

const openModal = (target, action) => {
  modalTarget.value = target;
  modalAction.value = action;

  if (action === "delete") {
    modalTitle.value = "Delete Task";
    modalMessage.value = "Are you sure you want to delete this task?";
  } else if (action === "clearAll") {
    modalTitle.value = "Clear All Tasks";
    modalMessage.value = "Are you sure you want to clear all tasks?";
  }
  modalVisible.value = true;
};

const handleConfirm = () => {
  if (modalAction.value === "delete" && modalTarget.value !== null) {
    deleteList(modalTarget.value);
  } else if (modalAction.value === "clearAll") {
    todos.value = [];
  }
  closeModal();
};

const closeModal = () => {
  modalVisible.value = false;
  modalTarget.value = null;
  modalAction.value = null;
};

const deleteList = (index) => {
  todos.value.splice(index, 1);
};

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>
