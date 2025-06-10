<template>
  <div class="app">
    <div class="logo-section">
      <i class="bi bi-clipboard-check logo"></i>
      <h1><i class="bi bi-card-checklist"></i> Smart To-Do List</h1>
    </div>

    <form @submit.prevent="addTask" class="form">
      <input
        v-model="newTask"
        type="text"
        placeholder="Masukkan kegiatan baru..."
        class="input"
      />
      <button type="submit" class="btn-add">
        <i class="bi bi-plus-circle"></i> Tambah
      </button>
    </form>

    <div class="task-count" v-if="tasks.length > 0">
      Total: {{ tasks.length }} tugas | Selesai: {{ completedCount }} | Tersisa: {{ remainingCount }}
    </div>

    <div class="filter-buttons">
      <button @click="filter = 'all'" :class="{ active: filter === 'all' }">
        <i class="bi bi-list-ul"></i> Semua ({{ tasks.length }})
      </button>
      <button @click="filter = 'unfinished'" :class="{ active: filter === 'unfinished' }">
        <i class="bi bi-clock"></i> Belum Selesai ({{ remainingCount }})
      </button>
      <button @click="filter = 'completed'" :class="{ active: filter === 'completed' }">
        <i class="bi bi-check-circle"></i> Selesai ({{ completedCount }})
      </button>
    </div>

    <transition-group name="task-list" tag="ul" class="task-list" v-if="filteredTasks.length > 0">
      <li
        v-for="task in filteredTasks"
        :key="task.id"
        :class="{ completed: task.completed }"
        class="task-item"
      >
        <label class="task-content custom-checkbox-container">
          <input type="checkbox" v-model="task.completed" />
          <span class="checkmark"></span>
          <span class="task-text">{{ task.text }}</span>
        </label>
        <button @click="removeTask(task.id)" class="btn-cancel" title="Hapus tugas">
          <i class="bi bi-trash"></i>
        </button>
      </li>
    </transition-group>

    <div v-else class="empty-state">
      <i class="bi bi-clipboard-check"></i>
      <h3>{{ getEmptyMessage() }}</h3>
      <p>{{ getEmptySubMessage() }}</p>
    </div>

    <div class="footer-actions" v-if="tasks.length > 0">
      <button @click="clearCompleted" class="btn-clear-completed" v-if="completedCount > 0">
        <i class="bi bi-x-circle-fill"></i> Hapus Tugas Selesai
      </button>
    </div>
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
      switch (this.filter) {
        case 'unfinished':
          return this.tasks.filter((task) => !task.completed);
        case 'completed':
          return this.tasks.filter((task) => task.completed);
        default:
          return this.tasks;
      }
    },
    completedCount() {
      return this.tasks.filter((task) => task.completed).length;
    },
    remainingCount() {
      return this.tasks.filter((task) => !task.completed).length;
    },
  },
  methods: {
    addTask() {
      const trimmed = this.newTask.trim();
      if (trimmed) {
        this.tasks.unshift({
          text: trimmed,
          completed: false,
          id: Date.now() + Math.random(),
          createdAt: new Date().toLocaleString('id-ID'),
        });
        this.newTask = '';
        this.saveTasks();
        setTimeout(() => {
          document.querySelector('.input').focus();
        }, 100);
      }
    },
    removeTask(taskId) {
      if (confirm('Yakin ingin menghapus tugas ini?')) {
        this.tasks = this.tasks.filter((task) => task.id !== taskId);
        this.saveTasks();
      }
    },
    clearCompleted() {
      if (confirm('Yakin ingin menghapus semua tugas yang selesai?')) {
        this.tasks = this.tasks.filter((task) => !task.completed);
        this.saveTasks();
      }
    },
    getEmptyMessage() {
      switch (this.filter) {
        case 'unfinished':
          return 'Semua tugas sudah selesai! 🎉';
        case 'completed':
          return 'Belum ada tugas yang selesai';
        default:
          return 'Belum ada tugas';
      }
    },
    getEmptySubMessage() {
      switch (this.filter) {
        case 'unfinished':
          return 'Selamat! Anda telah menyelesaikan semua tugas.';
        case 'completed':
          return 'Selesaikan beberapa tugas untuk melihatnya disini.';
        default:
          return 'Mulai tambahkan tugas pertama Anda!';
      }
    },
    saveTasks() {
      localStorage.setItem('todo_tasks', JSON.stringify(this.tasks));
    },
    loadTasks() {
      const savedTasks = localStorage.getItem('todo_tasks');
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },
  },
  mounted() {
    this.loadTasks();
    if (this.tasks.length === 0) {
      setTimeout(() => {
        this.tasks = [
          { id: 1, text: 'Belajar Vue.js', completed: false, createdAt: new Date().toLocaleString('id-ID') },
          { id: 2, text: 'Membuat aplikasi to-do', completed: true, createdAt: new Date().toLocaleString('id-ID') },
          { id: 3, text: 'Mendesain UI yang menarik', completed: false, createdAt: new Date().toLocaleString('id-ID') }
        ];
        this.saveTasks();
      }, 500);
    }
    document.querySelector('.input').focus();
  }
};
</script>