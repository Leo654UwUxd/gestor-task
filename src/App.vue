<template>
  <div :class="['app', darkMode && 'dark']">
    <header class="header">
      <h1>Gestor de Tareas</h1>

      <button class="icon-btn" @click="toggleDark">
        <Icon icon="mdi:weather-night" style="color: dark;" v-if="!darkMode" width="24" />
        <Icon icon="mdi:white-balance-sunny" style="color: white;" v-else width="24" />
      </button>
    </header>

    <main>
      <TaskList />
    </main>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";
import TaskList from "./components/TaskList.vue";
import { Icon } from "@iconify/vue";

export default defineComponent({
  name: "App",
  components: { TaskList, Icon },

  setup() {
    const darkMode = ref(false);

    // Cargar desde localStorage al abrir la app
    onMounted(() => {
      const savedMode = localStorage.getItem("darkMode");
      if (savedMode !== null) {
        darkMode.value = savedMode === "true";
      }
    });

    // Guardar cada vez que el usuario cambie el modo
    function toggleDark() {
      darkMode.value = !darkMode.value;
      localStorage.setItem("darkMode", String(darkMode.value));
    }

    return { darkMode, toggleDark };
  },
});
</script>

<style>
.app {
  --bg: #ffffff;
  --card: #c2b3b3;
  --text: #111;
  --primary: #28b0a8;

  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
  padding: 20px;
  transition: 0.3s;
}

h1 {
  margin-left: 245px;
  background-color: rgb(118, 236, 217);
  padding: 30px;
  border-radius: 50%;
}

/* MODO OSCURO */
.dark {
  --bg: #000000;
  --card: #fff3f3;
  --text: #000000d3;
  --primary: #40e0d0;
}

.header {
  max-width: 800px;
  margin: 0 auto 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.icon-btn {
  padding: 10px;
  border: none;
  background-color: rgb(108, 231, 190);
  border-radius: 35px;
  cursor: pointer;
}
</style>
