<template>
  <h1>Gestor de Tareas (TypeScript)</h1>
  <TaskList />
</template>

<script lang="ts">
import { defineComponent } from "vue";
import TaskList from "./components/TaskList.vue";

export default defineComponent({
  components: { TaskList }
});
</script>

<style>
body {
  font-family: Arial, sans-serif;
  padding: 20px;
}
</style>
