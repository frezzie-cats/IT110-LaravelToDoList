<template>
  <div class="todo-container">
    <h3 class="title">To-do List</h3>

    <form @submit.prevent="addTask" class="task-form">
      <input
        v-model="newTask"
        type="text"
        placeholder="Add a new task"
        required
        class="task-input"
      />
      <button type="submit" class="add-task-button">+ Add Task</button>
    </form>

    <ul class="task-list">
      <li v-for="task in tasks" :key="task.id" class="task-item">
        <div class="task-details">
          <input
            type="checkbox"
            v-model="task.completed"
            @change="updateTask(task)"
            class="task-checkbox"
          />
          <span
            :class="{ completed: task.completed }"
            class="task-text"
          >
            <span v-if="!task.editing">{{ task.task }}</span>
            <input
              v-else
              v-model="task.task"
              type="text"
              class="task-input-edit"
              @blur="saveTask(task)"
              @keyup.enter="saveTask(task)"
            />
          </span>
        </div>
        <div class="task-actions">
          <button v-if="!task.editing" @click="editTask(task)" class="edit-button">
            <i class="fa-solid fa-pen"></i>
          </button>
          <button v-if="task.editing" @click="saveTask(task)" class="save-button">
            <i class="fa-solid fa-check"></i>
          </button>
          <button @click="deleteTask(task.id)" class="delete-button">
            <i class="fa-solid fa-trash"></i>
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      tasks: [],
      newTask: "",
    };
  },
  mounted() {
    this.loadTasksFromLocalStorage();
    this.fetchTasks();
  },
  methods: {
    loadTasksFromLocalStorage() {
      const storedTasks = localStorage.getItem("tasks");
      if (storedTasks) {
        this.tasks = JSON.parse(storedTasks);
      }
    },
    saveTasksToLocalStorage() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    fetchTasks() {
      axios
        .get("/api/tasks")
        .then((response) => {
          this.tasks = response.data;
          this.saveTasksToLocalStorage();
        })
        .catch((error) => {
          console.error("Error fetching tasks:", error);
        });
    },
    isValidTask(task) {
      const regex = /^[a-zA-Z0-9\s]+$/;
      return regex.test(task);
    },
    addTask() {
      if (!this.newTask.trim()) {
        alert("Task cannot be empty!");
        return;
      }

      if (!this.isValidTask(this.newTask)) {
        alert(
          "Task contains invalid characters! Only letters, numbers, and spaces are allowed."
        );
        return;
      }

      const newTask = {
        id: Date.now(),
        task: this.newTask,
        completed: false,
        editing: false,
      };

      this.tasks.push(newTask);
      this.saveTasksToLocalStorage();
      this.newTask = "";

      axios
        .post("/api/tasks", { task: newTask.task })
        .then((response) => {
          newTask.id = response.data.id;
          this.saveTasksToLocalStorage();
        })
        .catch((error) => {
          console.error("Error adding task to backend:", error);
        });
    },
    editTask(task) {
      task.editing = true;
    },
    saveTask(task) {
      if (!this.isValidTask(task.task)) {
        alert(
          "Task contains invalid characters! Only letters, numbers, and spaces are allowed."
        );
        return;
      }

      task.editing = false;
      this.saveTasksToLocalStorage();

      if (task.id) {
        axios
          .patch(`/api/tasks/${task.id}`, {
            task: task.task,
            completed: task.completed,
          })
          .catch((error) => {
            console.error("Error updating task:", error);
          });
      }
    },
    updateTask(task) {
      task.editing = false;
      this.saveTasksToLocalStorage();

      if (task.id) {
        axios
          .patch(`/api/tasks/${task.id}`, {
            task: task.task,
            completed: task.completed,
          })
          .catch((error) => {
            console.error("Error updating task:", error);
          });
      }
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter((task) => task.id !== id);
      this.saveTasksToLocalStorage();

      axios
        .delete(`/api/tasks/${id}`)
        .catch((error) => {
          console.error("Error deleting task:", error);
        });
    },
  },
};
</script>

<style scoped>
.todo-container {
  max-width: 600px;
  margin: 40px auto;
  padding: 25px;
  background: linear-gradient(to bottom, #f1f5f9, #e2e8f0); /* Grayish background */
  border-radius: 15px;
  box-shadow: 0px 8px 20px rgba(0, 0, 0, 0.15); /* Added subtle shadow */
}

.title {
  font-size: 2.5rem;
  font-weight: bold;
  color: #2c3e50;
  text-align: center;
  margin-bottom: 30px; /* Added margin to give space below */
}

.task-form {
  display: flex;
  gap: 1rem;
  margin-bottom: 25px; /* Increased space between input and tasks */
}

.task-input {
  flex-grow: 1;
  padding: 12px 15px;
  border: 2px solid #e2e8f0;
  border-radius: 10px;
  font-size: 1rem;
  background-color: #f9fafb;
  transition: border-color 0.3s ease, box-shadow 0.2s ease;
}

.task-input:focus {
  border-color: #4a90e2;
  outline: none;
  box-shadow: 0 0 5px rgba(74, 144, 226, 0.6); /* Added focus glow effect */
}

.add-task-button {
  padding: 12px 18px;
  font-size: 1rem;
  font-weight: bold;
  color: #ffffff;
  background: #2f0452;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}

.add-task-button:hover {
  background: #5d0ca0; /* Change color on hover */
}

.task-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  margin-bottom: 15px; /* Slightly more margin for separation */
  background: #ffffff;
  border-radius: 12px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.1); /* More subtle shadow */
}

.task-item:hover {
  background: #f7fafc; /* Change background color on hover */
}

.task-details {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.task-checkbox {
  width: 20px;
  height: 20px;
  accent-color: #4a90e2;
  cursor: pointer;
}

.task-text {
  font-size: 1.1rem;
  color: #34495e;
}

.task-text.completed {
  text-decoration: line-through;
  color: #95a5a6;
}

.task-actions {
  display: flex;
  gap: 1rem; /* Increased gap for better spacing */
}

.edit-button,
.save-button,
.delete-button {
  background: none;
  border: none;
  font-size: 1.3rem;
  color: #7f8c8d;
  cursor: pointer;
}

.edit-button:hover {
  color: #4a90e2; /* Change color on hover */
}

.save-button:hover {
  color: #27ae60; /* Change color on hover */
}

.delete-button:hover {
  color: #e74c3c; /* Change color on hover */
}
</style>
