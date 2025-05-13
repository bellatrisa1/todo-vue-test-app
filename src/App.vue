<template>
  <div class="todo-app">
    <!-- Header -->
    <header class="header">
      <h1>To do list</h1>
      <div class="controls">
        <button @click="showModal = true" class="add-button">
          <i class="fas fa-plus"></i>
        </button>
      </div>
    </header>

    <!-- Search -->
    <div class="search-sort">
      <i class="fas fa-search search-icon"></i>
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Поиск Имени, статуса или даты"
        class="search-input"
      />
      <div class="sort-controls">
        <span>Сортировать по:</span>
        <select v-model="sortBy" class="sort-select">
          <option value="date">Дата</option>
          <option value="status">Статус</option>
        </select>
      </div>
    </div>

    <!-- Task -->
    <table class="task-table">
      <thead>
        <tr>
          <th>Описание</th>
          <th>Статус</th>
          <th>Дата</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(task, index) in filteredTasks"
          :key="index"
          :class="{ 'completed-task': task.completed }"
        >
          <td>
            <input
              type="checkbox"
              :checked="task.completed"
              @change="toggleTaskCompletion(task)"
            />
            {{ task.description }}
          </td>
          <td>
            <span
              :class="{
                'status-completed': task.status === 'Выполнено',
                'status-in-progress': task.status === 'В работе',
              }"
            >
              {{ task.status }}
            </span>
          </td>
          <td>{{ task.date }}</td>
        </tr>
      </tbody>
    </table>

    <!-- Modal -->
    <transition name="modal">
      <div class="modal-overlay" v-if="showModal">
        <div class="modal">
          <h2>Создать новую задачу</h2>
          <form @submit.prevent="addTask">
            <div class="input-group">
              <label for="description-input" class="input-label">Описане</label>
              <input
                id="description-input"
                type="text"
                v-model="newTaskDescription"
                placeholder="Введите описание"
                class="modal-input"
              />
            </div>
            <div class="modal-footer">
              <button type="submit" class="modal-button">Создать</button>
            </div>
            <button @click="closeModal" class="modal-close">
              <i class="fas fa-times icon-times"></i>
            </button>
          </form>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [
        {
          id: 1,
          description: "Размещение новостей на сайте",
          status: "Выполнено",
          date: "22.04.2022",
          completed: true,
        },
        {
          id: 2,
          description: "Внедрить Wi-Fi для читателей",
          status: "В работе",
          date: "25.03.2022",
          completed: false,
        },
        {
          id: 3,
          description: "Отредактировать раздел 'Доступная среда'",
          status: "Выполнено",
          date: "15.03.2022",
          completed: true,
        },
        {
          id: 4,
          description: "Презентация 'Информационные технологии'",
          status: "В работе",
          date: "15.03.2022",
          completed: false,
        },
        {
          id: 5,
          description: "Счетчики — внедрить дизайн",
          status: "В работе",
          date: "09.03.2022",
          completed: false,
        },
        {
          id: 6,
          description: "Сверстать новый layout",
          status: "В работе",
          date: "07.03.2022",
          completed: false,
        },
        {
          id: 7,
          description: "Скролл в новостях",
          status: "Выполнено",
          date: "01.03.2022",
          completed: true,
        },
        {
          id: 8,
          description: "Форма сброса пароля",
          status: "В работе",
          date: "25.02.2022",
          completed: false,
        },
        {
          id: 9,
          description: "Внедрение модуля Chat",
          status: "Выполнено",
          date: "20.02.2022",
          completed: true,
        },
      ],
      newTaskDescription: "",
      searchQuery: "",
      sortBy: "date",
      showModal: false,
    };
  },
  computed: {
    filteredTasks() {
      const query = this.searchQuery.toLowerCase();
      let filtered = this.tasks.filter((task) =>
        Object.values(task).some(
          (value) =>
            typeof value === "string" && value.toLowerCase().includes(query)
        )
      );

      // Сортировка
      if (this.sortBy === "date") {
        filtered.sort((a, b) => new Date(b.date) - new Date(a.date));
      } else if (this.sortBy === "status") {
        filtered.sort((a, b) => a.status.localeCompare(b.status));
      }

      return filtered;
    },
  },
  methods: {
    toggleTaskCompletion(task) {
      task.completed = !task.completed;
      task.status = task.completed ? "Выполнено" : "В работе";
      this.saveTasksToLocalStorage();
    },
    addTask() {
      if (!this.newTaskDescription.trim()) return;

      const newTask = {
        id: Date.now(),
        description: this.newTaskDescription,
        status: "В работе",
        date: new Date().toLocaleDateString("ru-RU"),
        completed: false,
      };

      this.tasks.push(newTask);
      this.newTaskDescription = "";
      this.closeModal();
      this.saveTasksToLocalStorage();
    },
    closeModal() {
      this.showModal = false;
    },
    saveTasksToLocalStorage() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    loadTasksFromLocalStorage() {
      const storedTasks = localStorage.getItem("tasks");
      if (storedTasks) {
        this.tasks = JSON.parse(storedTasks);
      }
    },
  },
  mounted() {
    this.loadTasksFromLocalStorage();
  },
};
</script>

<style scoped>
.todo-app {
  font-family: "Inter", sans-serif;
  max-width: 1000px;
  margin: 40px auto;
  padding: 30px;
  background-color: #fff;
  border-radius: 10px;
}

/* Header */
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

.header h1 {
  font-size: 22px;
  font-weight: 600;
  margin: 0;
}

.add-button {
  background-color: #eef0ff;
  border: none;
  color: #4b6eff;
  padding: 10px;
  border-radius: 50%;
  font-size: 20px;
  cursor: pointer;
  transition: 0.2s ease;
  width: 40px;
  height: 40px;
}

.add-button:hover {
  background-color: #d8dfff;
}

/* Search */

.search-sort {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}

.search-input {
  padding: 10px 15px 10px 35px;
  width: 320px;
  margin-right: 33rem;
  border: 0.1px solid #ffffff;
  border-radius: 8px;
  background-color: #ffffff;
  background-repeat: no-repeat;
  background-position: 10px center;
  font-size: 14px;
  color: #c4c4c4;
}

.search-icon {
  color: #59bba6;
}

.sort-controls {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
}

.sort-select {
  padding: 6px 10px;
  border-radius: 6px;
  border: 1px solid #ccc;
  font-size: 14px;
}

/* Task */

.task-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
}

.task-table th {
  position: relative;
  text-align: left;
  padding: 12px;
  background-color: #f4f6fa;
  font-weight: 500;
  color: #16191d;
  border-bottom: 1px solid #f2f2f2;
}

.task-table th {
  padding-left: 4rem;
  background-color: #ffffff;
}

.task-table th:first-child::before {
  content: "";
  position: absolute;
  top: calc(50% - 16px);
  left: 3rem;
  width: 1px;
  height: 32px;
  background: #c4c4c4;
}

.task-table th:not(:last-child)::after {
  content: "";
  position: absolute;
  top: calc(50% - 16px);
  right: 0;
  width: 1px;
  height: 32px;
  background: #c4c4c4;
}

.task-table th:nth-child(2) {
  padding-left: 16px;
}

.task-table th:nth-child(3) {
  padding-left: 16px;
}

.task-table td {
  padding: 12px;
  border-bottom: 1px solid #eee;
  vertical-align: middle;
}

.task-table tr:hover {
  background-color: #f8f9fc;
}

/* checkbox */

.checkbox-cell {
  text-align: center;
  width: 40px;
}

input[type="checkbox"] {
  appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 1px solid #16191d;
  background: #ffffff;
  cursor: pointer;
  transition: 0.2s ease;
  position: relative;
}

input[type="checkbox"]:checked {
  border: 1px solid #134ec1;
  box-shadow: 0px 4px 4px rgba(19, 78, 193, 0.15);
  background: #ffffff;
}

input[type="checkbox"]:checked::after {
  content: "";
  position: absolute;
  top: 4px;
  left: 6px;
  width: 4px;
  height: 8px;
  border: solid #134ec1;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
}

.input-label {
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 4px;
  color: #333;
  text-transform: none;
}

/* Описание задачи */

.completed-task {
  font-weight: 500;
  color: #111;
}

/* Статус */
.status-completed {
  color: #124ec1;
  padding: 4px 8px;
  border-radius: 12px;
  font-weight: 500;
  font-size: 13px;
  display: inline-block;
}

.status-in-progress {
  color: #f29b33;
  padding: 4px 8px;
  border-radius: 12px;
  font-weight: 500;
  font-size: 13px;
  display: inline-block;
}

/* Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(255, 255, 255, 0.4);
  backdrop-filter: blur(2px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background-color: white;
  padding: 40px;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  width: 280px;
  position: relative;
}

.modal h2 {
  margin-top: 0;
  font-size: 18px;
  margin-bottom: 15px;
}

.modal-input {
  width: 100%;
  padding: 10px 5px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
}

.modal-footer {
  display: flex;
  justify-content: center;
  padding-top: 20px;
}

.modal-button {
  background-color: #f0f5fe;
  color: #314b99;
  border: none;
  padding: 10px 18px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
}

.modal-close {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
}

.icon-times {
  background-color: #314b99; /* Синий фон */
  color: #fff;
  padding: 2px;
  border-radius: 3px;
  font-size: 16px;
  align-items: center;
  justify-content: center;
  display: inline-flex;
  width: 22px;
  height: 22px;
}
</style>
