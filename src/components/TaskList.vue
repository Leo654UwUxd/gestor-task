<template>
  <div>
    <input
      v-model="newTask"
      placeholder="Escribe una tarea..."
      @keyup.enter="addTask"
    />
    <button @click="addTask">Agregar</button>

    <ul>
      <li v-for="(task, index) in tasks" :key="index">
        {{ task }}
        <button @click="removeTask(index)">X</button>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";

export default defineComponent({
  setup() {
    const newTask = ref<string>("");
    const tasks = ref<string[]>([]);

    const addTask = () => {
      if (newTask.value.trim()) {
        tasks.value.push(newTask.value);
        newTask.value = "";
      }
    };

    const removeTask = (index: number) => {
      tasks.value.splice(index, 1);
    };

    return { newTask, tasks, addTask, removeTask };
  }
});
</script>

<style>
input {
  margin-right: 10px;
}
button {
  margin-left: 5px;
}
</style>
