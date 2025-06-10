<template>
  <div class="app">
    <div class="logo-section">
      <img src="https://cdn-icons-png.flaticon.com/512/2910/2910768.png" alt="Logo" class="logo" />
      <h1>🗒️ To Do List</h1>
    </div>

    <form @submit.prevent="addTask" class="form">
      <input
        v-model="newTask"
        type="text"
        placeholder="Masukkan kegiatan anda..."
        class="input"
      />
      <button type="submit" class="btn-add">
        ➕ Tambah
      </button>
    </form>

    <div class="filter-buttons">
      <button @click="filter = 'all'" :class="{ active: filter === 'all' }">
        Semua
      </button>
      <button @click="filter = 'unfinished'" :class="{ active: filter === 'unfinished' }">
        Belum Selesai
      </button>
    </div>

    <transition-group name="task-list" tag="ul" class="task-list">
      <li
        v-for="(task, index) in filteredTasks"
        :key="task.id"
        :class="{ completed: task.completed }"
        class="task-item"
      >
        <div class="task-content">
          <input type="checkbox" v-model="task.completed" />
          <span class="check-icon" v-if="task.completed">✔️</span>
          <span class="task-text">{{ task.text }}</span>
        </div>
        <button @click="removeTask(index)" class="btn-cancel">
          <i class="bi bi-trash"></i>
        </button>
      </li>
    </transition-group>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: [],
      filter: 'all',
    };
  },
  computed: {
    filteredTasks() {
      if (this.filter === 'unfinished') {
        return this.tasks.filter(task => !task.completed);
      }
      return this.tasks;
    },
  },
  methods: {
    addTask() {
      const trimmed = this.newTask.trim();
      if (trimmed) {
        this.tasks.push({
          text: trimmed,
          completed: false,
          id: Date.now() + Math.random(),
        });
        this.newTask = '';
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
    },
  },
};
</script>
