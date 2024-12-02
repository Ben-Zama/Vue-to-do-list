<template>
  <div class="modal-backdrop" v-if="visible">
    <div class="modal">
      <h3>{{ title }}</h3>
      <p>{{ message }}</p>
      <div class="actions">
        <button @click="cancel">Cancel</button>
        <button @click="confirm">Delete</button>
      </div>
    </div>
  </div>
</template>
  
  <script setup>
import { ref } from "vue";

const props = defineProps({
  visible: Boolean,
  title: String,
  message: String,
});

const emit = defineEmits(["confirm", "cancel"]);

const confirm = () => {
  emit("confirm");
};

const cancel = () => {
  emit("cancel");
};
</script>
  