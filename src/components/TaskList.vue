<template>
  <div class="task-container">
    <!-- INPUT NUEVA TAREA -->
    <div class="add-row">
      <input
        v-model="newTask"
        @keyup.enter="addTask"
        placeholder="Escribe una tarea..."
        class="input"
      />
      <button class="btn" @click="addTask">Agregar</button>
    </div>

    <!-- LISTA -->
    <transition-group name="fade" tag="ul" class="task-list">
      <li
        v-for="task in tasks"
        :key="task.id"
        class="task-item"
      >
        <div class="left">
          <button class="icon-btn" @click="toggleDone(task)">
            <Icon :icon="task.done ? 'mdi:check-circle' : 'mdi:circle-outline'" width="22" />
          </button>

          <span :class="{ done: task.done }" class="task-text">
            {{ task.text }}
          </span>
        </div>

        <div class="right">
          <button class="icon-btn" @click="startEdit(task)">
            <Icon icon="mdi:pencil" width="20" />
          </button>

          <button class="icon-btn delete" @click="removeTask(task.id)">
            <Icon icon="mdi:trash-can" width="20" />
          </button>
        </div>
      </li>
    </transition-group>

    <p v-if="tasks.length === 0" class="empty">No hay tareas aún.</p>

    <!-- MODAL EDITAR -->
    <div v-if="editingTask" class="modal">
      <div class="modal-content">
        <h3>Editar tarea</h3>

        <input
          class="input"
          v-model="editingText"
          @keyup.enter="saveEdit"
        />

        <div class="modal-buttons">
          <button class="btn" @click="saveEdit">Guardar</button>
          <button class="btn cancel" @click="cancelEdit">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, watch } from "vue";
import { Icon } from "@iconify/vue";

type Task = {
  id: string;
  text: string;
  done: boolean;
};

export default defineComponent({
  name: "TaskList",
  components: { Icon },
  setup() {
    const STORAGE = "tareas_moderno_v1";

    const tasks = ref<Task[]>([]);
    const newTask = ref("");

    // MODAL EDITAR
    const editingTask = ref<Task | null>(null);
    const editingText = ref("");

    onMounted(() => {
      const saved = localStorage.getItem(STORAGE);
      if (saved) tasks.value = JSON.parse(saved);
    });

    watch(tasks, (val) => {
      localStorage.setItem(STORAGE, JSON.stringify(val));
    }, { deep: true });

    const generateId = () =>
      Date.now().toString(36) + Math.random().toString(36).slice(2, 6);

    function addTask() {
      if (!newTask.value.trim()) return;

      tasks.value.unshift({
        id: generateId(),
        text: newTask.value.trim(),
        done: false,
      });

      newTask.value = "";
    }

    function toggleDone(task: Task) {
      task.done = !task.done;
    }

    function removeTask(id: string) {
      tasks.value = tasks.value.filter((t) => t.id !== id);
    }

    function startEdit(task: Task) {
      editingTask.value = task;
      editingText.value = task.text;
    }

    function saveEdit() {
      if (!editingTask.value) return;
      if (!editingText.value.trim()) return;

      editingTask.value.text = editingText.value.trim();

      editingTask.value = null;
      editingText.value = "";
    }

    function cancelEdit() {
      editingTask.value = null;
      editingText.value = "";
    }

    return {
      tasks,
      newTask,
      addTask,
      toggleDone,
      removeTask,
      editingTask,
      editingText,
      startEdit,
      saveEdit,
      cancelEdit,
    };
  },
});
</script>

<style scoped>
.task-container {
  max-width: 760px;
  margin: auto;
  padding: 22px;

}

.add-row {
  display: flex;
  gap: 8px;
  margin-bottom: 14px;
}

.input {
  flex: 1;
  padding: 10px;
  border-radius: 8px;
  border: 1px solid #bbb;
  background: var(--card);
  color: var(--text);
}

.btn {
  padding: 10px 14px;
  background: var(--primary);
  color:  white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.task-list {
  list-style: none;
  padding: 0;
}

.task-item {
  display: flex;
  justify-content: space-between;
  background: var(--card);
  padding: 12px;
  border-radius: 10px;
  margin-bottom: 10px;
  align-items: center;
}

.task-text {
  margin-left: 8px;
}

.done {
  text-decoration: line-through;
  opacity: 0.6;
}

.icon-btn {
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 4px;
}

.delete {
  color: #d9534f;
}

.left {
  display: flex;
  align-items: center;
}

.right {
  display: flex;
  gap: 6px;
}

/* Mensaje vacío */
.empty {
  text-align: center;
  margin-top: 14px;
  opacity: 0.7;
}

/* Modal */
.modal {
  position: fixed;
  inset: 0;
  background: #0008;
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: var(--card);
  padding: 20px;
  border-radius: 12px;
  width: 320px;
}

.modal-buttons {
  margin-top: 12px;
  display: flex;
  justify-content: space-between;
}

.cancel {
  background: #888;
}

/* Animación lista */
.fade-enter-active,
.fade-leave-active {
  transition: 0.3s;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(-6px);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(6px);
}
</style>
