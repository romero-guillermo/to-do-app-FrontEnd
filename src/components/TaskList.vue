<template>
  <div>
      <h1>Lista de Tareas</h1>
      <ul>
          <li v-for="task in tasks" :key="task.id">
              <input
                  type="checkbox"
                  v-model="task.completed"
                  @change="updateTask(task.completed)"
              />
              {{ task.title }}
              <button @click="deleteTask(task.id)">Eliminar</button>
          </li>
      </ul>
      <form @submit.prevent="addTask">
          <input type="text" v-model="newTaskTitle" placeholder="Nueva tarea" />
          <button type="submit">Agregar</button>
      </form>
  </div>
</template>

<script>
import api from "../components/api.js";

export default {
  data() {
      return {
          tasks: [],
          newTaskTitle: "",
      };
  },
  methods: {
      async fetchTasks() {
          const response = await api.get("/tareas");
          this.tasks = response.data;
      },
      async addTask() {
          if (this.newTaskTitle.trim()) {
              const response = await api.post("/tareas", {
                  title: this.newTaskTitle,
                  completed: false,
              });
              this.tasks.push(response.data);
              this.newTaskTitle = "";
          }
      },
      async updateTask(task) {
          await api.put("/tareas", task);
      },
      async deleteTask(id) {
          await api.delete("/tareas", { data: { id } });
          this.tasks = this.tasks.filter((task) => task.id !== id);
      },
  },
  mounted() {
      this.fetchTasks();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  padding: 0;
}
li {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
button {
  margin-left: 10px;
}
</style>